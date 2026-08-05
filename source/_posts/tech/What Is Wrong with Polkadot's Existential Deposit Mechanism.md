---
title: What Is Wrong with Polkadot's Existential Deposit Mechanism
date: 2026-04-22 17:50:48
tags: Blockchain
---

Polkadot has a mechanism called the [Existential Deposit (ED)](https://support.polkadot.network/support/solutions/articles/65000168651-what-is-the-existential-deposit-). It means that if your account balance falls below 1 DOT, the blockchain network will directly delete your account and its balance. Deletion means that your address information will no longer exist on-chain, other addresses will be unable to transfer less than 1 DOT to you, your balance and nonce will be erased directly, and the total token supply of the entire network will decrease as a result.

### Background

Let us first understand why a design like ED exists. Vitalik's coding and engineering ability may be far less impressive than his fame suggests; Gavin Wood was Ethereum's actual developer. Because Gavin designed Ethereum's node software in practice, he understood clearly that Ethereum has a problem with endlessly expanding state data.

Ethereum nodes maintain information about every address that has ever had a transaction record in the world state, using a tree structure called an MPT. Each address record is a leaf node in that tree. This creates a problem we can also experience when using an Ethereum wallet: if an address has a balance of 0.00001 ETH, that tiny balance can never be transferred out, because an ordinary Ethereum transfer always requires 21,000 gas. No matter how low the gas price is, an Ethereum account balance can never be reduced completely to zero. Such tiny balances are called dust, and they can be a disaster for people with obsessive tendencies.

For Ethereum nodes, these addresses and balances are not merely a visual annoyance. They mean that the amount of state data a node must maintain only ever increases and never decreases. Every time a new address acquires a balance, a node on the server must store another address key-value record. Even if each record consumes very little memory, the records continue to accumulate and place growing pressure on the server. From an engineering perspective, this is a brute-force, irresponsible, and unreasonable design.

There are two questions concerning dust balances. Let us compare them separately:

1. From the user's perspective, can a dust balance be eliminated so that the balance becomes exactly zero?
2. From the node's perspective, will dust balances continue to increase pressure on the server?

#### User Perspective

How does Bitcoin solve the dust-balance problem? Suppose a UTXO contains a very small balance of only 1 sat. Initiating a transfer with that UTXO alone clearly will not work, because 1 sat is not enough to pay the miner fee. However, a Bitcoin transaction can contain multiple inputs. As long as the small UTXO is included as one input and another UTXO with a larger balance is used as an additional input to pay the miner fee, the entire transaction can succeed and the 1-sat UTXO balance becomes zero. Thus, Bitcoin's UTXO model means that it naturally does not have the dust-balance problem.

Ethereum is where the dust-balance problem originated, and it still has no solution.

To solve the dust-balance problem, Polkadot wrote the Existential Deposit into the protocol as a core Day Zero design. A balance below 1 DOT is removed automatically, so naturally there is no dust-balance problem. Removing the balance does not mean transferring it to a miner or a burn address. It is simply erased as though it disappeared into thin air.

#### Node Perspective

Bitcoin nodes also need a chainstate and maintain information about all addresses with balances in memory. Compared with Ethereum, however, a Bitcoin address's dust balance can be reduced to zero, and nodes do not need to retain state data for addresses whose balances are zero. When an address has a zero balance, the node can remove it from state and reduce memory usage slightly. For Bitcoin nodes, therefore, state data does not only increase; it can grow or shrink according to actual conditions.

What about Ethereum nodes? On the one hand, because a user's balance itself cannot be reduced to zero, the node must maintain the state no matter how small the balance becomes. On the other hand, Ethereum does have a mechanism for clearing state data, but the requirements for clearing an address are strict: both its balance and nonce must be zero. A nonce is another concept Bitcoin does not have; Ethereum added it for its account-balance model. In other words, even if the balance reaches zero, once the address has sent a transaction its nonce can never be zero. These two causes combine to make Ethereum's state data expand continuously without decreasing.

Polkadot likewise uses its Existential Deposit design to clear addresses and related information directly when their balances fall below the threshold, reducing the state-data burden maintained by on-chain nodes.

#### Summary

Polkadot therefore uses the Existential Deposit mechanism to solve the effects of dust balances both for users and for nodes. From an engineering perspective, it is a relatively pure and modern design.

### What Is Wrong with It?

Returning to the title, is there anything wrong with Polkadot's design?

1. An Overly Pure Engineering Mindset

When programmers solve real-world problems, they often prefer engineering solutions while ignoring the experience of actual users. Polkadot gives the impression that small amounts of money do not count as money: if I do not have much money, I do not deserve to use it.

Cryptocurrency is an asset, a currency, and money. On-chain nodes behave as though they were collecting garbage when they reclaim my balance. Is that equivalent to saying that small amounts are garbage? Applying a programming-language garbage-collection mechanism to crypto assets is a mismatch. Invalid resources and dangling objects in memory can be garbage, but users' money is not. On the Bitcoin network, even a single sat will remain there quietly.

2. Violating the Principles of Cryptocurrency

Polkadot's Existential Deposit directly violates a fundamental principle of cryptocurrency: only the person holding the private key has control over the assets.

We often say, “Not your keys, not your coins.” On Polkadot, however, even though only you hold your private key, on-chain nodes can erase your balance directly and change the funds in the address without possessing the private key. This goes strongly against the principle.

In a blockchain, the only valid influence on a balance should come from ledger data. Ethereum has at least not gone this far, but Polkadot has: it directly modifies the balance in state data and introduces a node-level confiscation mechanism without any transaction taking place.

3. The Original Sin of the Account Model

If the [AA wallet](/2025/10/24/tech/what-is-the-fatal-flaw-of-ethereum-aa-wallets/) is a patch Ethereum introduced to solve problems in the account model, then the Existential Deposit is another patch Polkadot introduced to fill the same gap—an aggressive design intended to go one step further than Ethereum.
