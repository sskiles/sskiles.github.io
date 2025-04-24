---
layout: post
author: Shane Skiles
title: Stupid C# Pipe Trick
tags: [c#, csharp, pipe trick]
---

I recently wrote a post about playing around on a Linux system and different shell
commands. One of my favorite shell operators is the pipe `|` which redirects 
the output of one command into another command as input. Once you get the hang of it,
you'll wonder how you ever got along without knowing how to use it.

A while back I was wondering if it would be possible to do something like that in C#.
I don't mean writing programs to read `stdin`/`stdout`. I mean actually using `|` in code.
The `|` operator in C# is a bitwise or operation. I thought it would be fun if I
could reappropriate it for something more fun. The first thing that came to mind 
would be to use it for filtering like you would pipe something through grep in a shell.

*Insert disclaimer text. 
Do not use this for anything other than experimentation and laughs.*

I thought it would be neat to be able to get an `IEnumerable<T>` pipe it `|` 
through a function and get a filtered `IEnumerable<T>` out. Basically, you could
write something to the effect of `filteredData = data | filter`. That sounds simple
enough... right? Well, the filter is going to take and return `IEnumerable<T>`, it
would need to be a `Func<IEnumerable<T>, IEnumerable<T>>`.

I can overload operators on a `struct`. The function will end up being a 
`Func<T, TResult>`.  Since I want to overload the operator, the struct will
be a generic `<T, TResult>` also. With this information, I can define the struct,
pass the function into the constructor, the overload the `|` operator to use the data
on the left hand side as the input to the function on the right hand side, 
invoke the function on the data and return the result:

```csharp
public readonly struct Pipe<T, TResult>
{
    private readonly Func<T, TResult> function;

    public Pipe(Func<T, TResult> func)
        : this() => this.function = func;

    public static TResult operator |(T arg, Pipe<T, TResult> pipe)
        => pipe.function.Invoke(arg);
}
```

That was simple, right? I'm kidding. There may or may not be a simpler way of doing this. 
Either way, we shouldn't be doing this for any reason other than because we can.

`Func<IEnumerable<T>, IEnumerable<T>> func1 = x => x.Where(x => ...);`
`var filter1 = new Pipe<IEnumerable<T>, IEnumerable<T>>(func1);`

```csharp
IEnumerable<T> data = ...;
IEnumerable<T> filtered = data | filter1;
```
Since filter takes and returns `IEnumerable<T>` we can chain those together.
```csharp
Func<IEnumerable<T>, IEnumerable<T>> func1 = x => x.Where(x => ...);
// Different filter and format.
var func2 = (IEnumerable<T> x) => x.Where(x => ...);

var filter1 = new Pipe<IEnumerable<T>, IEnumerable<T>>(func1);
var filter2 = new Pipe<IEnumerable<T>, IEnumerable<T>>(func2);

IEnumerable<T> data = ...;
IEnumerable<T> filtered = data | filter1 | filter2;
```

If you have a lot of commonly used filters, this is a great way to be able to
mix and match them together for isolating targeted data sets - when you are TESTING.
Remember, this is just a fun experiment. Don't use this in production code.
