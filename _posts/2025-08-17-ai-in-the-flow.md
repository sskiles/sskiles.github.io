---
layout: post
author: Shane Skiles
title: "AI In The Flow"
tags: [AI, C#, SQL, GPT-4o, Gemini 2.5 Flash]
---

This week, I had a chance to just grind out some code and actually have AI 
get in the groove with me. It was an existing project that needed some 
backend and database work, and I was able to have AI help me with some of the work
that was needed. 

I had worked with the database before, but not extensively. To get started, 
I scripted out the database schema for myself 
(I was not sure exactly what I would be working on), then decided I would 
turn Gemini 2.5 Flash (I'll refer to this as Flash from here on out) 
loose on it while I started working on some of the base queries/stored procedures. 
I chose Flash because I have always been impressed with its capabilities and speed. 
Gemini 2.5 Pro is good for more abstract tasks, but it can be very slow. 
For relatively simple tasks, Flash is more than capable and much faster.
I didn't want to wait around for an AI to just verify
code validity and offer suggestions.

One of the first tasks I had Flash do was complete a `Merge` statement for me. 
I had the basics of the query, but I always get bogged down in the syntax. 
That was quick and easy for Flash to handle. Of course, I checked the query
to make sure it was doing the right things. I liked it, the IDE liked the syntax,
and we moved on.

I was actually saving all of the 
procedures I was writing down to a local directory (with the schema script) 
so it was easy for me to start Flash off in that directory and have it create
a GEMINI.md file for me/itself. I didn't have permissions in the database to
create/execute stored procedures, so having Flash in there to periodically
review and suggest changes was a big help.

If I would have known I wasn't going to have database permissions, I would have
started off using Visual Studio (with GPT-4o) instead of SSMS for the initial
work. Predictive code completion is a huge time saver, and I would have loved 
that for some of the queries I was writing. But, once I had the basics of the 
procedures down, I was able to switch over to Visual Studio and have GPT-4o
start to help get the repositories (controller/service/repository) set up.

I put most of the validation there before moving it to the service layer. GPT-4o
does a pretty good job of generating the validation code, but it's not a mind reader
so I usually had to tweak it a bit. Throwing the right errors with the right messages 
it usually got right. 

The models had already been created, and didn't quite match
up with the database schema, so I there was some back and forth with that. Flash 
did a good job of helping me with the procedures. I would remove some parameters 
I was expecting, and Flash would adjust the procedure accordingly or suggest changes
or point out default values I had missed.

Once the repositories and the procedures were sync'd up, I was able to start 
on the service and controller layers. GPT-4o shined on the service layer. 
Setting up the repositories' interfaces almost wrote themselves. It was a short and easy
process of verifying code predictions from the implementation. Moving those interfaces
to the service layer was just as straightforward as were those implementations.

The controllers were a bit more work, there wasn't a really good pattern to set up.
There were things that were just different enough that it was easier for me to write them
myself than to try to get GPT-4o or Flash to do it. And really, it felt odd after
having so much help with the rest of the code. 

The last thing I wanted to do was
move all of the validation from the repository to the service layer. I started by
asking Flash to do it on a sample repository, and it did a pretty good job, but I
didn't want to refine the prompt and still triple check the code, so I just did it
myself. That gave me a chance to check that the validation that was written was 
still the validation I wanted, and it was a good way to wrap up the project.
GPT-4o was still able to help move and check the validation and make sure everything
was in order.

Afterwards, I had Flash run over everything and it suggested a concurrency issue
in a procedure that I had missed. I suggested it change one of the statements in 
there to a `Merge` statement (detect a trend), and it did so without any issues.
Overall, it was a very productive session with a smooth flow of AI and code.
No hiccups, no puzzles, just a steady stream of productivity. It wasn't complicated
code, bits were tricky, but nothing incredibly difficult. AI was able to help
*and* stay out of the way. Sessions like this are incredibly satisfying.
