---
layout: post
author: Shane Skiles
title: The Continuous Queue
tags: [iasyncenumerable, semaphoreslim, concurrentqueue, c#]
---

I recently ran across an interesting problem. I had a service that needed serialized
messages sent to it. The messages were being sent asynchonously, but the service could
only process one message at a time. The messages could also come in from multiple threads.
My first thought was to just put a lock around the service call, but that would possibly
leave an unknown number of threads waiting around for the lock.

Then I thought about using a queue. That would work, just fire and forget the messages.
We would need a `ConcurrentQueue` to handle the multiple threads. Then just dequeue the
messages and process them one at a time. But, that would involve looping over the 
queue repeatedly, waiting for messages. I hate putting in artificial delays.

Then I thought about using a `SemaphoreSlim` to lock the processing of items until 
an item is queued. At first I thought limit the semaphore maxCount to 1, but the I 
realized that only one thread should be processing the queue at a time. 
So, we can set the maxCount as unlimited and have the queueing of items 
increment the semaphore count. Dequeuing items would decrement the semaphore count.

The more I thought about it, the more I liked the idea. There were actually a 
couple of places this might be useful. With the `SemaphoreSlim` taking care of the 
asynchronous locking delay we could convert our loop iteration of an `IEnumerable<T>`
into a `IAsyncEnumerable<T>`. This would allow us to use the `await foreach` to
hide what's acually going on.

Here's what we end up with for this idea...

```csharp
public sealed class FbContinuousQueue<T> : IDisposable
{
    private readonly ConcurrentQueue<T> _queue = new();
    private readonly SemaphoreSlim _semaphore = new(0);

    public void Enqueue(T item)
    {
        _queue.Enqueue(item);
        _semaphore.Release();
    }

    public async IAsyncEnumerable<T> Items(
        [EnumeratorCancellation] CancellationToken cancellationToken = default)
    {
        while (!cancellationToken.IsCancellationRequested)
        {
            await _semaphore.WaitAsync(cancellationToken);
            if (_queue.TryDequeue(out var item))
            {
                yield return item;
            }
        }
    }

    public void Dispose()
    {
        _semaphore.Dispose();
        GC.SuppressFinalize(this);
    }
}
```
Using it would look something like this...
```csharp
// Populate the queue with items
var continuousQueue = new FbContinuousQueue<T>();
continuousQueue.Enqueue((T)TestItem);

...

// Process the items elsewhere
await foreach (T item in continuousQueue.Items())
{
    Process(item);
}
```

Add in a `CancellationToken` to the `Items` method (don't forget the 
`[EnumeratorCancellation]` attribute), to break out of the loop if needed.
No need for an error handler in there since the `SemaphoreSlim` will probably 
throw the cancellation exception unless it exits the loop which is exactly what 
we want. The foreach loop should handle the exception since it will be handling the 
cancellation token anyway. 

Throw in a simple `Dispose` method to clean up the semaphore, mark the class `sealed` for 
now until we decide if we want to do anything else to it. There are definitely improvements 
that could be made. Adding in a `Stop`/`Start`/`Pause` to control flow through the queue
would probably be a good idea. Possibly a mechanism to ensure the queue is empty before
disposing of the queue. But, for now, this works.