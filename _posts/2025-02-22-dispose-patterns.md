---
layout: post
author: Shane Skiles
tags: [c#, dispose, sync, async]
---

I always want to refer back to something when implementing a full sync/async 
disposal pattern in C#. Well, here it is. This implementation has a `disposables` 
list to help keep track of everything you want to dispose of. 

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

/// <summary>
/// Represents example sync/async disposal pattern.
/// </summary>
public class DisposableCore : IDisposable, IAsyncDisposable
{
    private static readonly List<IDisposable> _disposables = [];
    private bool _disposed;

    /// <inheritdoc/>
    public void Dispose()
    {
        Dispose(disposing: true);
        GC.SuppressFinalize(this);
    }

    /// <inheritdoc/>
    public async ValueTask DisposeAsync()
    {
        await DisposeAsyncCore().ConfigureAwait(false);
        Dispose(disposing: false);
        GC.SuppressFinalize(this);
    }

    /// <summary>
    /// Disposes the object and its resources.
    /// </summary>
    /// <param name="disposing">A boolean value indicating whether the method is called from the Dispose method.</param>
    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                foreach (var disposable in _disposables)
                {
                    disposable.Dispose();
                }
            }

            /* 
                Dispose unmanaged resources here. 
                This block is executed regardless of the value of 'disposing'.
                It is called from Dispose(), DisposeAsync(), and the finalizer (if implemented).
            */

            _disposed = true;
        }
    }

    /// <summary>
    /// Disposes the object asynchronously.
    /// </summary>
    /// <returns>A <see cref="ValueTask"/> representing the asynchronous operation.</returns>
    protected virtual async ValueTask DisposeAsyncCore()
    {
        foreach (var disposable in _disposables)
        {
            if (disposable is IAsyncDisposable asyncDisposable)
            {
                await asyncDisposable.DisposeAsync().ConfigureAwait(false);
            }
            else
            {
                disposable.Dispose();
            }
        }

        await Task.CompletedTask.ConfigureAwait(false);
    }
    
#if USE_FINALIZER
    // If a finalizer is needed, it should call Dispose(false) 
    // to release unmanaged resources.
    /// <summary>
    /// Finalizes an instance of the <see cref="DisposableCore"/> class.
    /// </summary>
    ~DisposableCore() => Dispose(false);
#endif
}
```