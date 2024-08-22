---
title: How PoS Blockchains Handle Forks
date: 2024-08-22 17:41:01
tags: 
- Consensus Mechanism
---

Mainstream public blockchains can be classified into three categories based on their consensus mechanisms: PoW, PoS, and PBFT. The choice of consensus mechanism largely determines the network's TPS, degree of decentralization, and node scale.

Apart from PoW, the other two consensus mechanisms, PoS and PBFT, face a fundamental issue: how to recover when the network experiences a soft fork. Since PoS and PBFT do not require computational power to generate blocks, they cannot use the same longest chain rule as PoW.

### Background

#### Consensus Overview

PoW-based chains include BTC, BCH, BSV, LTC, DOGE, ZEC, and others. PoW uses the longest chain rule, where nodes facing multiple forked chains simply choose the one with the highest block height. Since generating each block requires significant computational power, the cost of attacking the network is high.

PoS-based chains include ETH, BNB, TRON, DOT, TON, ADA, AVAX, NEO, and others. Almost all current smart contract platform chains use PoS consensus.

PBFT-based chains include ATOM, SOL, TON, ONT, APT, SUI, etc. Among them, Cosmos is the most well-known, Solana shows potential to surpass Ethereum, and The Open Network has also developed well this year.

You might find it strange to classify SOL under PBFT. Isn’t SOL based on PoH consensus? Doesn’t SOL have staking features, such as earning rewards through staking in the Solflare wallet? TON also has staking and rewards, right?

These are two separate issues.

Firstly, Solana indeed developed and uses a BFT-like consensus called [Tower BFT](https://solana.com/news/8-innovations-that-make-solana-the-first-web-scale-blockchain). PoH is used to solve the clock problem on the Solana chain, not as a complete consensus mechanism.

Secondly, when it comes to PoS+BFT consensus, should it be classified as PoS or BFT? The above classification mainly refers to PoS, dPoS, PoS Casper, etc., while anything involving BFT is classified under PBFT for distinction. For instance, Cosmos also has staking and reward features, but few people would say Cosmos uses PoS.

#### About Forks

When dealing with forks in PoS and PBFT consensus, there are two aspects to consider.

One aspect is whether the staker list (called `Validators` in Ethereum, `Stakeholders` in Cardano) is consistent. Since PoS and PBFT generally use VRF to select a node from a candidate list as the block producer, what are the similarities and differences in handling this issue?

The other aspect is the rule for choosing the chain when the network forks (called `Forkchoice` in Ethereum, `Chain selection rule` in Cardano).

### Inconsistent Staker List

#### Consortium Chains

Let's start with the simplest, consortium chains. Consortium chains do not have coins, so there is no staking involved, only pure PBFT.

As the name suggests, consortium chains have very high entry barriers. You need to be reviewed or authorized to become a consortium member. Technically, this means that to join the network, it must be done with the knowledge of other nodes. For example, all nodes' configuration files contain a list of network members, defining the public keys and corresponding indexes of the network nodes. To add a node, all other nodes need to update their configuration files.

When a node produces a block, it randomly selects one from this list using VRF as the block producer. Generally, VRF returns a simple number corresponding to the index in the public key list, and the block producer signs the block with this public key.

This method is somewhat cumbersome but is characteristic of consortium chains. In this mode, the staker list is unlikely to be inconsistent. If it is, it means a configuration file error. A configuration error will always be wrong, making it easy to troubleshoot.

#### Cosmos

Cosmos Hub uses a consensus called [CometBFT](https://docs.cometbft.com/v0.37/introduction/). The basic process involves spending no less than 180 ATOM to register as a Validator, after which you may be selected as a block producer.

Since BFT consensus requires voting before block production, if there is indeed an inconsistency in the staker list within the network, two blocks might be produced at the same block height by different nodes, and other nodes in the network would vote on these blocks.

There are two scenarios here: a normal network and an abnormal network.

In a normal network, when two blocks are produced, only one of them can receive more than 2/3 of the votes; it's impossible for both blocks to receive more than 2/3 of the votes. So, before block production, nodes with inconsistent staker lists are already excluded from the process, ensuring that they don't affect subsequent procedures.

In an abnormal network, where a node cannot perceive the presence of other nodes, even if its staker list matches a few nodes in the current sub-network, the other nodes will not cast their votes. Unless the entire sub-network is isolated and the staker list coincidentally shares the same error, that sub-network would be isolated, playing alone on a local network. Network anomalies themselves are abnormal situations, isolated from the outside world.

#### Cardano

Cardano’s PoS is the purest form of PoS, without a voting mechanism. Cardano’s consensus has undergone [multiple evolutions](https://iohk.io/en/blog/posts/2022/06/03/from-classic-to-chronos-the-implementations-of-ouroboros-explained/) (there’s a lot to it, and I haven't finished reading it).

The rule in the Cardano network is that anyone can stake any amount into Stake pools to become a [Delegator](https://docs.cardano.org/about-cardano/learn/delegation/). These Delegators share the pool’s rewards in proportion to their staked amount but do not have the right to produce blocks.

In the Cardano network, the entities with the right to produce blocks are Stake pools. In other words, nodes that may be selected as block producers are already on the [pool list](https://preprod.cexplorer.io/pool). There aren’t many of them, around 300 at present, and each slot randomly selects one to produce a block.

So what happens if there is an inconsistency in the Stake pools list among nodes after registration? Cardano’s documentation [describes](https://developers.cardano.org/docs/operate-a-stake-pool/introduction-to-cardano#how-it-works) that when two blocks are produced in the same slot, the Chain Selection Rule is activated. This means that when a second block is produced, the chain has already forked, and all nodes will activate the chain selection rule to recover.

#### Ethereum

To become a Validator in Ethereum, you need to spend 32 ETH to register your node information on the [staking contract](https://etherscan.io/address/0x00000000219ab540356cbb839cbe05303d7705fa), after which all Validators retrieve the staker list from the contract.

How do you ensure that other Validators have synchronized the staker information from the contract to their local node? You can find a field called [Eth Data](https://github.com/ethereum/consensus-specs/blob/v1.3.0/specs/phase0/validator.md#eth1-data) in any block on the Beacon Chain explorer, such as [this block](https://beaconcha.in/block/20584195). This field is crucial for the staker list. When a validator is selected as the block producer, it packages the current staker list synchronized by the node, including the total number of stakers and the Deposit root information, into the block.

The Ethereum network updates the staker list approximately every 17 hours. During this period, new validators are only added to the network if the Eth Data field in more than half of the blocks contains the new validator. 

Thus, the process of adding Validators to Ethereum is long and strict. The staker list is difficult to be inconsistent under such rules.

### Chain Selection After Forks

#### Ethereum

If multiple forks occur in Ethereum, the choice is relatively easy because Ethereum has a voting mechanism. Each block contains the number of validators that voted for it. When a fork occurs, simply selecting the block with the most votes should suffice.

In fact, Ethereum's fork choice is based on a [checkpoint mechanism](https://ethos.dev/beacon-chain). Each block is a slot, every 32 slots form an epoch, and each epoch is a checkpoint. A checkpoint that receives more than 2/3 of the votes becomes justified. When the checkpoint after the next one also becomes justified, the current checkpoint is considered finalized. Therefore, in Ethereum, a transaction takes 15 minutes to be marked as finalized.

The checkpoints mentioned here are the basis for [FFG](https://arxiv.org/abs/1710.09437) fork choice. Each chain will choose the one with more checkpoints, so Ethereum's consensus does not select the chain with the most blocks, but the one with the most checkpoints. The chain with the most checkpoints is the main chain.

#### Cardano

Cardano currently uses a [Chain selection rule](https://developers.cardano.org/docs/operate-a-stake-pool/introduction-to-cardano/#what-if-for-some-reason-there-is-a-fork) provided by the [Ouroboros Genesis](https://dl.acm.org/doi/10.1145/3243734.3243848) version.

The previous version of Ouroboros Genesis was [Ouroboros Praos](https://link.springer.com/chapter/10.1007/978-3-319-78375-8_3). The Praos version introduced a rule called `maxvalid`, and the Genesis version made some improvements to this rule by incorporating the `moving checkpoint` feature, resulting in a new rule called `maxvalid-mc`.

The moving checkpoint is a simple concept: when facing multiple forked chains, if the fork does not exceed `k` blocks, the chain with the longest length is selected. If the fork exceeds `k` blocks, it is ignored entirely. This means the local chain will only select the longest chain within the `k` blocks range. The range of `k` blocks is referred to as the moving checkpoint. The advantage of this restriction is that it helps avoid the longest chain attack. Of course, Cardano established this rule through a series of academic analyses and practical tests.

#### Cosmos

In PBFT chains like Cosmos, as long as the staker list is consistent under normal network conditions, forks will not occur.

### Conclusion

In summary, each consensus mechanism has detailed rules for handling forks, and these rules have been tested in real-world scenarios by established public blockchains. The specific implementation of fork recovery is closely tied to the design philosophy of each blockchain.