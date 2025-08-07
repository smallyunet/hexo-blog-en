---
title: Analysis of the Camp Network Project (On-chain Property Rights)
date: 2025-08-04 12:15:42
tags:
- Project Analysis
---

[Camp Network](https://www.campnetwork.xyz/) aims to protect authors' intellectual property in AI Agent scenarios and potentially allow creators to earn revenue from AI-generated content. At the end of April this year, Camp Network announced a total of $30 million in [Series A funding](https://mirror.xyz/0xa01A821E654b923Be011acE131A22Ba58cFee3ad/wvdmjQaM2hw8uxLaLEamNPQMVT4H_bLWgzrsNWoOZpU) from various investment firms.

If you only look at the homepage, you might be confused. The concept is undoubtedly valid—intellectual property protection—but how is it actually implemented? The [official announcement article](https://mirror.xyz/0xa01A821E654b923Be011acE131A22Ba58cFee3ad/wvdmjQaM2hw8uxLaLEamNPQMVT4H_bLWgzrsNWoOZpU) mentions "Proof of Ownership and Priority." But aren't these concepts from a few years ago? And what exactly are these "Proof of..." mechanisms?

Camp Network hasn't made a Github repository public, which means the code can't be analyzed. Initially, I found this odd—why not reveal the Github account? Later, it made sense.

According to Camp Network’s [documentation](https://docs.campnetwork.xyz/introduction/l1-architecture/abc-stack), the network is built on top of the Celestia blockchain as the DA layer and is constructed using the [ABC Stack](https://www.abundance.xyz/). So what is ABC Stack? It’s a project within the Celestia ecosystem that differs architecturally from Ethereum’s "L2 Rollups." Instead, it introduces a new model called "Rollup L1s."

ABC Stack is developed by the Abundance team and claims to offer EVM-complete rollups capable of handling gigabytes of data per second. This means Camp Network directly used ABC Stack’s core technology to create its own chain and built applications on top of it for intellectual property protection, DID, AI, etc.

So how does one use the ABC Stack framework? Where's the code repository? How do you deploy and develop on it? You don’t need to—because there's a Celestia ecosystem company called Gelato that offers BaaS (Blockchain as a Service) platforms. These allow one-click deployment of chains using Op Stack, Arbitrum, or ABC Stack. The service is paid—$3,000/month to deploy to mainnet and $100/month for testnet. Camp Network is currently in its testnet phase.

That’s why Camp Network doesn’t need a code repository—it simply uses BaaS. Plus, there’s no alternative, as ABC Stack itself hasn’t released its code.

Camp Network’s [architecture documentation](https://docs.campnetwork.xyz/introduction/l1-architecture) mentions BaseCAMP and SideCAMP. BaseCAMP is the chain built using ABC Stack, while SideCAMP is for specific application scenarios. According to the documentation, Camp Network plans to deploy separate chains for each Dapp scenario—AI, music, art, etc.—all using ABC Stack.

With so many chains, how do BaseCAMP and SideCAMP communicate? ABC Stack wraps [Hyperlane’s cross-chain communication](https://docs.abundance.xyz/modular-bridging/gelato-hyperlane-cluster) to enable interoperability between ABC Stack-based chains.

But how does Camp Network actually protect IP? It provides an [SDK](https://docs.campnetwork.xyz/origin-v1/origin-framework). Essentially, authors must declare their identity on-chain—e.g., by linking their on-chain address to their X (formerly Twitter) username. Camp Network's Origin framework will then fetch content posted on X and automatically generate IP NFTs. Other users can purchase these IP NFTs, essentially buying the copyright.

Frankly, it's a rather dated model. Any project heavily touting DID should be approached with caution, as it often lacks core technical innovation and relies on a non-existent ecosystem.

In summary, Camp Network has no real core tech; it simply uses the services provided by ABC Stack to deploy chains and builds applications on them. Its [testnet](https://testnet.campnetwork.xyz/) already hosts many Dapps, and users can interact with them. Although it lacks hardcore underlying technology, Camp Network boasts a strong ecosystem, with many projects already participating in the testnet.
