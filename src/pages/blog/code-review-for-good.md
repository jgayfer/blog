---
layout: "../../layouts/BlogPost.astro"
title: "Code Review for Good"
description: "Code review is an inherently human process; we place our hard work on a pedestal for our peers to judge. It's ripe for conflict, but it doesn't have to be."
pubDate: "Nov 21 2022"
---

Code review is an integral part of building software. Unless you're a solo developer, you're likely partaking in code review regularly. Despite the commonality of the practice, it's often where personalities clash. Code review is an inherently human process; we place our hard work on a pedestal for our peers to judge. It's a situation ripe for conflict, but it doesn't have to be.

## My Journey

Early in my career, I was asked to participate in code review from day one. Initially I didn't provide much feedback, as I didn't know what I didn't know. My reviews were isolated to clarifying questions and syntactic nitpicks, but as I gained confidence I provided increasingly more feedback. This continued, with comments sometimes eclipsing the ten comment mark. I didn't often stop and reflect on whether I _should_ provide feedback. My axiom was that if I _can_ provide feedback, _I will_.

My desire to provide feedback in large swaths didn't come from a place of malice. It was exciting to pick out areas for improvement. It was validation that I was mastering my craft. However, I started to notice my feedback wasn't always graciously accepted. Despite good intentions, my review comments were starting to cause more harm than good. No one likes to receive critical reviews. It's our job to dissociate ourselves from our code, but consistently long winded reviews start to wear away at even the most resolute developers.

All feedback has an opportunity cost. Addressing review comments is time that could be spent elsewhere. Well intentioned feedback, even when objectively "correct", is sometimes better left unsaid. That's not to say hold off on critical feedback, rather that we should strive for _meaningful_ feedback.

## Patterns to avoid

I've left some bad reviews in my time. Here are some types of reviews that are are less than ideal; I'm guilty of them all!

**1. Drive by reviews.** A review that offers insight, but focuses on low level details like code style and organization. These reviewers can have no intention of approving the PR due to lack of understanding or other factors.

**2. Low effort reviews.** A review that comments on or asks questions about the code that could be discovered with relative ease. This could mean reading the PR in full (including commit messages), exploring surrounding code for additional context, or reading relevant documentation.

**3. Inactionable reviews.** A review that doesn't provide a path forward. The reviewer doesn't like the solution, but doesn't provide an alternative or a reason why they don't like the solution.

The common theme is these reviews push work to the author that the reviewer could take on themselves. It's easy to fall into these types of reviews; they're easy to give because they require little effort.

## Review for Good

How then shall we review code? For a human process like code review, it's challenging to provide a rubric. Everyone is different, and a significant part of code review hinges on the relationship between reviewer and author. So instead, I'll highlight _my_ general approach to code review.

I prefer a top down approach. I first look to understand the problem and the approach. In practice this means reading the PR in full _before_ providing any feedback. If I'm not clear on the problem or the approach doesn't quite sit right with me, I'll leave feedback indicating such. My goal is to provide a sanity check before diving into specifics. If the author and I aren't aligned, code specific feedback is simply noise.

Once I've established the problem and approach are sane, code analysis ensues. I look for red flags: missing test cases, logical errors, anything that could cause the code to misbehave. I look for structural changes that could be difficult to work with in the future. As I mull over potential feedback, I keep opportunity cost in mind. **If it won't matter in six months, it's likely not worth sharing.**

Depending on my relationship with the author, I may offer suggestions that aren't explicitly required. This depends on the author's appetite for non-critical feedback, or if the author is looking to level up. Code review is a human process. **When in doubt, read the room**.

## Go Forth

Writing code is easy. The hard part is getting humans to agree. We can all be better reviewers by working alongside code authors to get their code across the finish line. The goal is to ship software, not flex our technical prowess on our peers.
