---
layout: post
author: Shane Skiles
title: DuplicateStreamAsync()
tags: [c#, stream, copyto, copytoasync, duplicate]
---

I was trying to organize some older projects that dealt heavily with streams 
and I came across a nice little extension method I wrote a while back. 
It duplicates a stream and lets you specify the position of the source and 
destination streams. I can't count the number of times I've copied a stream or
breathed wrong on a stream and ended up with some weird bug because I didn't
reset the position of a stream. This little extension method takes care of that.

```csharp
public static async Task<MemoryStream> DuplicateStreamAsync(
    this Stream source,
    StreamPosition sourcePosition = StreamPosition.Start,
    StreamPosition destinationPosition = StreamPosition.Start,
    CancellationToken cancellationToken = default)
{
    _ = source ?? throw new ArgumentNullException(nameof(source));
    if (!source.CanSeek && !source.CanRead)
        throw new ObjectDisposedException(
            nameof(source), "Cannot access a closed Stream.");

    var position = source.Position;
    var destination = new MemoryStream();
    try
    {
        source.Position = 0;
        await source.CopyToAsync(
            destination: destination,
            cancellationToken: cancellationToken).ConfigureAwait(false);

        if (destinationPosition == StreamPosition.Start)
            destination.Position = 0;
        else if (destinationPosition == StreamPosition.Current)
            destination.Position = position;

        return destination;
    }
    catch
    {
        destination.Dispose();
        throw;
    }
    finally
    {
        if (sourcePosition == StreamPosition.Start)
            source.Position = 0;
        else if (sourcePosition == StreamPosition.Current)
            source.Position = position;
        else source.Position = source.Length;
    }
}

public enum StreamPosition
{
    Start,
    Current,
    End
}
```

Granted, copying a stream isn't rocket science, but I had one project where I was
doing a lot of stream manipulation... and this was the result. By default, it 
resets the source and destination streams to the start position. You can also specify
the current position or the end of the stream for either stream.

Actually, add this to it:
```csharp
public static async Task<MemoryStream> DuplicateStreamAsync(
    this Stream source,
    CancellationToken cancellationToken) =>
    await source.DuplicateStreamAsync(
        sourcePosition: StreamPosition.Start,
        destinationPosition: StreamPosition.Start,
        cancellationToken: cancellationToken).ConfigureAwait(false);
```
This overload is a little more convenient if you don't need to specify the
positions, but do want to use a cancellation token without naming it.
Anyway, it was a nice little helper for me at the time and I thought I'd share it.
May all your `Position`s be `0`.