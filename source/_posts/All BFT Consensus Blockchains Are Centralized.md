---
title: All BFT Consensus Blockchains Are Centralized
date: 2025-01-05 11:57:59
tags: BFT
---

First, let's rank consensus mechanisms in terms of decentralization. This ranking is almost indisputable:

PoW > PoS > DPoS > BFT

Now, let's compare PoS and BFT from the perspective of handling forks.

Due to the nature of the BFT algorithm, all chains using BFT consensus do not have forks, whether soft or hard forks. A chain without forks means that there is only one version of the network at any given time, and this version is determined by the project issuer. Even if the issuer is not an official entity, this version can only come from a centralized organization. Therefore, blockchains using BFT consensus are centralized.

What happens if the network issuer makes an unacceptable change?

Under PoS consensus, validators can choose either the old rules or the new rules. Both versions can coexist until most validators reach a consensus and the network regains consistency. If validators never reach consensus, the network will keep forking indefinitely.

Under BFT consensus, validators can also choose between the old and new rules, but if one side reaches half of the validators, the network will stop. The network will only restart once validators reach an offline agreement.

In other words, when a fork should theoretically occur, BFT networks simply halt. This is why networks like Solana and SUI have experienced outages.

At this point, you can understand that the centralization in BFT networks means that there cannot be two simultaneous networks. While other consensus mechanisms also rarely result in such situations, they at least allow for the possibility.

To clarify further, the centralization of BFT networks means that if a certain proportion of validators want to stop the network, they can. The only way to restore the network is to start a new one (which is essentially a form of hard fork).

What are the implications of this? In the Ethereum network, even if the majority of nodes go offline, as long as a few remain, the network can continue to operate. However, BFT networks have a fault tolerance of less than half—if half of the validators go offline, the network completely shuts down, making it impossible to transfer any on-chain assets.

From an investment perspective, if you plan to hold a cryptocurrency long-term, which type of network do you think is safer and better protects your assets?

However, it is important to note that network reliability does not necessarily come from decentralization. Coinbase’s Base network, for example, derives its reliability from U.S. government regulation and partial compliance. Many exchanges and government institutions store funds in Coinbase Prime's custodial service, making the Base network relatively reliable as well.