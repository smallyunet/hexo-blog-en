---
title: Understanding Blockchain Consensus Mechanisms
tags:
  - Blockchain
  - Consensus Mechanism
date: 2023-07-01 17:19:29
draft_date: 2023-07-01 13:12:13
---

The role of a consensus mechanism is to ensure that the data across most nodes remains consistent. Consensus mechanisms can be broadly categorized into two types: PoW (Proof of Work) style and PBFT (Practical Byzantine Fault Tolerance) style. Almost all consensus mechanisms fall under these two styles.

Let's start from scratch and think about how to maintain consistency among a group of humans. One method is to elect a leader whose opinion everyone follows. Another method is to make decisions collectively through discussion to reach a unified opinion.

For the first method, the challenge lies in how to elect the leader, the criteria for selection, and what kind of person can become the leader. For the second method, the challenge is who can participate in the discussion and how decisions are made.

First, let's look at the first method, which mainly involves three steps:
1. Elect a leader through some means.
2. Everyone listens to the leader within a time unit.
3. Everyone reaches a consensus within a time unit.

The PoW process is as follows:
1. The miner who solves the hash puzzle becomes the block-producing node.
2. All nodes receive and validate the miner's block.
3. All nodes' block data reach a consensus.

In this PoW style consensus process, the biggest variable is the first step, which is how to elect the block-producing node. Therefore, there are many variants of PoW consensus.

- Proof of Stake (PoS): The consensus mechanism used by Ethereum, with the cryptocurrency named ETH. Nodes that stake a certain amount of assets randomly become block-producing nodes, with the randomness determined by the VRF function running on the beacon chain. The more assets staked, the higher the probability of becoming a block-producing node.

- Proof of Authority (PoA): A consensus mechanism supported by the Ethereum testnet. The block-producing nodes are predetermined during the network initialization phase and produce blocks in sequence. The process of verifying validators is completed offline, and the network itself does not have the ability to correct validator nodes, making it a relatively centralized consensus.

- Proof of Importance (PoI): The consensus mechanism used by Nem, with the cryptocurrency named XEM. Nodes are ranked based on certain scoring mechanisms, such as the number of transactions and transaction amounts over a certain period. Nodes with scores above a specified standard randomly become block-producing nodes.

- Proof of Elapsed Time (PoET): The consensus mechanism used by the Hyperledger Sawtooth project, developed by Intel. Each node randomly generates a wait time, and the node with the shortest wait time becomes the block-producing node. The block-producing node must provide proof of the shortest time, which is combined with hardware devices to make it tamper-proof.

- Proof of Burn (PoB): The consensus mechanism used by Slimcoin, with the cryptocurrency named SLM. Nodes obtain a burn hash by burning assets. The burn hash calculation includes the amount burned and the frequency of burns over a period. The system compares the hash values submitted by each node, and the node with the smallest hash value produces the block for that round.

- Proof of Capacity (PoC): The consensus mechanism used by Burst, with the cryptocurrency named Burst. Nodes use a hard-to-pebble graph data structure to write files on disk, requiring sufficient disk space. After writing, nodes randomly open a file location and calculate the corresponding hash value until they find a satisfactory hash value, allowing them to produce a block.

- Proof of History (PoH): The consensus mechanism used by Solana, with the cryptocurrency named SOL. The innovation of this consensus mechanism is that each transaction or other on-chain operation corresponds to a hash value. The PoH generator produces blocks, each composed of a series of continuous hash values, ensuring the consistency of on-chain data. The PoH generator is the block-producing node, selected based on the amount of staked assets.

These are examples of PoW style consensus mechanisms. Despite their efforts to change the way block-producing nodes are selected, all these blockchains follow the three-step process.

Now, let's look at the second method, which also involves three steps:
1. Select decision-makers through some means.
2. Decision-makers communicate and reach a consensus.
3. Everyone else follows the decision to reach a consensus.

The PBFT process is as follows:
1. Select consensus nodes from all nodes, then rotate the role of proposer among them.
2. Consensus nodes undergo two rounds of voting on the proposal content.
3. Consensus nodes reach an agreement, and other nodes follow suit to reach a consensus.

Compared to the first method, the single leader is replaced by multiple decision-makers. In this PBFT style consensus process, the biggest variable is also the first step, which is how to select consensus nodes and determine the order of proposer nodes. Here are some variants:

- Delegated Byzantine Fault Tolerance (DBFT): The consensus mechanism used by Neo, with the cryptocurrency named NEO. Every node holding assets can participate in the election of consensus nodes, delegating their assets to consensus nodes. The top-ranked consensus nodes are the proposers in sequence.

- Federated Byzantine Agreement (FBA): The consensus mechanism used by Stellar, with the cryptocurrency named Stellar. All nodes in the network are consensus nodes and can participate in two rounds of voting. To reduce network congestion, nodes can delegate their voting rights to another node, using slices or sub-networks to improve consensus efficiency.

- HoneyBadgerBFT: A type of BFT consensus that supports fully asynchronous networks, not relying on synchronized time sequences, which PBFT cannot achieve. However, consensus efficiency in asynchronous networks is relatively low.

- HotStuff: A BFT consensus allowing partial network asynchrony. Its feature is that multiple proposals can exist simultaneously in the network, with proposer nodes selecting the best proposal for subsequent processes. This parallel proposal process improves overall consensus efficiency.

- VBFT: The consensus used by Ontology, with the cryptocurrency named ONT. Proposal nodes are selected using the VRF random function, with each round's proposal nodes being random and unpredictable.

In summary, both PoW and PBFT styles filter out content that can ultimately achieve consensus through some means, but they fundamentally differ in how content is selected. In PoW, other nodes unconditionally accept the source block as long as it meets certain criteria. In PBFT, other nodes receive the block content first, then decide whether to accept it through a voting process before reaching a consensus.

There are also some innovative types of consensus, such as hybrid consensus combining multiple consensus mechanisms. These may evaluate node reputation, score based on historical transaction quality, or use hardware devices like mobile phones and IoT for data validation. Additionally, optimizations in PBFT, such as parallelism at certain stages, fault tolerance, and network efficiency, still fall within the frameworks of PoW and PBFT.

Therefore, it can be boldly stated that consensus mechanisms are essentially the same, differing only in their specific design and implementation.
