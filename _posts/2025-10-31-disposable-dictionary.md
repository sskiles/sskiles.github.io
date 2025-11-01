---
layout: post
author: Shane Skiles
title: "Disposable Dictionary"
tags: [C#, IDisposable, IAsyncDisposable, Dictionary]
---

I was trying to think of something interesting to write about and a disposable 
dictionary came to mind. I was working on a project once, I think it was a 
document storage system or some such, and I needed a way to manage a collection of
disposables. I originally thought a standard dictionary would be fine, but then I
realized I needed to make sure I wasn't holding on to disposed objects.
That led me to think I needed to dispose of objects when they were removed from the
dictionary. What if an object was replaced in the dictionary? And so on.

This all led me to create a `DisposableDictionary` class that helps manage disposable 
objects. The dictionary itself implements `IDisposable` and ensures that all disposable
values are properly disposed of when they are replaced, removed, or the dictionary 
itself is disposed.

The majority of the IDictionary<TKey, TValue> implementation is straightforward
and simply delegates to the underlying _dictionary. Such methods include:

`public int Count => _dictionary.Count;`

The indexer, `Remove()`, and `Clear()` methods are the ones that require special handling.

```csharp
public class DisposableDictionary<TKey, TValue> : IDictionary<TKey, TValue>, IDisposable, IAsyncDisposable
    where TValue : IDisposable
    where TKey : notnull
{
    private readonly Dictionary<TKey, TValue> _dictionary = [];
    private bool _disposed;

    public ICollection<TKey> Keys => _dictionary.Keys;

    public ICollection<TValue> Values => _dictionary.Values;

    public int Count => _dictionary.Count;

    public bool IsReadOnly => false;

    public TValue this[TKey key]
    {
        get => _dictionary[key];
        set
        {
            if (_dictionary.TryGetValue(key, out var oldValue))
            {
                oldValue.Dispose();
            }

            _dictionary[key] = value;
        }
    }

    public void Add(TKey key, TValue value) => _dictionary.Add(key, value);

    public void Add(KeyValuePair<TKey, TValue> item)
        => _dictionary.Add(item.Key, item.Value);

    public void Clear()
    {
        foreach (var value in _dictionary.Values)
        {
            value.Dispose();
        }

        _dictionary.Clear();
    }

    public bool Contains(KeyValuePair<TKey, TValue> item)
        => ((ICollection<KeyValuePair<TKey, TValue>>)_dictionary).Contains(item);

    public bool ContainsKey(TKey key) => _dictionary.ContainsKey(key);

    public void CopyTo(KeyValuePair<TKey, TValue>[] array, int arrayIndex)
        => ((ICollection<KeyValuePair<TKey, TValue>>)_dictionary).CopyTo(array, arrayIndex);

    public IEnumerator<KeyValuePair<TKey, TValue>> GetEnumerator()
        => _dictionary.GetEnumerator();

    public bool Remove(TKey key)
    {
        if (_dictionary.TryGetValue(key, out var value))
        {
            value.Dispose();
            return _dictionary.Remove(key);
        }

        return false;
    }

    public bool Remove(KeyValuePair<TKey, TValue> item)
    {
        if (_dictionary.TryGetValue(item.Key, out TValue? value) && EqualityComparer<TValue>.Default.Equals(value, item.Value))
        {
            _dictionary.Remove(item.Key);
            value.Dispose();
            return true;
        }

        return false;
    }

    public bool TryGetValue(TKey key, [MaybeNullWhen(false)] out TValue value)
        => _dictionary.TryGetValue(key, out value);

    IEnumerator IEnumerable.GetEnumerator() => _dictionary.GetEnumerator();

    public void Dispose()
    {
        Dispose(disposing: true);
        GC.SuppressFinalize(this);
    }
    
    public async ValueTask DisposeAsync()
    {
        await DisposeAsyncCore().ConfigureAwait(false);
        GC.SuppressFinalize(this);
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                foreach (var value in _dictionary.Values)
                {
                    value.Dispose();
                }

                _dictionary.Clear();
            }

            _disposed = true;
        }
    }

    protected virtual async ValueTask DisposeAsyncCore()
    {
        if (!_disposed)
        {
            foreach (var value in _dictionary.Values)
            {
                if (value is IAsyncDisposable asyncDisposable)
                {
                    await asyncDisposable.DisposeAsync().ConfigureAwait(false);
                }
                else
                {
                    value.Dispose();
                }
            }

            _dictionary.Clear();
            _disposed = true;
        }
    }
}
```

The dispose methods are pretty straightforward.
They could be as simple as just calling `Clear()`, but this is a bit more explicit.
Use the disposable part of the `DisposableDictionary` just like you would any 
other disposable object - wrap it in a `using`, `await using`, 
or manually call `Dispose()`/`DisposeAsync()`.
Use the dictionary as you normally would use a dictionary to add, remove, and access items.

```csharp
await using var dictionary = new DisposableDictionary<string, IDisposable>();

dictionary.Add("item1", new SomeDisposableResource());
dictionary.Add("item2", new SomeDisposableResource());

if (dictionary.TryGetValue("item1", out var resource))
{
    // Use the resource
}

var item2 = dictionary["item2"];
```

Disposing the `DisposableDictionary` disposes of all its values 
without any extra effort.
It's not an overly complex concept or implementation, 
but it's one of those things that solves that specific 
problem you don't know you have until you need it.

