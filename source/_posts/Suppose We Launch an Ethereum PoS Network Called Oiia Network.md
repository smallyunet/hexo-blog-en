---
title: Suppose We Launch an Ethereum PoS Network Called Oiia Network
date: 2025-01-28 23:26:40
tags: OIIA
---

OIIA OIIA (Spinning Cat) is a recently popular meme featuring a spinning cat. Compared to DOGE on PoW chains, OIIA aims to be the cat-themed memecoin on a PoS chain.

Unlike memecoins issued by platforms like pump.fun or tokens like $Trump, OIIA tokens will be entirely generated through PoS mining. There is no pre-allocation (as verified by the genesis file), meaning tokens not mined cannot be purchased with money.

### Network Motivation

1. [The Best Way to Issue Cryptocurrency](/2025/01/10/The%20Best%20Way%20to%20Issue%20a%20Cryptocurrency/)
2. Oiia will provide tools that make it easier to set up Ethereum PoS networks, further lowering the barrier to entry.

#### Why Users Should Participate

If participants lack relevant experience, they can benefit as learners by:
1. Learning how to set up a complete Ethereum PoS network.
2. Understanding how to launch and maintain an Ethereum Validator node, including staking and unstaking processes.
3. Becoming proficient in using Ethereum ecosystem tools for nodes.
4. Identifying and solving usability issues in Ethereum ecosystem tools, contributing to the broader Ethereum ecosystem.

#### Why Not Use Ethereum Testnets

As Ethereum network learners, why choose Oiia Network over testnets like Sepolia?

The reason lies in Oiia Network’s identity as a memecoin.

### Network Specifications

#### Technical Foundation

Oiia Network uses Ethereum clients Geth + Lighthouse as initial nodes. Only the startup configuration and genesis file are modified—no code changes are made.

#### Chain ID and Network ID

Decimal:
```
20220915
```

Hexadecimal:
```
0x1348BF3
```

#### Initial Validators

The initial validator count is 128, the minimum scale for network launch. This number will be directly written into the `genesis.ssz` file.

#### Initial Faucet

Since initial participation is expected to be limited, a reserve of 128 × 32 = 4096 OIIA will be allocated as the faucet balance and placed in the faucet address.

The faucet will distribute tokens via PoW Faucet (web-based mining). 

The faucet aims to provide small amounts of tokens for testing the network and incentivizing early Validators to join (although acquiring 32 OIIA from the faucet alone will be challenging). If 128 solo stakers participate, it would be a significant success, and the 4096 OIIA reserve should suffice.

#### Initial Token Supply

To avoid issues like Ethereum Foundation’s token sales, OIIA will not pre-allocate any tokens to developers or DAOs. Pre-allocation often raises suspicion across networks.

Ethereum’s PoS consensus has no upper limit on token supply. Thus, the initial supply is limited to 4096 OIIA. All post-launch circulation will come from mining rewards, similar to Bitcoin.

In summary, based on the genesis file of Oiia Network, apart from the 4096 OIIA reserved for the airdrop (not reflected in the genesis file for the 128 Validator nodes, valued at 4096 OIIA), no additional tokens will be pre-allocated to any address.

#### How to Become a Validator

Since the initial token supply is very limited and the faucet cannot provide enough OIIA, users can apply to become a Validator through the community. The community will transfer 32 OIIA directly from the faucet address to the applicant's address.

#### Network Launch Progress

As there are no commercial objectives, the network launch will proceed at a relaxed pace.