---
title: Analysis of the 0G Project
date: 2025-08-06 13:08:00
tags: Project Analysis
---

First of all, I'm not very optimistic about the technical depth of 0G, mainly because it's a project developed by a Chinese team. 0G is in the AI sector and posted job listings on TinTinLand in March. It's likely planning a token launch around September. My guess is that the AI aspect is more hype than substance. I've recently joined a study group on TinTinLand that's collaborating with 0G on community courses, so I got slightly interested and decided to analyze the project.

The official website of 0G is [0g.ai](https://0g.ai/), and it tries its best to flaunt buzzwords like "the next generation," "decentralized AI," "DeAIOS," and "RWA." The more extravagant the terminology, the more suspicious it tends to be.

### Project Background

0G released its [whitepaper](https://4134984757-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FsEYMfeKUqxaOUwhkw6AT%2Fuploads%2Fgit-blob-6f0538c70e09bf3180519342bfc516355c7a12c0%2F0g-whitepaper.pdf?alt=media) in August 2024. Judging from the table of contents and the length, things don't look very promising. The structure is rather simple, with only 20 pages. While page count is a superficial measure (Bitcoin’s whitepaper is only 9 pages), the overly simplistic structure gives a rough-and-ready impression for a project that's supposed to be AI-tech-driven.

<img src="1.png" width="50%">

Let’s look at the abstract. 0G aims to solve transparency issues in the AI model training process:

<img src="2.png" width="70%">

Honestly, seeing the term “modular” gave me a bad feeling—especially when I saw “DA” (Data Availability). I wondered if they were using Celestia. Coupled with the claim on the homepage of 2500 TPS, what chain can even achieve that? Cosmos seems like a candidate. But at this point, it's still unclear what the “8K validators” mentioned on the homepage means—Cosmos can't do that.

<img src="3.png" width="80%">

Thankfully, it's not Celestia. The whitepaper doesn't detail the tech stack but clearly positions itself alongside Celestia. They've built their own chain called 0G DA.

The whitepaper explains the PoRA (Proof of Random Access) mining mechanism in detail. This is where there's actual technical depth. Unlike Filecoin's cold storage model, 0G Storage emphasizes on-chain data that’s immediately accessible. It sets an 8TB mining window, requiring miners to quickly verify data integrity within this range.

PoRA’s limitation lies in its inability to prove the uniqueness of stored data. It verifies whether miners *have* the data but not whether it's *unique*, unlike Filecoin’s PoRep. This aligns with 0G’s focus on data availability rather than proof of replication. Its economic model assumes that bad actors can’t profit, so the mechanism works under these assumptions. Filecoin, on the other hand, rewards based on computing power and faces different challenges.

From the project's first [blog post](https://0g.ai/blog/introduction), we can see more clearly that 0G consists of two key components: 0G Storage and 0G DA. Essentially, it's solving the DA problem and applying this capability to AI scenarios, which is why it falls into the AI category. In essence, it’s a distributed storage blockchain project.

0G raised $30 million in seed funding last year, which shows decent capital backing.

On the engineering side, the [0G Storage code](https://github.com/0glabs/0g-storage-node/blob/main/Cargo.toml#L31) is based on Conflux node code, with additional development layered on top:

<img src="6.png" width="70%">

I won’t delve too deep into the PoRA implementation.

### Project Architecture

So far, we've discussed 0G Storage and 0G DA. In addition, the 0G project includes two other components: 0G Chain and 0G Compute Network. These weren’t mentioned in the whitepaper, possibly because they were added later.

0G Chain is a blockchain node developed using the Cosmos SDK (finally, Cosmos appears!). It uses evmos to ensure Ethereum smart contract compatibility:

<img src="4.png" width="80%">

The last code commit to the 0G Chain repo was five months ago, suggesting that the Cosmos SDK route may have been abandoned. A more active repo recently is [0g-geth](https://github.com/0glabs/0g-geth), which appears to be a Geth fork with added support for 0G DA via precompiled contracts.

<img src="5.png" width="40%">

The 0G Compute Network is the component truly related to AI model training. It currently supports the use of some [pre-trained models](https://docs.0g.ai/developer-hub/building-on-0g/compute-network/sdk#discover-available-services). User interactions are straightforward—similar to OpenAI’s SDK: send a request and get a response. It’s essentially a client-layer SDK.

The nodes providing computing power to train models in the 0G Compute Network are called Providers. The repo is [0g-serving-broker](https://github.com/0glabs/0g-serving-broker), and it includes training code. For example, the [finetune.py](https://github.com/0glabs/0g-serving-broker/blob/main/api/fine-tuning/execution/transformer/transformer/finetune.py) script fine-tunes text models using Transformer. The Docker container uses [pytorch 2.5.1-cuda12.4-cudnn9-devel](https://github.com/0glabs/0g-serving-broker/blob/main/api/fine-tuning/execution/transformer/Dockerfile#L2) as the base image.

So from the LLM training perspective, 0G has some engineering-level technical work. But what 0G is doing is fine-tuning—i.e., training pre-trained models further with smaller compute to perform specific tasks. By contrast, giants like OpenAI or Grok train from scratch with massive datasets (e.g., 1 TB tokens)—that’s pre-training.

For example, if OpenAI were to release a GPT-3 model (not open-sourced in reality), 0G Compute Network could fine-tune it using its own dataset to create a customized version of GPT-3. That’s the idea.

More precisely, 0G Compute Network offers a “training ground” with integrated blockchain-based economic models and incentives. It allows some users to provide compute for fine-tuning and earn rewards, while others can use the fine-tuned models.

The interaction between Providers and smart contracts is straightforward. 0G uses Solidity-based contracts in [0g-serving-contract](https://github.com/0glabs/0g-serving-contract). Contract interactions follow the usual Ethereum ecosystem norms. What 0G needs to do is to write contracts that handle fine-tuning results, task distribution, reward records, penalty mechanisms, etc., and integrate these into off-chain compute nodes.

### Summary

All in all, I have to revise my initial opinion—0G does have some technical substance, particularly in engineering. Whether it's blockchain DA or AI model fine-tuning, both parts are decently executed. The business logic forms a relatively complete loop.

That said, analyzing the 0G project was less straightforward than other projects I’ve covered. The whitepaper and documentation are not very complete, and the technical roadmap isn’t very unified. As a result, there’s no clear top-down resource outlining the full project structure. Still, I believe the analysis above provides a reasonably comprehensive technical breakdown of the 0G project.
