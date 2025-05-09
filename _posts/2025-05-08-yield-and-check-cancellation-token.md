---
layout: post
author: Shane Skiles
title: Yield and Check Cancellation Token.
tags: [c#, csharp, yield, cancellation token]
---

I was reading through some code recently and ran across an asynchronous method that
immediately called `cancellationToken.ThrowIfCancellationRequested()`. Nothing wrong
with that. It's a good practice to check for cancellation at the start of a method. But, 
I rarely actually do unless I'm about to start a long-running operation. I almost always
call `await Task.Yield()` at the beginning of my async methods. This allows the calling
method to continue on its context and the new method to run on its own context (a 
polite way to force a method to run async).

That made me think it would be nice to have a method that would check for cancellation 
and yield at the same time. Basically, just put this in a single function: 

```csharp
public static async Task YieldCancelCheck(CancellationToken cancellationToken)
{
    await Task.Yield();
    cancellationToken.ThrowIfCancellationRequested();
}
```

That would work, but it would be nice to know where the cancellation happened. The 
function would always be at the top of the call stack, so it would be nice to have the
caller's information at the top of the stack. I thought about trying to use 
`ExceptionDispatchInfo`, but that would need to be in the caller, defeating the 
purpose. Passing the caller's information to the function would seem to be the best
solution. This way, the function can throw an `OperationCanceledException` with the caller's
information in the message.

```csharp
public static async Task YieldCancelCheck(
    CancellationToken cancellationToken,
    [CallerMemberName] string memberName = "",
    [CallerFilePath] string filePath = "",
    [CallerLineNumber] int lineNumber = 0)
{
    await Task.Yield();
    if (cancellationToken.IsCancellationRequested)
    {
        throw new OperationCanceledException(
            $"Task was canceled at {memberName} in {filePath} at line {lineNumber}.",
            cancellationToken);
    }
}
```

That's pretty straight forward. The `if` and `yield` are unrelated so we could switch
them around. It doesn't really matter unless the token is cancelled. Which should be
very rare I would think. If this is a frequent occurrence or concern, you may want to 
consider if you would prefer to check for cancellation before switching contexts or
if you would prefer to have a cancellation exception on its own context. It's a small
function - you could have one of each if you wanted :).

I tried thinking of other things to do with this function, and the first thing that
came to mind was to make it an extension method on `CancellationToken`. That way it
would be a little more natural to call - not having to pass the token in. That way, we 
can `await` a `token.Yield()` instead of `Task.Yield()`. It also seems more logical to
me. Thinking of it as yielding on the token, not the task.

The other thing I thought of was to implement the function the same way `Yield()` 
itself is implemented. That way we wouldn't need to call `Task.Yield()` inside the
method. I should probably go ahead and say that this should not be done and should
be put in the "bad idea" / "stupid C# trick" category. The native `Yield()` method
is synchronous (as such, it doesn't create a state machine), but returns 
`YieldAwaitable` so it can be awaited. So, we drop the `async`, return 
`YieldAwaitable`, get rid of the `Yield()`, and return a  `default(YieldAwaitable)`. 
Easy enough, right?

```csharp
public static YieldAwaitable Yield(
    CancellationToken cancellationToken,
    [CallerMemberName] string memberName = "",
    [CallerFilePath] string filePath = "",
    [CallerLineNumber] int lineNumber = 0)
{
    if (cancellationToken.IsCancellationRequested)
    {
        throw new OperationCanceledException(
            $"Task was canceled at {memberName} in {filePath} at line {lineNumber}.",
            cancellationToken);
    }

    return default;
}
```

Never let `CompilerServices` internal comments like `intended for compiler use only` 
stop you from using it. Live dangerously! I mean, what could possibly go wrong?

*Insert stupid code trick disclaimer - *
Remember, this is just a fun little experiment. Don't use this in production code.