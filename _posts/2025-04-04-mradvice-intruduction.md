---
layout: post
author: Shane Skiles
title: MrAdvice Introduction
tags: [c#, mradvice, aop, aspect oriented programming]
---

<p>I recently ran across a nice project here on GitHub for implementing aspect oriented 
programming (AOP) in C# (available via NuGet). Let me introduce you to the wonderful 
world of <a href="https://github.com/ArxOne/MrAdvice">MrAdvice</a>. It is an open-source 
alternative to PostSharp's <a href="https://www.postsharp.net/metalama">metalama</a>, 
which is a commercial AOP framework for .NET.</p>

<p>MrAdvice is a lightweight, open-source AOP framework for .NET that allows you to
apply cross-cutting concerns to your code without modifying the original source. 
It is designed to be easy to use and integrate into existing projects, using 
code weaving to inject additional behavior into methods at runtime. This allows you 
to separate concerns, improve code readability, and reduce boilerplate code.</p>

<p>MrAdvice provides a simple and intuitive API for defining aspects. These aspects 
allow you to encapsulate existing functionality with new behavior, such as logging,
caching, exception handling, and more. A sample aspect might look like this:</p>

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
