---
layout: post
author: Shane Skiles
title: "Comments and Commentary"
tags: [c#, csharp, code, comments, coding style]
---

To pick up from the the previous [post](comments-and-commentary), don't write bad code or make it harder than it has to be.
There are, of course, exceptions to every rule.
One of those cases is coming in, as a consultant, to a codebase that works, but needs maintenance.
Actually, any similar situation where you are only going to be touching a small fraction of a codebase, keep the code changes as much like the rest of the code as possible.
As a consultant, contributor, whatever, unless you are specifically tasked with creating, updating, designing the coding style, don't change it.

Usually, bad code just exists because it doesn't know better. 
If there is code that exists just because it is done that way everywhere else, leave it be. 
If the bad code isn't hurting anything and you won't be able to fix it all, keep it.
Even smells and "cargo cult" baggage can serve as waypoints in a codebase.
When you come in to a new codebase and you see something everywhere, you come to expect it. 
When you run across something written in a totally different style, that makes you take pause.
You end up double checking anything that's different just because it is different.

Good, solid code shouldn't leave you asking questions. 
It should be easy to read. 
You should be able to tell what it is doing, even if you don't know why.
Once you have a grasp of the problem domain, the why should start to make itself clearer.
If someone with a strong grasp of the language and domain might ask "Why?", you need to comment that.

Comment your functions! Parameter types, return types, functionality/purpose. 
This not only helps anyone every needing to interact with the code, it helps you double check yourself.
Should those parameters be those types? Are they needed? 
Are the parameters the most forgiving for callers? 
Does that parameter need to be a concrete type when it could accept anything with `X` interface?
Does it need to return `X` type when an interface would work?

I'll come back and organize these posts later on. 
What it is is what you get for now.

Shane
