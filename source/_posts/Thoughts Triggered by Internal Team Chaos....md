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

<br><br>

### Supplement (2025.04.20)

Let me explain what the term “Incomplete Scrum” style means. The content above may look like an open-ended conclusion, but in reality, it's expressing dissatisfaction with the Scrum style—just subtly, because I was still employed at the time and couldn’t say it too directly.

Original Scrum Agile development emphasizes equality among team members. The product manager sets the priorities, but how tasks are divided and executed technically is entirely up to the team members. The team truly collaborates to achieve shared goals.

In original Scrum Agile development, there is no such role as a manager (Leader). There’s only the product manager and the technical team. Technical work isn’t assigned by a leader, but decided through team discussions. This ensures that everyone feels involved, and upon task completion, there’s a strong sense of achievement and belonging. The Scrum Master is more of a servant leader whose main job is to help team members solve technical issues and ensure the team follows the Scrum process—not to manage the team.

“Incomplete Scrum” refers to situations where a leader is unwilling to give up their leadership status and authority over the team, yet still wants to use the Sprint model from the Scrum framework to manage development tasks.

It’s essentially an attempt to combine the benefits of traditional waterfall-style management with those of Scrum. This approach, however, has proven to be very problematic.

In a Scrum framework, if Sprint tasks are planned and assigned by the leader instead of being self-organized by the team, it leads to a loss of responsibility and ownership among team members. “The tasks are already planned by the leader—why should I care?” So people end up just playing the role of a code monkey. And the ones who stay in the team are likely those who are okay with just being code monkeys.

Next, there's the issue of no sense of accomplishment. A task usually lasts two or three days. Once one task is done, it’s on to the next. The previous task is completely forgotten unless a bug pops up. This feels more like outsourced work. The only reason to revisit a task is to fix bugs. Optimization? Forget about it—only someone with no programming experience would think that's unnecessary. I often feel the faster I work, the more tasks I get, which means higher efficiency leads to more work.

Then there’s the problem with leadership. From the leader’s perspective, assigning tasks daily feels like their job. Once that’s done, they’re at ease. But as mentioned, development often requires ongoing optimization. When is a task ever truly “done” and never touched again? Leaders don’t seem to get this. Once a task is marked “complete,” if I later say I optimized something, the response is confusion: “Wasn’t that requirement already closed?”

Next up is testing. We don’t have a dedicated QA person in our team. Strangely, our leader seems to think testers aren’t necessary at all. Even integration testing is seen as unimportant—the only thing that really matters, apparently, is unit testing. Remember I said a task is considered “closed” once the code and unit tests are written? What about integration testing? Never heard of it. What happens in this kind of setup? Well, code gets deployed to the public testnet without any integration testing at all. This has led to frequent deployment accidents—something like 4 out of 5 deployments causing testnet crashes.

Finally, there's the issue with the Sprint planning spreadsheet. Currently, the rule is one task equals one PR. Just from that sentence, doesn’t it sound amateurish? How can it always be one PR per task? PRs can be large or small. For example, there might be a typo in a command-line description—just one line of code. Do I really need to go through the whole Scrum spreadsheet process for that? If I do, I’ll need to mention it to everyone in the workflow, filling in multiple fields like which project it belongs to, priority level, reviewer, etc. For a typo fix? Not worth it. But if I don’t fix it, am I just going to let that issue stay there forever? So in practice, people often sneak unrelated optimizations into a proper PR, which makes the Scrum tracking sheet a complete mess to use.
