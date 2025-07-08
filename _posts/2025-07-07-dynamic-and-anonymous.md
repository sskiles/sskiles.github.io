---
layout: post
author: Shane Skiles
---

In my previous post, I mentioned the idea of creating a 
```record``` out of a ```class```. I did work on that and wondered if I could
get the ```with``` keyword implemented. I knew it worked with records and structs,
but I didn't know it also worked with ```anonymous types```. I worked on a project
one time that created a lot of anonymous types, and I never really thought about them
being readonly. It makes sense that ```with``` would work with them.

That project also used a lot of ```dynamic``` types. Anonymous types can be passed
as dynamic parameters to functions. If you have never used the ```dynamic``` type, 
break out an editor, create a new ```ExpandoObject``` and start adding properties to it
(add a ```Func``` or ```Action``` for more fun).
It's fun to play with, but I don't recommend using it in production code. 

One thing that approve of is using ```dynamic```/```ExpandoObject``` for is ad-hoc
JSON serialization. If you need to create non-trivial JSON object quickly, 
this is a great approach.

```csharp
dynamic expando = new ExpandoObject();
expando.Property1 = "Value1";
expando.Property2 = "Value2";
expando.List = new List<string> { "One", "Two" };
expando.Dictionary = new Dictionary<string, int>()
{
    { "Key1", 1 },
    { "Key2", 2 }
};

var options = new JsonSerializerOptions { WriteIndented = true };
string jsonString = JsonSerializer.Serialize(expando, options);
Console.WriteLine(jsonString);
/*
{
  "Property1": "Value1",
  "Property2": "Value2",
  "List": [
    "One",
    "Two"
  ],
  "Dictionary": {
    "Key1": 1,
    "Key2": 2
  }
}
*/
```

Granted, for this example it is a bit overkill, but if you need to create a more complex
object without a good JSON editor, this is a great way to do it. Of course, create proper
classes if you are going to be doing this a lot, but for quick and easy JSON, this works.
Combine this with Visual Studio's "Paste JSON as Classes" feature and you can quickly
prototype a class and then use that in your code.

This has been your semi-regular diversion into "stupid C# tricks".