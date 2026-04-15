---
layout: post
author: Shane Skiles
title: "ExpandoObject Fun and Games"
tags: [c#, stupid tricks, json, debugging, sqlite]
---

I had originally planned on writing a short piece on SQLite today, but while thinking of a simple use case, I thought of this little trick that can be useful in a couple of ways.
Enter ExpandoObject. For those of you unfamiliar with the C# ExpandoObject, this may make you question what you know about the language.
For those of you who have had to deal with it, I hope it was by choice and not thrust upon you.

The ExpandoObject, when declared as a `dynamic` object, becomes a special creature.
You can add and reference properties to it on the fly.
No special naming or setup required.
Want to add a property? `expandoObject.MyProp = new List<string>();` - Done!
Reference it later? `expandoObject.MyProp.Add("blah!");` - Done!

The `dynamic` keyword enables late binding and defers type checking from compile-time to runtime.
This means no autocomplete, syntax highlighting, IntelliSense, or any other niceties you might expect from a well-behaved object.
As such, if you make any mistakes using the object, you won't know it until your program throws an error at runtime.
At its heart, the ExpandoObject implements `IDictionary<string, object>`, and with `dynamic` pushing the actual execution to the DLR (Dynamic Language Runtime).
Given this information—combined with the fact that performance isn't great, it has high memory usage, and possible memory leaks—you probably won't want to use this in production.

With all of this in mind, this should probably be filed under "stupid C# tricks".
One of my favorite uses of the ExpandoObject is for serialization/deserialization.
Here is a short example of how that works:

```csharp

        dynamic expando = new ExpandoObject();
        expando.strings = new List<string> {"a", "b", "c"};
        expando.ints = new List<int>{1,2,3};
        expando.strings.Add("z");
        expando.miscVal="something";
        var json = JsonSerializer.Serialize(expando);
        Console.WriteLine(json);

        dynamic dyn = JsonSerializer.Deserialize<ExpandoObject>(json);

        Console.WriteLine(dyn.strings[3]);
        Console.WriteLine(dyn.miscVal);
```
Output:
```
{"strings":["a","b","c","z"],"ints":[1,2,3],"miscVal":"something"}
z
something
```
This can make the ExpandoObject very useful for prototyping JSON objects.
But once you have the schema figured out, go ahead and create a real object to use instead.

This brings me back to my original topic: SQLite.
I will still probably write a longer piece on it, but for now, I will just go over this particular use case.

The ExpandoObject is a great way to capture the state of a program/procedure.
Just toss all of your variables into an Expando and serialize everything out.
A SQLite database is a great place to store that data for easy retrieval.
Create a simple table (name, description, JSON, timestamp, etc.) and store it in there.

Anyway, that was my semi-useful C# trick for today.
If you've ever seen it in production code, I'm sorry.

Shane
