---
layout: post
author: Shane Skiles
title: ToChunkedList()
tags: [c#, enumerable, iasyncenumerable, list]
---

I recently came across this fun little snippet I wrote a while back. I don't remember
the exact case where I used it, but I thought it was worth sharing. I think I needed
to process a huge `IEnumerable<T>` and wanted to distribute it as simply as possible.
This is a simple extension method on `IEnumerable<T>` that returns it as 
multiple `List<T>` objects from a `IAsyncEnumerable<List<T>>`. 
The chunk size is configurable, and the method is cancellable.

I present to you the `ToChunkedList` extension method:
```csharp
public static async IAsyncEnumerable<List<T>> ToChunkedList<T>(
    this IEnumerable<T> enumerable,
    int chunkSize = 100,
    [EnumeratorCancellation] CancellationToken token = default)
{
    var itemList = new List<T>(chunkSize);
    var enumerator = enumerable.GetEnumerator();
    for (var count = 1; enumerator.MoveNext(); count++)
    {
        token.ThrowIfCancellationRequested();
        var item = enumerator.Current;
        itemList.Add(item);
        if (count % chunkSize == 0)
        {
            yield return itemList;
            itemList.Clear();
            await Task.Yield();
        }
    }

    if (itemList.Count > 0)
    {
        yield return itemList;
    }
}
```
I think my original idea for the `IEnumerable<T>` distribution would have spent more
time on the distribution than the actual processing. This seemed to be a quick and easy
way to break it into chunks get the job done. It's also lowers the memory pressure as
opposed to a single huge `ToList`. Here's a simple example of how to use it:
```csharp
await foreach (var chunk in Enumerable.Range(1, 42).ToChunkedList(10))
{
    Console.WriteLine($"Chunk: {string.Join(", ", chunk)}");
}
// Output:
// Chunk: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
// Chunk: 11, 12, 13, 14, 15, 16, 17, 18, 19, 20
// Chunk: 21, 22, 23, 24, 25, 26, 27, 28, 29, 30
// Chunk: 31, 32, 33, 34, 35, 36, 37, 38, 39, 40
// Chunk: 41, 42
```
You may never need this, but I thought it was a fun little snippet to share.
It may give you some ideas for something else. 