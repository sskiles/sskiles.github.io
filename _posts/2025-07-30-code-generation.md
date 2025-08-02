---
layout: post
author: Shane Skiles
title: Code Generation
tags: [c#, code generation, iincrementalgenerator]
---

Okay, back to our unregularly scheduled programming. Today, let's see if we can get a
code generator going. This is a quick and dirty example but it gets up and running.
Let's create a simple source generator that generates a class with a method that 
returns a string.

The source generator will be a class library that implements the `IIncrementalGenerator` 
interface. It needs to target ```netstandard2.0``` for maximum compatibility. 
It is possible to target other frameworks, but that's a different post. 

First, let's set up the projects (one for code generation, one for consumption)
and a solution to hold them.

Here we go:
```sh
dotnet new classlib -n MySourceGenerator -f netstandard2.0
dotnet new console -n MyConsoleApp
dotnet new sln -n SourceGeneratorSolution
dotnet sln .\SourceGeneratorSolution.sln add .\MyConsoleApp\ .\MySourceGenerator\
```
Set up the `Microsoft.CodeAnalysis` packages for the generator:
```sh
dotnet add MySourceGenerator/MySourceGenerator.csproj package Microsoft.CodeAnalysis.CSharp -v 4.2.0
dotnet add MySourceGenerator/MySourceGenerator.csproj package Microsoft.CodeAnalysis.Analyzers -v 3.3.3
```

Delete or ignore the default `Class1.cs` and create a new
`MyGenerator.cs` and add the following code:

```csharp
using Microsoft.CodeAnalysis;

namespace MySourceGenerator
{
    [Generator]
    public class MyGenerator : IIncrementalGenerator
    {
        public void Initialize(IncrementalGeneratorInitializationContext context)
        {
            context.RegisterPostInitializationOutput(ctx => ctx.AddSource(
                "GeneratedCode.g.cs", 
                @"
namespace MyConsoleApp;  // See notes below
public static class GeneratedCode
{
    public static string GetMessage() => ""Hello from generated code!"";
}
"));
        }
    }
}
```

This code implements the `IIncrementalGenerator` interface. 
The `Initialize` method registers a callback that adds a 
new source file, `GeneratedCode.g.cs`, to the compilation.

**Notes:** 
- Remember that the generated code will be a new file so it needs its own namespace. 
It doesn't default to the generator's namespace. 
In this case, we are using `MyConsoleApp` as the namespace. So it will already be
available in the console app without needing to add a `using` directive.
- If you get an error regarding the namespace such as 
"Feature 'file-scoped namespace' is not available in C# X.X..."
or any other language versioning issue, you can update the `LangVersion` 
in the `.csproj` file of the generator project. Add `<LangVersion>latest</LangVersion>` 
to the `PropertyGroup` section of `MySourceGenerator.csproj`
I thought I would need to do this for this example, but it worked without it in my case.

To use the generator, the console app needs to reference it in a specific way.

Add a project reference to the generator:
```sh
dotnet add MyConsoleApp/MyConsoleApp.csproj reference MySourceGenerator/MySourceGenerator.csproj
```

Modify `MyConsoleApp.csproj` for source generation:
You must edit the `.csproj` file of the consuming application (`MyConsoleApp`) 
and modify the `ProjectReference` to include 
- `OutputItemType="Analyzer"`
- `ReferenceOutputAssembly="false"`
This tells the compiler to use the project as a generator and not as a regular library.

The `ProjectReference` should look like this:
```xml
<ItemGroup>
<ProjectReference Include="..\MySourceGenerator\MySourceGenerator.csproj" 
    OutputItemType="Analyzer" ReferenceOutputAssembly="false" />
</ItemGroup>
```

Now, we can use the code that our generator creates in the console app.

Modify `MyConsoleApp/Program.cs` to call the generated method:
```csharp
Console.WriteLine(MyConsoleApp.GeneratedCode.GetMessage());
```
IntelliSense won't find `GeneratedCode.GetMessage()` before the first build, 
but the project will build and run correctly.

Finally, build the solution and run the console app to see the result.
```sh
dotnet build
dotnet run --project MyConsoleApp
```

You should see the following output:
```
Hello from generated code!
```
If you see this, congratulations! Your source generator is working correctly. 
That means the generator has successfully created a new source file, 
compiled it into the project, and your console application is able to call it.

If you want to see the generated code in Visual Studio, open Solution Explorer, 
and start expanding `MyConsoleApp` > `Dependencies` > `Analyzers` > `MySourceGenerator` > `MySourceGenerator.MyGenerator` > `GeneratedCode.g.cs`. 
Once you are finally there - double-click on it to view the generated code.

Useful cases for source generators include:
- Boilerplate and repetitive code patterns like expanding generics (`T1, T2, ..., T16`)
- Pulling reflection information at compile time so no runtime reflection is needed
- Generating code based on all types of metadata (compiler directives on steroids)

Keep in mind `IIncrementalGenerator` isn't 
the first .NET source generator (`ISourceGenerator`), and it probably won't be the last.
But it's what we have for now. 
