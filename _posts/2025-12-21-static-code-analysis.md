---
layout: post
author: Shane Skiles
title: "C# Static Code Analysis"
tags: [csharp, static-analysis, code-quality]
---

This past week, I went through and cleaned up some C# code that had been cranked 
out over the past few weeks. The primary focus up until this point had been getting 
the base functionality built and working. Now, it is working, but it's a bit messy. 

My first pass was to just run through the code adding file headers, Xml documentation,
some formatting, and basic cleanup. Of course, nothing is set in stone at this point,
so I didn't change any of the actual code logic. Actually putting words to the 
code can help you more clearly define the APIs, interfaces, and classes -
what the code is doing and why. It can be sort of like "rubber duck debugging",
describing the code, can help you clarify your intent.

I will probably expand this into a few different posts covering different 
analyzers, editor configs, and whatever else comes up. For now, I'll just 
go over what I did in this first pass and some general thoughts. The main analyzers
involved were StyleCop and Roslyn Analyzers. My main goal for this pass was to 
get the code to a point where it was "clean" according to these analyzers. 
This makes it much easier to see real issues when they arise later on. It's hard 
to spot real problems when there are 50 different warnings and suggestions on every file.

To get started, in this first pass was to just add in file headers. StyleCop has a default
file header template that you can modify as needed. It also has rules (`SA16xx`) to let you 
know if a file is missing a header or the header is not correct or not formatted correctly. 
This is a good way to wrap your head around how the project is laid out and what each file
is doing. You also get a chance to see if the file names and locations make sense. 
If you find yourself puzzled by a file name or unsure about its purpose, make a note 
and come back to it later. I ended up with a good list by the end of this first pass.

The next step was to add in Xml documentation comments. This falls under the 
`SA1600` and other rules. I'll visit the meanings of these rule names/number below. 
Basic method documentation comments are pretty straightforward. You have a summary,
parameters, and return value. Remarks and exceptions can be added as needed. In this pass,
I focused on just getting the summary, parameters, and return values documented. 
Generation of headers and comments can be automated quite well with different tools, 
but be careful. You want to make sure the comments aren't hallucinations and make sense.
After doing this, if you find yourself puzzled by what a file is doing, it may be a sign
that the file needs to be renamed, moved, or even split up into multiple files.

Analyzer rules have a specific naming convention. 
Usually, two letters relating to the type of analyzer followed by a 4 digit number.
The StyleCop rules I mentioned above started with `SA`. There is no need to know these 
rule names/numbers by heart, but it makes it simple to look them up. All of these rules
come with human readable descriptions. Usually describing what the rule is checking for,
why it is important, and sometimes how to fix it. 

Many of these "rules" are more just
"best practices" or "suggestions". These rules can be configured to be 
warnings, errors, suggestions, or just ignored.
Sometimes, an analyzer will have rules that 
contradict each other:
- `XX0001` - Do this
- `XX0002` - Don't do this
Pick the one that makes the most sense for your project/team, - make it complain
about the other way. This will help you standardize your code style and practices. 
Not only does it help to have consistency across a team, 
but it also reduces arguments over code style issues in code reviews.
And again, things that make sense in one project may not make sense in another
or a different section of the project.

Another step going on during the first two was to format namespaces (block vs file scoped)
and usings (inside vs outside namespace, alphabetized). There are rules for these as well.
When I was taking care of the headers this was a good time to standardize these since I
was at the top of the file. During the Xml documentation step, I was able to identify
identify any "missing" usings or unnecessary usings. The "missing" usings are ones
are not needed for the code to compile, but are used in the Xml documentation. 
The code worked implicitly, but not explicitly. Removing unnecessary usings just helps 
cleans things up a bit.

The next step in this pass was to disable rules that are, I won't say pet peeves,
but more just use common sense. 

For example, I don't care about the ordering private, protected, and public members 
or readonly vs readwrite variables. I care more about grouping related members together.
If it makes more sense to have private members next to the public members that use them, 
then that is what I want. So, I disabled those rules. 



