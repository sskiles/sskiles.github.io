---
layout: post
author: Shane Skiles
title: MrAdvice Introduction
tags: [c#, mradvice, aop, aspect oriented programming]
---

I recently ran across a nice project here on GitHub for implementing aspect oriented 
programming (AOP) in C# (available via NuGet). Let me introduce you to the wonderful 
world of <a href="https://github.com/ArxOne/MrAdvice">MrAdvice</a>. It is an open-source 
alternative to PostSharp's <a href="https://www.postsharp.net/metalama">metalama</a>, 
which is a commercial AOP framework for .NET.

MrAdvice is a lightweight, open-source AOP framework for .NET that allows you to
apply cross-cutting concerns to your code without modifying the original source. 
It is designed to be easy to use and integrate into existing projects, using 
code weaving to inject additional behavior into methods at runtime. This allows you 
to separate concerns, improve code readability, and reduce boilerplate code.

MrAdvice provides a simple and intuitive API for defining aspects. These aspects 
allow you to encapsulate existing functionality with new behavior, such as logging,
caching, exception handling, and more. A sample aspect might look like this:

```csharp
// Sample aspect
public class MrAdviceSampleAttribute : Attribute, IMethodAdvice
{
    public void Advise(MethodAdviceContext context)
    {
        Console.WriteLine($"Method {context.TargetMethod.Name} is being advised.");
        context.Proceed();
        Console.WriteLine($"Method {context.TargetMethod.Name} has completed.");
    }
}

...

// Sample usage
[MrAdviceSampleAttribute]
public static void MrAdviceSampleMethod()
{
    Console.WriteLine("Running MrAdvice tests...");
}
```
This example would create the following output:
```
Method MrAdviceSampleMethod is being advised.
Running MrAdvice tests...
Method MrAdviceSampleMethod has completed.
```

I have been doing a lot of work with asynchronous services recently and have been
creating a lot of boilerplate code to handle the async/await patterns. Here are a 
couple of ideas I have for using MrAdvice to help with this.

```csharp
public class AsyncLoopUntilCanceled : Attribute, IMethodAsyncAdvice
{
    public async Task Advise(MethodAsyncAdviceContext context)
    {
        var cancellationToken = context.Arguments.OfType<CancellationToken>()
            .FirstOrDefault();
        while (!cancellationToken.IsCancellationRequested)
        {
            await context.ProceedAsync();
        }
    }
}

public class AsyncOperationCanceledHandler : Attribute, IMethodAsyncAdvice
{
    public async Task Advise(MethodAsyncAdviceContext context)
    {
        try
        {
            await context.ProceedAsync();
        }
        catch (OperationCanceledException)
        {
        }
    }
}
```
I definitely plan to explore this further and see what I can come up with. However,
I do have some concerns about readability and maintainability, possibly performace - 
especially in a team environment.

For the readability part, it seems like it might become difficult to follow 
or cause issues when just reading the code. Although, it might become second nature 
to just look for the attributes. I guess it would depend on how much you use it and
how much it end up being used.

As for maintainability, it could be difficult or impossible to make nuanced 
changes to code that is being affected by aspects. Fixing an aspect could cause issues
elsewhere. Creating a new aspect just adds confusion. Removing the aspect to make the 
change defeats the purpose of using it in the first place. Also, accidentally stacking
multiple aspects in the wrong order could definitely cause a lot of confusion.

Performance could become an issue especially if you are stacking multiple aspects
on a single method. Especially if looping or recursion is involved. Going back to the
maintainability issue, if you have multiple aspects on a single method, getting the
stack order wrong could cause performance issues.

Overall, I think MrAdvice is a great project and I will definitely be exploring 
it more. I think it is a great way to reduce boilerplate code and there are a lot of 
possible use cases. I will using it in some personal projects that I have.
That being said, I do not see this being deployed (by me) in a production environment 
anytime soon. 

I think it is a great tool for learning and exploring, but I have issues with 
"code weaving" in general. I know that the guys working on these projects are top-notch.
I know the Roslyn team is top-notch. I just see the possibility of edge cases creeping in
and hiding in the corners as new versions the weavers and compiler are released. I know 
that under the hood, the resulting code and image is the same as if you had written it
yourself. But I just like to see the code and know what is going on.