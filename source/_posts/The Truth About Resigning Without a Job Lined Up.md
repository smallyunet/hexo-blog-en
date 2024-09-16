---
title: The Truth About Resigning Without a Job Lined Up
tags:
  - Work
date: 2024-09-16 10:16:23
draft_date: 2024-09-03 12:40:41
---

> Couldn't resist publishing this, haha. I had to vent, sorry.
>
> Just a few days ago, a colleague mentioned in the company group chat, "We are a bunch of traditional game developers who love games. Blockchain has nothing to do with us" (which aligns with how I’ve felt about the company after working here for a year). With that kind of mindset, how can the company possibly succeed in web3?
>
> A new colleague who has only been here for two weeks has already started noticing certain issues...

These things are not appropriate to write in a public blog. In my post "[Resigned Without a Job Lined Up](/2024/07/28/%E8%A3%B8%E8%BE%9E%E4%BA%86/)," the content was vague and superficial. The real reasons for my resignation are tied to specific people and events, and only when these people and events become "the past" can I fully disclose everything.

If I share these details too soon, it might bring consequences such as:
- My superiors might see it and develop a negative opinion of me, which could affect my future background checks for other jobs.
- Potential employers might see it and think I'm difficult to manage or have too many opinions, or worse, that I’m unprofessional for sharing these thoughts after resigning, which could affect my interview prospects or job offers.
- Writing with strong emotions might lead me to use harsh words or exaggerate.
- I might mention work-related specifics, and I need to keep my work identity separate from my real-life identity.

So for now, I'll keep these thoughts in private notes. Maybe a year later, when everything has passed, I can publish them on my blog. This article was written on September 3, 2024. As for when it will be published, I’m not sure.

In short, my resignation was due to a growing disconnect between me and my superior. It wasn't something that appeared on the surface—no verbal or physical conflicts. It was just something that had been simmering inside me for a long time, until one day I decided to resign without a job lined up—what I see as the most dignified way to exit.

If I’ve been enduring painful work for a long time, what exactly was the work that caused this pain? And what was the final straw?

### "Take Another Look" (Part One)

The phrase "take another look" left a lasting impression on me and triggered my decision to resign. What was the context in which it was said?

- Him: There's an issue with the software—it’s behaving unexpectedly.
- Me: The cause of the issue is... the impact is... the solution is...
- Him: Take another look.

My inner thoughts: What am I supposed to look at? I’ve already explained the cause, the consequences, and the solution. What am I looking at? I could look for 10 more minutes or even 10 more days, but my conclusion wouldn’t change. What am I supposed to be looking for? I've already said everything I could!

Here’s a simpler example:

- Him: What's 1 plus 1?
- Me: It’s 2.
- Him: Take another look. We need to confirm what 1 plus 1 equals.

What am I looking for? Doesn't 1 plus 1 equal 2? Do you have a different answer? If it’s not 2, tell me I’m wrong. If it is 2, tell me I’m right. No matter how long I look at it, 1 plus 1 will still be 2 in my perspective. What am I supposed to look for after I’ve already given the answer and drawn my conclusion?

I’ve encountered the "take another look" situation many times, and each time it leaves me speechless. I always feel like "I don’t understand what you’re saying."

### "Take Another Look" (Part Two)

Another meaning of "take another look" is that I feel like I’m wasting time, not knowing what to look for.

A specific situation involved deploying a USDC contract on the network, where the key point was that I wouldn’t control the owner or admin privileges of the contract. To me, it was just a matter of deploying a contract, but I kept getting told to "take another look." Look at what? This contract would ultimately be managed by him, calling specific functions for certain operations, so what am I looking at? Am I just looking for fun? Am I supposed to explain it after looking? He wouldn't believe me anyway, so why not just do it himself?

### Software Benchmark (Part One)

The second-to-last straw was a task related to testing the performance data of some software. I didn’t even put this on my resume because I felt like I didn’t really do much—it wasn’t worth mentioning.

The task involved testing the speed of five different Ethereum clients executing EVM instructions, comparing the gas consumption per second. Up to this point, it wasn’t a problem—performance benchmarking for EVM is meaningful.

But what I couldn’t understand was that the original intention of this task wasn’t to focus on EVM performance but to dig into specific functions in a client, comparing performance differences in code structure, how much CPU was used, or what architecture’s instruction set was employed. To him, physical machine performance metrics were crucial.

Why do I find this confusing? Because such deep focus on low-level performance has nothing to do with blockchain. Any software, regardless of whether it’s written in Go, Java, or another language, can be benchmarked for performance, and optimizations can be made at the CPU, memory, or instruction set level. But none of this relates to blockchain.

So what performance metrics are relevant to blockchain? Let me give a few examples:

- TPS, the elephant in the room that everyone sees but no one optimizes because it’s too hard.
- Transaction acceptance speed, transaction pool capacity and performance.
- Block packaging speed, efficiency, and delay (fetching transactions from the pool).
- Consensus voting speed, efficiency, and delay (if consensus involves voting).
- Recovery speed after network soft forks.
- The efficiency of syncing blocks for light clients.
- The speed and performance of verifying transaction balances (SPV direction).
- And so on.

In my view, there are plenty of performance issues in blockchain that are worth focusing on. Yet my superior chose a direction completely unrelated to blockchain. And the upcoming work, potentially lasting several months or even a year, would revolve around this irrelevant benchmark. I couldn’t bear it anymore and couldn’t continue working under him.

The reason behind this choice, if I may be a bit harsh, is that he doesn’t understand blockchain development. The entire company is a game company, and he himself comes from a game development background. He doesn’t have the technical foundation in blockchain and doesn’t approach problems from a blockchain development perspective.

If someone who deeply understands blockchain chose to work on something unrelated to blockchain, I could accept it because there would likely be a deeper reason behind the choice. But when someone who doesn’t understand blockchain chooses a task outside the field just because it’s their expertise and the results are predictable, it’s hard for me to accept.

To put it simply, crypto users only care about two things: Can fees be lower? Can transactions be faster?

The current state of blockchain isn’t mature enough to compete on who uses 2GB less memory. If a chain has advantages in every other aspect, but just uses slightly more hardware resources, no one will fault it. On the other hand, if a chain underperforms, even if it’s efficient in hardware use, how many developers and users will it attract?

### Software Benchmark (Part Two)

Did I express my thoughts about this situation? Why did I choose to resign instead of discussing it with my superior and persuading him to work on areas I suggested? If I couldn’t persuade him, does that mean I was wrong?

First, I could be wrong. But have you ever felt that it’s difficult to make someone who doesn’t understand your expertise grasp what you do? And even more so, it’s nearly impossible to get someone who doesn’t understand to think from your perspective.

In my daily work, I frequently felt two things: 1. His mindset was different from mine. 2. He didn’t trust me. So, I eventually gave up trying to change things.

For instance, I suggested using ENS more extensively. The company had a strong user base of gamers, and I thought it would be interesting to tie game identities to ENS. Vitalik once said ENS is Ethereum’s most successful non-financial DApp, and Base recently launched an official ENS service. Many wallets have integrated ENS as well. So I thought it was worth exploring. My superior’s response? "Even in web2, only a small number of users need domain names." And that was the end of it.

Another example: I proposed using DAO more, given the strong user base of gamers. DAO seemed like a perfect fit, with a real user base to experiment with. ATOM integrated governance proposals based on token holdings directly in their wallet, and just two days ago, Cardano’s "Chang" upgrade’s biggest selling point was "on-chain governance." DAO is an interesting concept, but my superior dismissed it as an unworthy focus.

I also suggested adding consensus layer voting efficiency metrics to the benchmark. His response? "We don’t care about that." (From this, it’s clear that work priorities were driven by his personal interests, not company needs, industry demands, or technical logic. Of course, he’s the boss/CTO/co-founder, so he gets to decide what to do.)

Given all this, I no longer expected him to take my suggestions seriously. Instead, I chose to resign.

### Software Benchmark (Part Three)

Another issue was the Solo Staker situation. The network used Ethereum clients, so it had the same Solo Staker problem. His approach was to double the network’s native staking yield, increasing it from 12% to 24% to encourage more Solo Stakers and make the network more decentralized.

When staking first launched, some in the community questioned why the yield was only 15% (at launch). His attitude was, "That’s already high enough. No need to argue with those people."

I suggested holding an event to offer a one-time reward for new stakers, as the yield alone wouldn’t attract many people. Maybe an event could help retain users or at least get people to try it. The result? Since it wasn’t his idea, nothing came of it.

Is a 24% yield high? It’s decent. Polkdot’s wallet staking yield is 17%, and Cosmos’ wallet staking yield is 15%. These numbers are after fees are deducted, and no special skills or hardware resources are required. Of course, smaller coins offer higher yields, but DOT and ATOM prices are much more stable. As a crypto user looking to earn some interest, which method would I choose?

This issue isn’t that important in itself. The important point is that he’s not a crypto user, and I suspect (without confirmation) that he has no experience with crypto trading. Compared to traditional finance, a 15% return is indeed high, but this is the crypto industry, where Bitcoin can fluctuate 10% in a day, let alone smaller coins. Given this context, someone who’s not a web3 user can’t be a good builder in the web3 space. This logic is similar to the complaint that many blockchain game companies can’t create good games because the people behind them don’t enjoy playing or understand games. How can someone who’s not part of the web3 world build a better web3?

### Trust Issues (Part One)

In day-to-day work, there was another long-standing frustration: when a problem arose, his first instinct was to doubt me, not the software.

For example, I said that using parameter A in a software would cause it to behave abnormally and recommended not using it for now. When I informed him of the issue, his first reaction wasn’t to accept the advice but to doubt it: "Really? How did you operate it? What’s the specific log?"

Other times, I pointed out issues here and there, and he would immediately say, "That’s impossible. Such a big piece of software—it must be your usage that’s wrong."

I wondered, setting aside my role as a developer, even as a regular user, if I follow the documentation to use an open-source software and encounter a problem, is that my fault? Shouldn’t the software issue warnings for potential user errors? Shouldn’t it have error handling for user misconfigurations? If it’s a logic flaw in the software itself, that has nothing to do with me.

What often happened was this: I would say that after steps A -> B -> C -> D, I proposed either solution A or solution B. The problem? He would first doubt me rather than the software. Either he would redo all the steps himself and encounter the same problem, or I would have to capture the entire process and explain it thoroughly. Only after he verified it with additional research would he believe there was an issue, and then we could discuss a solution. Meanwhile, I had already provided solutions from the start.

This working style doubled the time and effort to solve problems. In simple terms, I figured things out once, then he had to figure them out again. The time was doubled, and during his process, I still had to assist with explanations, detailing my operations and understanding of the issue. These redundant steps added nothing to solving the problem, only to convincing him "the software indeed has a problem."

Eventually, I realized that he wouldn’t trust any conclusions I reached. So I stopped thinking through problems or drawing conclusions myself. I also learned that if he asked me to do something, I would keep records of my operations from the start. If there was an issue, I’d just throw screenshots or pinpoint specific lines of code at him, providing as much surrounding documentation as possible rather than drawing conclusions or wasting mental energy thinking about solutions. Since the prerequisite to solving problems was for him to fully understand the issue, why not let him discover it from the beginning? (Maybe this is why he started saying, "Take another look.")

This distrustful way of working had two side effects for me: 1. I didn’t feel any sense of accomplishment. 2. I didn’t feel any sense of responsibility. Since he micromanaged everything and made all decisions, big and small, I had no choice. A lack of accomplishment was one thing, but I also didn’t feel a sense of responsibility. If I wasn’t making the decisions, I didn’t need to take responsibility. If issues arose later, they weren’t my problem. But isn’t that a good thing? The problem is, he wanted me to understand the cause and potential issues, expecting me to think like I was responsible for the outcome, but he never gave me the opportunity to actually bear responsibility. (That’s why when I complain about work lately, I often say, "What does that have to do with me?")

So in terms of trust, I’ve always felt there were major issues. He might say he trusts me, but in my view, it’s a trust in my "diligence" rather than in my "technical judgment."

Speaking of trust, I remember after I submitted my resignation, he tried to resolve the issues and retain me, saying he trusted and appreciated me. In my view, this trust and appreciation were for my "work ethic" and "willingness to work overtime," not for my "technical insights" or "problem-solving abilities." So, is this trust and appreciation a good thing? Maybe.

Let me put it this way: if I had a cow (I was born in the Year of the Ox), and this cow worked diligently without complaint, and I said to the cow, "I really appreciate you and trust that you’ll keep doing a great job," would that make the cow happy? Isn’t that an insult? It’s like calling someone a slave. If the cow invented a machine to plow the field, increasing efficiency and lightening its own workload, and I praised it, then the cow might really be happy.

### Trust Issues (Part Two)

Trust issues combined with the "take another look" situation often resulted in typical frustrations. For example, how do I prove that a particular solution won’t work?

- Him: Maybe if we change the X part of the data, it’ll work.
- Me: The X data can’t be changed.
- Him: Take another look.

Who can understand this frustration? How do I prove that a solution is unworkable? If something works, I can just implement it to show that it works. But how do I prove something doesn’t work? What action do I take to prove that a solution is unfeasible? What am I supposed to "look at" when it won’t work?

### Conclusion

That’s everything I wanted to say. I hope this doesn’t bring any negative consequences. If it does, so be it. I’ll still say what I want to say.

Although I’ve expressed dissatisfaction with the company and my superior, I hold no ill will. In fact, I hope he finds employees who are a better fit for his needs and vision to continue developing the project and network. It just won’t be me.

### Postscript (2024.09.08)

As for the timing of my resignation, in my view, it came rather suddenly. The logic behind it is that I submitted my resignation around the time of my one-year work anniversary.

I believe the company had some level of trust in me from the moment I joined—otherwise, they wouldn’t have hired me. I’ve repaid that trust with at least a year’s worth of work. Whether it was worth it or not depends on how the company sees it. I feel I’ve done everything I could, and there’s almost nothing left for me to do here. Resigning was my only option.

Ultimately, listing out all the reasons for my resignation is just a way of justifying the decision. Whether it’s the right or wrong choice, I don’t know. The decision to leave isn’t in question. Whether or not it’s "resigning without a job lined up" doesn’t really matter either. The uncertainty lies in my judgment of the company’s future (in the web3 direction).