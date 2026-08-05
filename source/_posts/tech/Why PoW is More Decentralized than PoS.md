---
title: Why PoW is More Decentralized than PoS
tags: Consensus Mechanism
date: 2024-04-14 00:50:00
draft_date: 2024-04-12 09:42:46
---

I want to compare PoW and PoS from the perspective of entry barriers.

We often describe a blockchain as either permissionless or permissioned. This means whether becoming a block-producing node requires authorization or not.

PoW is universally recognized as a consensus mechanism that does not require authorization for mining. As long as you have a computer, you can join the network and start mining. If you can compute the required hash value, even if done manually, you have the right to produce blocks, and you decide which transactions to include in those blocks. Whether you can compute it is another matter.

BFT is a typical consensus mechanism that requires authorization. Many projects using BFT-like consensus directly require a CA center to issue certificates, and only nodes with certificates can undertake mining responsibilities in the network. The reason why the CA center issues a certificate to you is entirely an offline action. The authority to become a block-producing node is firmly controlled by the CA center.

There is some controversy with PoS.

The SEC once defined 66 cryptocurrencies as securities, including high-market-cap coins like SOL and ADA. However, ETH was not included, which gave many people hope and left ETH's PoS in a somewhat ambiguous space.

A significant fact is that PoS is a permissioned consensus mechanism.

In PoS, nodes often need to stake a certain amount of tokens to become block-producing nodes, and staking these tokens is the entry barrier for PoS.

A node wanting to become a block-producing node needs to acquire many tokens. Where do these tokens come from? Either they are allocated by the project at the genesis stage or subsequently bought from miners because miners will increase the supply of tokens.

Here's the problem: imagine an extreme scenario where all token holders are unwilling to sell their tokens. Wouldn't the control of the entire network be in their hands? Whether they are ten people or ten thousand, it is still a closed circle. Moreover, in PoS, the more tokens you have, the greater your power, which further increases PoS's centralization.

At this point, you might wonder, with Ethereum's success and millions of holders, thousands of institutions managing tens of thousands of block-producing nodes, isn't it decentralized enough?

Therefore, we must distinguish two concepts: a decentralized consensus mechanism and a decentralized blockchain. These are not the same.

Even if BFT-like consensus only supports a scale of dozens of nodes, if these nodes are distributed worldwide and held by large consortia with conflicting interests, the chain can still be considered decentralized because it is unlikely that more than two-thirds of these stakeholders will cooperate.

Similarly, Ethereum being decentralized does not mean PoS is a decentralized technology.

Why do many project teams choose PoS over PoW to issue coins? Because PoW is uncontrollable and has a high risk of being attacked. Observing the current PoW chains, almost all are forks of Bitcoin, and there are very few new ones. Most new altcoins come from Ethereum's tech stack.

Why is PoS controllable? As long as you don't distribute the initial funds too widely, you have control over the entire chain. Even if distributed, as long as the overall proportion is controllable, the chain remains under your control.

But then, isn't the mining resource of Bitcoin also controlled by a few manufacturers? If they are unwilling to sell mining machines, Bitcoin would always be controlled within a certain range. Not exactly; even without advanced mining machines, you can still participate in mining. Accumulating many outdated mining machines can still yield high computational power. At least no one has the authority to deprive you of the qualification to become a block-producing node.

In contrast, PoS is different; without enough money, you do not qualify to become a block-producing node. If token holders are unwilling to sell, you cannot obtain this qualification no matter how much effort you put in. PoS is essentially a consensus mechanism where the door is closed, and everyone negotiates internally, randomly selecting a block-producing node among qualified nodes and then voting.

What if there is a PoS without an entry barrier? All nodes are block-producing nodes, and one is randomly selected each round to produce blocks. This mechanism would inevitably suffer from Sybil attacks, eventually becoming a game of competing node numbers. Competing node numbers are not necessarily bad, as the number of nodes ultimately reflects hardware resources. Nodes with more hardware resources have a higher chance of being selected. I think such a consensus mechanism has further room for thought, but it faces many practical issues, such as how to use VRF to select the next block-producing node and whether the required network bandwidth for such a large number of block-producing nodes is supported by existing technology. Also, a PoS without an entry barrier is not really PoS.

15 years have passed, and Bitcoin has never been surpassed...
