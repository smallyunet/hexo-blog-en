---
title: Randomized Block Confirmation Consensus Mechanism
date: 2022-09-25 17:43:28
tags: 
- Blockchain
- Consensus Mechanism
---

### Steps

1. Within a time window, a node will receive multiple validated blocks and randomly select one as its next block.

2. In the next time window, if a node does not receive a block and notices that the overall block height of the network is higher than its own, it will discard the previously confirmed block.

3. If block heights are the same, the minority obeys the majority.

### Why Do This

- Receiving Multiple Validated Blocks

The idea behind Proof of Work is that within a time window, there is only one correct answer, ensuring consistency across the network. Conversely, why can't there be multiple correct answers within the same time window?

- Randomly Selecting a Block

Since multiple valid blocks exist simultaneously in the network, a mechanism is needed to select one among them. For fairness, a completely random method is used.

- Discarding the Previous Block

The block discarding mechanism provides the system with self-correction capability. The DAO attack on Ethereum required developers to lead the community in a hard fork, indicating that the system lacked self-correction capability and required human intervention. It seems all current blockchain systems are like this.

- The Minority Obeys the Majority

In the absence of transactions, it is possible that all nodes are in a waiting state. In this case, since block heights are the same, no node is willing to discard its own block, causing a network split. This is a game state, so the principle of minority obeys majority is used to break the balance.

### Some Issues

- Why Random?

To avoid the rich getting richer, nodes with computing power becoming wealthier, and nodes with assets getting wealthier, giving every node participating in the network a fair chance to receive block rewards.

- Who Randomizes?

Each node randomizes and selects a block on its own, rather than using a method like a Verifiable Random Function where each node uses a confirmed result.

- Completely Random or Restricted Random?

Completely random.

Completely random means that if a node receives 100 blocks, the probability of selecting a particular block is 1%. Restricted random means, for example, if the previous block was provided by a certain node, the next block will not use its block, and the block reward will not go to it either.

If it is restricted random, nodes that have already produced blocks and nodes that have not produced blocks will have different weights. No matter which side the weight leans towards, it is not reasonable. If nodes that have produced blocks have more weight, the rich getting richer issue persists. If nodes that have not produced blocks have more weight, participants in the network will create new accounts like crazy, using nodes that have not received block rewards to produce blocks.

- Does Randomness Lead to Forks?

Definitely, but forks are small-scale. If the network is efficient, only the latest one or two blocks will be uncertain. Once the majority of nodes confirm, it becomes the mainstream chain.

- Will Forks Always Exist?

No, because forks will be eliminated. New nodes joining the network will definitely choose to sync data with a particular node. For other nodes, random selection means choosing from validated blocks. Validation means that the historical data is the same as theirs. When a forked chain has no transactions, due to the self-correction mechanism, it will keep discarding its blocks until it aligns with the main network.

- What if New Nodes Do Not Sync Data?

If new nodes do not sync existing data but start anew, they need to attract more than the majority of nodes in the network. For example, if there are 1000 nodes, due to the completely random mechanism, they need an additional 1000 nodes for their chain to become mainstream. This approach can be seen as a way to attack the network, and it seems more difficult than a 51% attack.

- Is There a 51% Attack?

Due to the block height priority combined with the principle of minority obeys majority, it might be thought that an attacker only needs to control 51% of the highest block height nodes to attack.

This may be a conceptual misunderstanding. An attack refers to overthrowing existing data, not generating new data. A 51% attack does not exist.

- Does Rollback Cause Uncertainty?

For users, it is indeed a bad experience. A transaction might be successful one minute, and the next minute, the node discards the block, and the transaction is canceled.

However, this uncertainty is short-lived. It can be considered that confirming a block requires two or more time windows. Because even if there are small-scale forks in the network, it will eventually trend towards majority consensus.

Thus, the issue becomes the timing of client confirmation of a transaction.

- Do Unconfirmed Blocks Cause Resource Waste?

If a node receives 10 blocks and confirms 1, the other blocks are wasted. Repeated correction is also considered resource waste.

PoW wastes computational resources, while this random block confirmation method wastes network transmission resources. If a node wants its block to be confirmed, it must first let other nodes receive its block. So, if there are 1000 nodes in the network, each round requires broadcasting the block to all 1000 nodes.

Fortunately, the waste of network transmission resources has an upper limit. Blockchain networks inherently need to broadcast every transaction to the network. Compared to that, only needing to broadcast an additional block's content is not too much of a burden. Also, since blocks are chosen completely randomly, nodes trying to broadcast their block multiple times through higher network configurations are futile.

- Will Nodes Discard All Blocks if the Network is Abnormal?

If a node receives very low block heights from the network, according to rule 2, will it gradually discard all blocks?

No. It is crucial to distinguish between receiving abnormal information from the network and being unable to connect to the network to get information. If a node has 1000 connection records in its routing table but can only connect to 10 due to network conditions, it is an abnormal situation. The node should not discard blocks according to the normal consensus process.

- Are There Any Existing Proposals?

In the paper [Blockchain Consensus Algorithms: A Survey](https://arxiv.org/abs/2001.07091), pages 16-17, there is a mention of a consensus method for randomly selecting block nodes and coin-age-based selection. This indeed is a random concept, but the paper describes randomness as random node selection, not for blocks, and discusses it in the context of PoS, implying that block nodes are selected and predetermined.
