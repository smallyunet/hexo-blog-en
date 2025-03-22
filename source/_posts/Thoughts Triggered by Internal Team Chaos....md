---
title: Thoughts Triggered by Internal Team Chaos...
date: 2025-03-22 20:23:47
tags: Work
---

First, I recommend two blog posts by Yin Wang:

- [How to Respect a Programmer](https://www.yinwang.org/blog-cn/2015/03/03/how-to-respect-a-programmer)
- [Git Etiquette](https://www.yinwang.org/blog-cn/2015/03/11/git-etiquette)

I revisited these two posts today and felt quite relieved that in my daily work, I rarely, if ever, unintentionally violate the etiquette issues mentioned by Yin Wang.

What reminded me of this topic was a small incident at work, which I found quite interesting. It started when I merged some code without getting it reviewed first. My colleague then criticized my code in the group chat—perhaps he felt that merging code without his review hurt his sense of dignity and status within the team.

I'm currently working on a web3 development team with only four or five people in total, but the internal management is extremely chaotic—something I haven't encountered in my previous jobs. But to be fair, I don't have much past work experience, so there's not much reference value, and I can't draw any useful conclusions.

If I had to compare, the biggest difference between my current and previous jobs lies in the granularity of task allocation, or rather, the scale of responsibilities assigned to individuals.

For example, when I worked at [Onchain](https://www.linkedin.com/company/onchain/), I needed to develop Solidity contracts to support our distributed file system’s compatibility with EVM chains. This task was entirely on me. Although someone reviewed the code, the entire [contract repository](https://github.com/saveio-backup/savefs-contracts) only had commits from me (not emphasizing the number of contributors here). In such a setup, naturally, there were no issues like the current ones related to code quality.

(Onchain has been dissolved. The project's code wasn’t open-sourced at the time. The current repo is a personal backup I made. It’s been two years—shouldn’t be a big risk now, I guess.)

Another example is when I worked at [Fusionist](https://ace.fusionist.io/). I single-handedly launched and maintained the entire [Endurance Network](https://explorer-endurance.fusionist.io/), without any issues. A professional DevOps colleague helped during the mainnet launch with server setup, Docker configuration, etc.

(I've left that job, so I think it's okay to mention this.)

Even in earlier work experiences, whether it was me or my colleagues, the general feeling was that everyone was responsible for a specific area of business, not just for individual development tasks. Responsibilities were relatively clear, and tasks typically spanned weeks to months. As a result, management chaos wasn't something I encountered.

The current work model is a somewhat incomplete version of Scrum. Each task has a development cycle of two to three days—basically small dev tasks. These tasks are not strictly divided by business lines (or code repositories), and developers randomly jump between different things. Everyone is responsible only for their own development task (PR), not for the entire codebase. In this model, every task has a different person in charge, which creates a sense of chaos.

By contrast, in my previous jobs, when someone left, a long handover process was needed, because the responsibilities were significant. Now, if someone quits, there's really nothing major to hand over—just two or three days' worth of work at most. I'm not sure if it's because of the remote work nature that forces companies to adopt this kind of management style.

My boss once summed up this difference in work models as a contrast between Chinese-style and Western-style management approaches.

I'm just an ordinary developer with no management experience. I don’t understand these management styles, nor do I want to or have the opportunity to think deeply about them. But now I’m starting to feel that management itself is a discipline—a rather big one...
