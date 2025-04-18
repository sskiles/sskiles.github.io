---
layout: post
author: Shane Skiles
title: DotNet Benchmarking
tags: [dotnet, benchmarking, benchmarkdotnet, c#]
---

There are a few different opinions on code performance and/or optimization. 
Whatever your opinion is, you are going to need to have measurements to compare.
The de facto standard for .NET benchmarking is 
<a href="https://benchmarkdotnet.org/">BenchmarkDotNet</a>. Available via NuGet,
BenchmarkDotNet is a reliable, high precision library for benchmarking .NET code. 
If you ever need to measurements of code perfomance, BenchmarkDotNet is the way to go.

I will not even attempt to cover all the features of BenchmarkDotNet, but I will 
point you in the right direction(s). I will say that BenchmarkDotNet has some of 
the BEST documentation I have ever seen. A quick 
<a href="https://benchmarkdotnet.org/articles/overview.html">overview</a> from their 
site shows you how to get started. The example code has a class with methods to 
benchmark, marking the test methods with the `[Benchmark]` attribute. Then
running the benchmark with `BenchmarkRunner.Run<MyClass>()` will out the results 
to the console.

You can only run benchmarks in release mode, ensuring optimized production ready code 
is being tested. The base output will display the elapsed mean time for each 
benchmark (in milliseconds/microseconds/appropriate units), the standard error 
of the mean, and the standard deviation. Through configuration, you can easily add in
memory usage, garbage collection, and other metrics. There are over 60 different 
samples in the documentation labeled as "intro" to show you how to use BenchmarkDotNet.
I told you the documentation was outstanding.

It is also possible to export the results to numerous formats ranging from 
AsciiDoc to XmlFullCompressed. Side note: if you have experience with graphing in R, 
`RPlotExporter` is a beautiful way to visualize the results. BenchmarkDotNet uses
the <a href="https://github.com/AndreyAkinshin/perfolizer">perfolizer</a> 
performance analysis toolkit / statistical engine, so any data you want to crunch
or pull out should be within reach.

Often times, I will actively tweak code in benchmarks to see how different changes can 
affect performance or memory usage. This is a great tool for that. One thing that
always throws me is switching back and forth from debug mode while tweaking code 
to release for benchmarking. Sometimes other code changes are needed to switch 
back and forth. Here is a quick and dirty way to simplify this process.

```csharp
#define TEST_BENCHMARK

using System;
using System.Collections.Generic;
using System.Reflection;
using BenchmarkConsole.Benchmarks;
using BenchmarkDotNet.Running;

/// <summary>
/// Test Harness for Benchmarks.
/// </summary>
internal static class Program
{
    private static async Task Main()
    {
#if TEST_BENCHMARK
        await MainType<TestBenchmark>();
#endif
    }

    private static async Task MainType<T>()
        where T : class
    {
#if !DEBUG
        BenchmarkRunner.Run<T>();
        await Task.CompletedTask;
#else
        await ValidateBenchMarks<T>();
#endif
        Console.WriteLine("\n...Complete\n");
    }
#if DEBUG
    private static async Task ValidateBenchMarks<T>()
        where T : class
    {
        await Task.Yield();
        var type = typeof(T);
        var methods = type.GetMethods(
                BindingFlags.DeclaredOnly | BindingFlags.Instance | BindingFlags.Public)
            .Where(m => m.GetCustomAttribute(typeof(BenchmarkAttribute)) != null);

        object? instance = Activator.CreateInstance(type);
        var maxNameLength = methods.Max(m => m.Name.Length);
        foreach (var method in methods)
        {
            object? result = method.Invoke(instance, null);
            object? readableResult = null;
#if TEST_BENCHMARK
            if (result != null)
            {
                readableResult = ((List<int>)result)[^1];
            }
#endif
            Console.WriteLine(
                "Method "
                    + $"{new string(' ', maxNameLength - method.Name.Length)}"
                    + $"{method.Name}: {readableResult ?? result}");
        }
    }
#endif
}
```
This code will run the benchmarks in release mode, but will run each method 
individually in debug mode to change, evaluate and compare. As an example, 
running the code against `TestBenchmark` class (determined by `TEST_BENCHMARK`) 
will output the following in release mode:
```
| Method         | N   | Mean       | Error     | StdDev    | Median     | Ratio | RatioSD | Gen0   | Allocated | Alloc Ratio |
|--------------- |---- |-----------:|----------:|----------:|-----------:|------:|--------:|-------:|----------:|------------:|
| GenPrimesNaive | 100 |   258.0 ns |  31.45 ns |   4.87 ns |   259.8 ns |  1.00 |    0.02 | 0.0440 |     432 B |        1.00 |
| GenPrimes2N    | 100 |   276.0 ns | 101.33 ns |  15.68 ns |   271.8 ns |  1.07 |    0.06 | 0.0360 |     368 B |        0.85 |
| PossiblyClever | 100 |   280.7 ns | 480.00 ns | 124.66 ns |   222.3 ns |  1.09 |    0.44 | 0.0520 |     520 B |        1.20 |
|                |     |            |           |           |            |       |         |        |           |             |
| GenPrimesNaive | 500 | 1,627.6 ns | 338.90 ns |  52.45 ns | 1,610.9 ns |  1.00 |    0.04 | 0.1320 |    1248 B |        1.00 |
| GenPrimes2N    | 500 | 1,566.8 ns | 372.46 ns |  57.64 ns | 1,559.7 ns |  0.96 |    0.04 | 0.1240 |    1184 B |        0.95 |
| PossiblyClever | 500 | 1,056.6 ns | 219.57 ns |  33.98 ns | 1,052.9 ns |  0.65 |    0.03 | 0.1680 |    1608 B |        1.29 |
|                |     |            |           |           |            |       |         |        |           |             |
| GenPrimesNaive | 750 | 2,564.6 ns | 201.41 ns |  31.17 ns | 2,573.5 ns |  1.00 |    0.02 | 0.2440 |    2296 B |        1.00 |
| GenPrimes2N    | 750 | 2,594.0 ns | 628.63 ns |  97.28 ns | 2,584.1 ns |  1.01 |    0.04 | 0.1720 |    1640 B |        0.71 |
| PossiblyClever | 750 | 1,699.9 ns | 450.43 ns |  69.70 ns | 1,683.0 ns |  0.66 |    0.03 | 0.2320 |    2216 B |        0.97 |
```
And in debug mode:
```
Method GenPrimesNaive: 99991
Method    GenPrimes2N: 99991
Method PossiblyClever: 99991
```
This allows you to make changes while debugging and verify the results match
the expected results. Then run the benchmarks in release mode to get the metrics.
Switching back and forth is as easy as `dotnet run -c release` and `dotnet run`.

In the results above, the benchmark shows the result of methods when varying the 
paramenter `N` from 100 to 750. `GenPrimesNaive` is set as the baseline which the
other methods' timing and memory usage are compared against. The debug results show
the result of the last member of each of the lists the test methods return. 

That's all I have to add to world on BenchmarkDotNet. A quick and dirty way to
switch between debug and release while testing and benchmarking. Everything else
is in the documentation. I hope you find this useful.

Remember, *"premature optimization is the root of all evil"*.