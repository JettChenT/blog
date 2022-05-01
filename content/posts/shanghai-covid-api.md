---
title: "How to reliably get data of Shanghai covid cases"
description: "It's *harder* than you think"
draft: true
date: 2022-04-10T21:23:35+08:00
---

In march of 2022, a new variant of covid has hit Shanghai, 
resulting in a chain of events that eventually leads to 
me being stuck in my room, pondering the question: "can I predict the tomorrow's covid cases with machine learning?"
Then, one thing lead to another, and I was stuck at the first step: *gathering data*.

This is a post about how to scrape html pages with Python, why you should avoid doing that, and [baidu](https://baidu.com)'s **weird** API design.

## Attempt 1: Scraping

