---
title: A Response to a Layer 2 Startup Idea
tags:
  - Layer2
  - Entrepreneurship
date: 2023-07-08 13:25:31
draft_date: 2023-07-08 02:15:28
---

> Note: I have no intention of disrespecting the letter. The viewpoints on L2 in the email have given me a lot of inspiration. Since some of the content does not completely align with my understanding, I have seriously considered and thought about the meaning of the letter. I would like to express my views on L2 here. As there is little L2-related content on my blog, I'll put it up here to purely discuss technical issues.

### The Letter

> Hello, are you still looking for a job?
> 
> Jul 6, 2023, 5:39 PM (2 days ago)
> to me
> 
> Hi Wangyu!
> I came across your blog by chance and am not sure if this email will reach you.
> L2 has become quite popular this year. Various large companies, as long as they have some money, are starting to focus on > L2.
> According to L2beat, there are quite a few L2 projects about to launch: https://l2beat.com/scaling/tvl#upcoming
> It's unclear whether all L2s are just copying each other. But it feels like the current L2 technologies mostly come from > the open-source codes of a few leading companies.
> If this is true, does it mean anyone can fork a version themselves?
> If so, then we are that "anyone" :)
> Our startup team is quite interested in jumping on the L2 public chain "trend," and it seems you are an expert in this area.
> 
> If you are also interested, we can discuss collaboration methods further?
> My general idea is remote, with you freely leading the chain's evolution.
> 
> PS: My telegram is @******. Feel free to contact me online.


### Response

Hello!

I am very pleased to receive your email, and I apologize for not responding promptly.

I tried to carefully understand the content of your letter. Regarding some of the points about L2, I want to describe my understanding.

#### 1. The Difference Between "Technology Types" and "Projects"

There are 4 types of Layer 2 technologies: State channels, Sidechains, Optimistic rollups, and ZK rollups. All projects fall within these 4 types. Among the upcoming projects, most are of the rollups type, but there is no information on whether their code is forked from existing projects.

<img src="a.png" width="50%" />

There are specifically these 4 projects:

<img src="b.png" width="30%" />

However, from the list of launched projects, even if they fork the same project, their application scenarios are highly customized and vary greatly:

<img src="c.png" width="50%" />

#### 2. Existing Projects Are Not Simple Forks

Take zkSync as an example. Three projects have this label in the list:

<img src="d.png" width="30%" />

They are zkSync Era, zkSync Lite, and ZKSpace. zkSync Era and zkSync Lite are from the same company.

Looking specifically at ZKSpace, it indeed uses zkSync's contracts but is not simply a fork of the entire project. Instead, it uses zkSync's contract code and modifies it to fit its own business. Additionally, ZKSpace also uses code from other projects.

<img src="e.png" width="50%" />

From ZKSpace’s (formerly ZKSwap) whitepaper, we can see that ZKSpace aims to be Uniswap on Layer 2, including AMM functions, which are those of a market maker.

<img src="f.png" width="60%" />

zkSync is a general Layer 2 project that does not provide very specific functions. ZKSpace uses some of zkSync’s contract code to do things that are highly relevant to their business and have specific goals. It does not compete directly with zkSync itself.

Looking at projects with the OP label, which are forked from the OP project:
- Arbitrum initially forked OP, then changed some economic model aspects, and later restructured the project as it grew. Additionally, Arb's operational capabilities are strong, having hosted several Odyssey events.
- Boba’s feature is that it doesn’t require a 7-day wait to withdraw funds; withdrawals are immediate. Moreover, through its own so-called hybrid computing technology, it brings web2 capabilities to the blockchain.
- Zora is a layer2 dedicated to the NFT field.
- Mantle provides decentralized sequencers, among other things.

In summary, these projects all have specific purposes and aim to achieve certain functions by reusing existing code. The focus is likely on the goals of these projects, i.e., what they aim to do. They combine their business scenarios rather than just being able to run by simply forking the code.

#### 3. L2 is Not a Chain

Current rollup projects are centralized. They are not a chain themselves and do not have consensus mechanisms; the project team operates them.

#### Summary

Regarding your idea of developing an L2 project, I mean that, based on your description, it may not be as simple as you think. It might involve specific issues. Innovations in application scenarios, combined with DeFi or GameFi, or technical innovations that can improve certain pain points, might be needed.

I also want to jump on the trend and hope for a suitable entrepreneurial opportunity. I am currently looking for a job, but from my perspective, I am quite confused. I don’t have a direct idea of what direction a project should take; I think those involve quite market-oriented aspects and require significant capital.

I’m not sure about your specific thoughts. As there is a lot of content, I replied via email. Feel free to contact me if you have any questions.

### Supplement

There are some topics not mentioned in the reply, which I will briefly discuss.

First, why is Layer 2 hot? Because Vitalik has frequently mentioned zkEVM in his recent blogs. For example, in the article [Endgame](https://vitalik.ca/general/2021/12/06/endgame.html), he considers supporting ZK-rollup as an important task for Ethereum’s future. Some issues cannot be directly resolved on Layer 1, so they hope to solve them through Layer 2. With Vitalik, a highly regarded developer, leading the technical direction in the ecosystem, Layer 2 has become a development hotspot for Ethereum.

Vitalik was quite optimistic about ZK-SNARK-based zkEVMs. From the perspective presented in this 2023 article, ZK-rollups were the so-called Endgame. At the time, however, ZK development costs were extremely high and the technology was still in research and development. It was too expensive for small-scale investors to pursue.

The Dapp-learning community has a technical sharing video on Scroll on Youtube, which is quite good. The entire Scroll technical team is based in China. From the video explanation, it seems Scroll's technical solution involves some brute-force elements, such as creating a table for a certain type of circuit results and expanding various tables to support the entire EVM. This is how Scroll compares the ways each ZK project implements EVM completeness:

<img src="g.png" width="80%" />

Vitalik’s blog post [The different types of ZK-EVMs](https://vitalik.ca/general/2022/08/04/zkevm.html) also discusses the classification of zkEVMs. Anyway, the hype around Layer 2 should be referring to the heat around ZK-rollup projects.

Optimistic rollups are more like a transitional solution for L2. I believe once ZK rolls out, OP projects will be impacted, as OP does not rely on the reliability of cryptographic technology. From its name, you can see it is optimistic, assuming a transaction has no issues first and then giving a 7-day challenge period. If there are issues, users need to challenge it themselves or third-party verifiers to fully challenge, involving significant human intervention.

Another issue is the centralization of L2 projects. Apart from sidechains, L2 systems are necessarily centralized because consensus is delegated to L1 rather than handled by L2 itself. An L2's role is to collect transactions quickly, batch them, and submit them to L1. The transaction ultimately derives its trust from L1: users trust the record on L1, not the L2 operator. From this perspective, neither users nor project teams require the L2 itself to be decentralized. L2s are generally launched and operated by project teams, and users deposit assets only when they trust the project's technical design.

There was also the issue of L2 user experience. At the time of writing, few users used OP or ARB for low-cost asset transfers. They preferred BNB or TRX for pseudonymous payments. More common uses of L2s were investing in governance tokens or interacting with DApps. OP's seven-day withdrawal period was inconvenient, and moving assets between L2 networks was another problem. For example, could USDT on OP be transferred to ARB? If I used OP and another person used ARB, we could not transact directly. There was still considerable room for improvement.

L2 is a promising technical direction, and we look forward to it bringing us a better user experience. As for whether there are low-cost profit opportunities, that is uncertain.
