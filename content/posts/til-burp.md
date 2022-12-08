---
title: "TIL: Burpsuite"
date: 2022-12-08T23:13:00+08:00
description: "In praise of professional software."
tags: [TIL]
---

## About: TIL
TIL stands for "Today I Learned," 
and I plan to write a TIL post whenever I discover something interesting.
It could be related to anything: from technology to life.

This is inspired by [100daystooffload](https://100daystooffload.com) and various TIL posts I found over the internet.
This can motivate me to keep finding interesting things to learn and experience, 
improve my writing a little bit, and, most importantly, be an intriguing read.

## Burpsuite
Burpsuite is a professional software for web application security testing.
I heard of it immediately as I started learning web security from sites such as [hackthebox](https://www.hackthebox.com).
However, it wasn't until today that I actually got the time to actually sit down and try it out,
as the complex interface and the new terminologies intimidated me. 
If devtools, postman, and some python scripting work, why complicate me with a new tool?

Today, I had an hour to spare, so I decided to try it.
Upon watching the [tutorial from hacker101](https://www.youtube.com/watch?v=LSqC9qgEMi0&list=PLxhvVyxYRviajtnHaICLg_ZcY47TpgGjR&index=1), 
I didn't change my mind about burpsuite's innate complexity, but I realized that such complexity is **necessary**.
When doing web CTFs with devtools, postman, and python, I often find myself doing the same thing over and over again:
noticing some noteworthy detail about a request in devtools, 
manually recreating that request in postman by copying and pasting all the parameters and headers,
going to cyberchef for some decoding and encoding,
manually tweaking those parameters until I get the desired result,
then reproduce this request repeatedly with python if I need any form of automation or brute forcing.

Burpsuite, on the other hand,
allows for automatic capture of all requests and responses,
can intercept and modify requests on the fly,
provides a built-in tool for common encoding and decoding tasks,
and provides a built-in tool for enumerating parameters that cover the most common cases.
While burpsuite is still less convenient or flexible compared to devtools, postman, cyberchef, etc., 
it provides a streamlined workflow that is much more efficient than context-switching between different tools constantly
and allows for a much more efficient workflow when dealing with targets of increasing complexity.

Thus, it's safe to say that burpsuite will save me tons of hours down the road on web security-related projects, 
and I'm glad I finally took the time to learn it.
I suppose that a similar argument can be made for all professional software in general:
yes, they do look not very user-friendly and are probably written in Java,
they are harder to learn and use than their counterparts,
but in return, they optimize for the workflows of the professionals they're targeting for,
and in turn, saves an insane amount of time.