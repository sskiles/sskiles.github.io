---
layout: post
author: Shane Skiles
title: Memoization
tags: [c#, memoization, memoize]
---

For the sake of this post, I will be using the term "memoization" to refer to the caching of
function results based on their input parameters. This sometimes gets sucked into 
top-down, bottom-up, dynamic programming, tabulation and recursion, 
but I will not be going into that here.

Where were we? Memoization the caching of function results based on their 
input parameters. This can significantly reduce the time complexity of 
computationally expensive functions, especially when the same calculations 
may be performed multiple times.

Depending on how you plan on using memoization in your code, it might be better to
build a custom solution for each specific use case. Other times, it may be more
efficient to use a generic memoization function.

This would be an example of a generic memoization function. Both thread-safe and
non-thread-safe versions are provided. The non-thread-safe version is faster,
but... you know the rest.

```csharp
Func<T1, T2, TResult> Memoize<T1, T2, TResult>(Func<T1, T2, TResult> func)
{
    var cache = new Dictionary<(T1, T2), TResult>();
    return (x, y) =>
    {
        if (!cache.TryGetValue((x, y), out var result))
        {
            result = func(x, y);
            cache[(x, y)] = result;
        }

        return result;
    };
}

Func<T1, T2, TResult> MemoizeConcurrent<T1, T2, TResult>(Func<T1, T2, TResult> func)
{
    var cache = new ConcurrentDictionary<(T1, T2), TResult>();
    return (x, y) =>
    {
        return cache.GetOrAdd((x, y), func(x, y));
    };
}
```

The memoization functions above are for only two parameters. A fully generic solution 
would be able to handle up to `Func<T1,T2,T3,...,T15,T16,TResult>` as a 
method/result if you wanted to go that for. That seems unnecessary, but who's to say.

This is a fun approach. 
This technique takes a function as an argument and returns a new "memoized" function.
The function argument is turned into a lamdba function that captures the memoizing dictionary 
in a closure, this lambda function is the result of the memoization. So, when the lambda 
is called, the dictionary is checked for the result. If the result is found, 
that result is returned. If the result is not found, the function is called and 
the result is stored and returned.

Here's a simple example of how to use the memoization function - 
Let's say you have a computationally expensive function that takes two parameters 
and returns a result (e.g., `Func<T1, T2, TResult>`)

```csharp
// Example of a computationally expensive function
public static double MyExpensiveFunction(int x, int y)
{
    ...
    return myDoubleResult;
}

// Memoized version of the function created using the Memoize function
var memoizedFunction = Memoize(MyExpensiveFunction);

var result0 = MyExpensiveFunction(1, 2); // The original function
var result1 = memoizedFunction(1, 2); // Calls the original function
var result2 = memoizedFunction(1, 2); // Returns the cached result
```
This is a simple example, but you can see how this could be useful in a 
real-world scenario. The original function remains unchanged, and the memoized
function can be used in its place. This allows you to easily add memoization/caching
to any "pure" function without modifying its implementation.

This is not meant to be a drop in solution for caching by any means. However, a dedicated
caching solution would likely be more involved than dropping in a one-liner lambda function.
This is more of a fun little experiment to see how you could use memoization in C#.

Of course, always benchmark your code to see if this is worth the effort. 
This is another good example of a "stupid C# trick" that is fun to play with,
but not necessarily ready for a production environment. 
I really do need a standard disclaimer.