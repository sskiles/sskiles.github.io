---
layout: post
author: Shane Skiles
title: "Comments and Commentary"
tags: [c#, csharp, code, comments, coding style]
---

I was just going to write a short post about code comments, but I think this will probably wander around coding and style in general.
I was writing some code today that involved a bit of data manipulation with serialized data coming in and going out.
The formats coming in and going out were 90% the same, and since this was just a bridging API, I decided to use the same data model for both.
There was no need to create separate incoming and outgoing models with a translation layer to modify the data when this process was only going to be used here.
But, I'm getting ahead of myself.

During deserialization, the data was used to populate the model for the outgoing data.
I just needed to remove a couple of fields that I needed coming in but didn't want to be serialized going out.
In reality, the deserialization did all the work; I just wanted to avoid sending out a couple of fields.
Unfortunately, the serialization layer being used would read or write all fields with values.
This was where my dilemma came in.
Do I create an outgoing model just for this purpose?
Do I create an anonymous object just for this purpose?
What I decided to do was simply blank out those values on the model and send it out.

This worked fine (aside from saving the overhead of creating new objects). 
I did feel it necessary to add in a comment explaining the purpose of the code. 
Either of the other choices would (should) have required the same or a similar comment.
If anyone runs across code transferring values from one object to another, they would ask why.
If they looked carefully enough, they might notice you "missed" a couple of values and "fix" that for you.
At least code explicitly removing values won't be seen as accidental.
Either way, a comment explaining why something was done is appropriate.

This was going to be the original point of the post: 
comments should tell you "why" something is being done.
They shouldn't be needed to describe "what" is being done.
Code itself can be complex and difficult to follow. 
The business logic behind it can definitely be challenging to follow sometimes.

The business logic part of the complexity can be hard to avoid.
The code part of the complexity can usually be avoided.
Good code should be understood by anyone with a good grasp of the language.
This is the tricky part.
I'm not saying code complexity isn't necessary or shouldn't be done.
I'm just saying don't try to unnecessarily complicate things.
Any language can be written in such a way that it is unreadable.
Unless you are smarter than the compiler, have "reasons", and it's Tuesday, don't do this.
If you do, comments had better be involved with good reasons.
Don't obfuscate a codebase that's being maintained.

Shane
