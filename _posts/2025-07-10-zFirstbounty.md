---
title: "From Confusion to First Bounty💸"
date: 2025-07-10 00:00:00 +0530
categories: [Bug Bounty]
tags: [Pentesting, bug bounty, blog]
---

<!-- # From Confusion to First Bounty💸 -->

### 🙏 A Big Shoutout to the Mentors and Fellow Raiders
Before I dive into the story, I want to take a moment to acknowledge the amazing people who helped shape my journey :

**Mentors :**
[Shreyansh](https://www.linkedin.com/in/shreyansh-desai/) , [XXSRAT](https://www.linkedin.com/in/wesley-thijs-8b384828a/) , Kandarp , [Tehan](https://www.linkedin.com/in/mohammadtehan/) , [Hemantsolo](https://www.linkedin.com/in/hemantsolo/) — Always ready to guide, explain and even jump on a call when I was stuck.

**Fellow Raiders :**
[_b3ludan_](https://www.linkedin.com/in/b3ludan/) _,_ [_Soura_](https://www.linkedin.com/in/sourajit-mukherjee-915060266/) — from late-night hunts to sharing logic and payloads, hunting with them became a routine full of learning.

### Where It All Began
It started with a spark — a vague curiosity about hacking and bug bounty programs. I didn’t have any knowledge, no roadmap … just the Internet and an itch to learn.

### Self-Learning and Chaos

The early days were a mess. Every new term felt like a puzzle : XSS? SSRF? What even is HTTP parameter pollution?

Doubts? Hundreds. But I didn’t stop there — I let my doubts fuel deep dives into “Why does this happen?” and “How does this even work?”

> “You don’t really learn to swim until you’re in the deep end.”

I was learning slowly and figuring things out on my own… until I came across a tweet by [Het Mehta](https://www.linkedin.com/in/hetmehtaa/) about bug bounty training. That tweet connected me with Shreyansh — someone who became more than just a guide. **_A GURU_**, he patiently broke down not just **“what”** but the **“how”** and **“why”** behind every vulnerability. **His guidance helped transform my scattered curiosity into structured learning.**

Hemantsolo’s guidance gave me deep insight into core web vulnerabilities like  Broken Authentication, Injection Methodologies and CSRF. He’s someone who truly stands out for his **practical teaching style** — breaking down complex concepts into real-world scenarios. His ability to teach through **real application logic** and not just theory played a huge role in how I approach bugs today.

### Community Is Everything
I started actively hunting and engaging in [XXSRAT’s Discord server](https://discord.gg/T5G7QVtG) **_(_**[**_https://discord.gg/T5G7QVtG_**](https://discord.gg/T5G7QVtG)**_)_** — a space buzzing with real hunters who were consistently reporting bugs, sharing insights, and discussing live targets. I had live hunting sessions with XXSRAT where I got to observe and learn practical exploitation techniques, especially around IDOR and BAC workflows and even reported bugs with him and b3ludan. That’s where the thrill began!

To top it off, **Cyber Crusade** sessions on **BAC and IDOR** really helped solidify those concepts — and they paid off in the field.

> **_XXSRAT_** _is a_ **_phenomenal mentor_** _— always ready to share, explain, and lead by example. Our_ **_long hunting sessions and late-night hangouts_** _weren’t just educational — they were pure adrenaline. Watching him break things down in real-time, testing edge cases and thinking creatively gave me a whole new perspective on bug hunting._
> 
> _During one such session with b3ludan, we were testing a common program and stumbled upon a classic IDOR and BAC issue. We cross-checked IDs, tested different access levels — and boom! I finally understood IDOR and BAC at a core level._

The coordination with **b3ludan** kept growing — we started **hunting regularly together**, discussing logic, learning from each other, bouncing ideas, testing, verifying.

Along with **b3ludan & Soura**, we submitted several bugs. Things started feeling easier… until triage **hit us hard** 😅.

> **During one of our most intense streaks — a so-called “raid” 10 hours a day for 13 straight days — we submitted 13 reports in a single run.**

The outcome?  
🟢 **6 Accepted**  
🟡 **2 Duplicates**  
🔴 **4 Rejected  
🔵 1 Informative**
_Bounties We Earned in Raid : $6k++ in 2 weeks_

**Lessons? Countless.**
Not every report was a win, but each one deepened our understanding, sharpened our instincts, and made us better hunters.

### The Push for Validation
Two of our reports got marked **N/A (Not Applicable)**.  
We didn’t accept it.  
With solid proof, deeper explanations, and additional PoCs, we **argued our case professionally**. And guess what?  
**Both bugs were updated to HIGH severity** — and received  
**first bounty : 2x$300**

> _One of our_ **_most satisfying wins_** _came just a few days ago.  
A report we submitted was initially marked as_ **_“Out of Scope.”_** _Instead of letting it go, referred the triage team to_ **_their own documentation_**_, highlighting the exact section that validated our finding.  
The triagers reconsidered — and the report was_ **_accepted with a $472 bounty_**_._

That day, we didn’t just win a bounty, We learned the **real game** :
> _“Finding bugs is half the battle. Proving their impact is the real test.”_

### Tips from the Battlefield
-   Don’t give up when reports are marked N/A. Learn to **communicate impact**.
-   Always keep a **clear PoC**.
-   Be polite but **persistent** with triagers.
-   Have **deep knowledge** of your finding. It’ll help in defending it confidently.

> _Be confident, but reasonable. Know your exploit well, explain it clearly, and be prepared to prove the impact._

### Don’t Just Read — DO It!
Every time I had doubts, I _forced_ myself to practically explore that exact bug or vulnerability — **again and again** — until it made sense. That’s when I noticed real improvement.

> _More doubts? More practice. More confusion? More testing. More clarity._

The more you hunt, the more those concepts sink in. You don’t just memorize — you **understand**.

_Things didn’t truly click until I actually started applying them._
-   Read about XSS? Try it on a lab.
-   Saw a video on IDOR? Go hunt for it on a real program.
-   Confused about SSRF? Build a local vulnerable lab or test it live (ethically).

### One of the biggest level-ups in my learning came when I started **building my own stuff** — even if it was super basic.
To understand how APIs work, I created a simple API lab [**_https://tarkash.surapura.in_**](https://tarkash.surapura.in/) — nothing fancy, just endpoints, token generation and protected routes and **that changed everything**.

I finally began to **see the flow** :

-   How **tokens** (JWTs or random) are generated post-login and validated on each request.
-   What happens when you send an `Authorization: Bearer <token>` — how the server checks and grants access.
-   **Common dev mistakes**: missing auth checks, expired tokens still working, leaking tokens in logs, and broken role validations.

> _If you want to go deep, don’t just test — build, break and understand._

[**_https://github.com/ikajakam/learn-api-testing_**](https://github.com/ikajakam/learn-api-testing)

### 📞 Hacking Tips + IRL Support
Always have someone in your circle who’s willing to listen, guide, or jump on a quick call.
For me, that has always been **Shreyansh** — a to-go support system for all my doubts, logic checks, and motivational push.

> _From “Bro, is this a bug?” to “Bro, this got accepted!” — he was just a call away._

### A Big Kudos to b3ludan & Soura

A **massive shoutout** to **b3ludan** and **Soura** for their **continuous dedication**, **sleepless nights** and **lethal hunting** skills.

From deep logic analysis to testing edge cases at odd hours — they’ve been absolute beasts on the field. Hunting alongside them wasn’t just productive — it was **next-level fun and full of adrenaline**.
