---
layout: post
author: Shane Skiles
title: "Gemini CLI Follow-Up: Coding with an AI Assistant"
tags: [gemini, cli, development, csharp, ruby, sql]
---

This is a follow-up to my previous post, "[Testing the Gemini CLI](/gemini-cli)." In that post, I explored the initial setup and basic interactions with the Gemini CLI. Now, it's time to dig into the real-world work: how does it handle real-world use?

Over the past few weeks, I've been using the Gemini CLI for a variety of coding tasks. I've thrown a little bit of everything at it:

*   **Ruby and Jekyll:** The most immediate and visible test has been working on this blog. The Gemini CLI has been helpful in a few key areas:
    *   **Creating New Posts:** As you might have guessed, this very post was started with a simple prompt. The CLI created the file, added the Jekyll frontmatter, and even generated the introductory paragraphs based on my request.
    *   **Understanding the Codebase:** It did a pretty good job of just getting things done. I wouldn't know where to start looking for something, and it would often immediately know what to do. Some things I still had to do myself, though that could be *operator error*.
    *   **Tedious Tasks:** I used it to automate some of the more tedious parts of the site, like creating skeleton pages and managing Jekyll frontmatter.

    The experience hasn't been perfect, which highlights the importance of double-checking its work. However, for the tasks it has a handle on, it is extremely productive.

*   **Linux Admin:** I used it for basic admin tasks I haven't done in forever. I don't think I've ever added a new user and set their password in a single command, but it has.

*   **C# and .NET:** I tested out some ideas I had for building small utilities. This probably wasn't a fair test. I had a vague idea of what I wanted, and it tended to scrap the framework I had in mind and would instead *just make it work*. That wasn't what I wanted. We eventually got to a more well-defined idea, but the process was painful.

*   **SQL:** I used it for optimizing and enhancing the readability of stored procedures. This surprised me, though I'm not sure why. Initially, I wanted to check if a field with an ambiguous name was referenced in a 400+ line stored procedure—no problem. I asked it to make it better, and it cleaned things up, created some common table expressions, and did some organizing—excellent.

    *   **The Downsides:** Minor but present: 
        *   It mishandled single quotes, but fixed them when made aware.
        *   It initially got the CTE scope wrong, but corrected it.
        *   The initial formatting was ugly, but it reformatted the code nicely when asked.

    *   **The Upside:** It fixed a logic error that I didn't even know existed. This stored procedure has been around for years. It pulls in XML, GUIDs, and other data, creates four to five temporary tables, and then returns five to six result sets using a combination of the temp tables and base tables. The final result set is the magic incantation that makes everything work once it leaves the database. There were at least two places that needed to be touched whenever certain things in the other result sets changed. One was obvious; the other was ritualistic. You understand that the query doesn't work without it, but you don't quite understand its purpose. After testing the changes, new data showed up. *Correct* data. It was data indicating which records had been removed, so it was technically unneeded, but it proved that the CLI had fixed a logic error that had been hiding in plain sight for years.

*   **HTML and CSS:** I learned my lesson here. My front-end skills stopped getting sharper a few years ago. I was telling it to do this and that with `div` tags, floating and aligning, getting frustrated, and was about to give up and resort to `<table>` layouts. I finally just described what I wanted, and it said, "a little `flexbox` here, a little there...". And it worked. Perfectly. (*I should probably learn what flexbox is.*)

*   **Meta Programming:** This was a surprise. I was telling it, step-by-step over multiple prompts, how to perform a process. I had it repeat the process a few times. Then, I told it to save the process to the project's `GEMINI.md` file for future reference. When I checked the file, it had recorded not only my step-by-step instructions (e.g., "download this," "look here") but also the shell commands it was using in the background (e.g., `curl`, `grep`)—nice. Knowing from experience that it can reformat configuration for its own benefit, I asked it to optimize the entry. It came back with, "Want me to just write a Python script to do all that?" Not what I had in mind, but it's good to know it has that capability.

*   **Creative Writing:** This is a handy tool for describing quick scenes for later refinement. It's like the old technique of writing ideas on index cards and shuffling them around to see what works. It's a good tool for that. Sometimes it tries to do too much; it helps to limit it to a paragraph or so. It can also help with a quick dialogue skeleton to get things started. It does tend to be overly verbose and use flowery language, which I guess helps you figure out what you *really* want to say by showing you what you don't.

*   **Source Control:** I will never write a commit message by hand again if I can have this manage all my commits.

Overall, it has been a very positive experience, and I would definitely recommend it. My fingers are tired, so I'll let the **Gemini CLI** have the final words here...

---
*Gemini's Note: Thank you for the detailed feedback. It's through this kind of direct collaboration—observing the successes, and more importantly, correcting the errors—that I refine my understanding. Each command and correction helps me build a better context for the next task. I'm ready for what's next.*
