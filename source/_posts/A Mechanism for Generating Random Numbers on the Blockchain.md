---
title: A Mechanism for Generating Random Numbers on the Blockchain
tags: 
- Blockchain
- MEV
date: 2023-02-22 18:19:59
draft_date: 2023-02-22 14:11:13
---

The blockchain itself does not allow for random numbers because all nodes need to synchronize their results. If the results differ each time, the entire network would be in chaos. In other words, on the blockchain, if there is a function that generates random numbers, it needs to meet two requirements: 1. The return value is unpredictable; 2. The return value is deterministic.

These two requirements seem contradictory, but there are solutions. Chainlink's oracle provides a method for generating random numbers using VRF. Due to the characteristics of VRF, it is suitable for generating random numbers in the blockchain scenario.

However, now we need to discuss another issue: miner misbehavior. You can refer to this article: [How Not To Run A Blockchain Lottery](https://hackingdistributed.com/2017/12/24/how-not-to-run-a-blockchain-lottery/)

Suppose there is a lottery game running on the blockchain, and miners also participate in the lottery. Since winning the lottery results in huge rewards, as long as the reward exceeds the miner's mining income, the miner has enough motivation to misbehave. The way to misbehave is that because the execution result of a transaction is deterministic, the miner will know the result first. If the result is unfavorable to them, the miner can refuse to produce the block. Economically, this may not be worthwhile for the miner, but this situation not only adds unfairness to the lottery game but also gives miners the right to misbehave. Miners can also be bribed, and if miners collectively misbehave, the network will be in chaos.

In the face of this situation, we need a way to generate random numbers where miners cannot know what the number is. The random number will only be revealed after the block is confirmed on the chain.

What is truly random and hard to predict? The future is unpredictable.

We can try using this method: use the block hash of the block height +2 of the current transaction as the random number seed.

For example, a transaction is initiated to generate a random number, the current block height is 1, after the transaction is submitted, the random number is null, and the actual random number will only be displayed when the block height reaches 3. Because this random number uses the block hash of height 3 as the random number seed, no one can know what this number will be before the block height reaches 3.

In the lottery scenario, the lottery result is determined at block height 1, users have already participated in the lottery in the transaction at block height 1, and the lottery result is only announced at block height 3. This almost avoids the problem of miner misbehavior because at block height 1, miners do not know what the result is, and at block height 3, the order and result of the lottery have been determined.

But can't miners refuse to produce blocks at block height 3? Refusing 1 block or refusing 3 blocks makes no difference.

Here we need to distinguish between two situations:

- Requests made at block height 1, querying at block heights 3, 4, and 5 will all get the random number calculated using the block hash at height 3 as the seed.
- Requests made at block height 1 use the block hash at height 3 as the seed; requests made at block height 2 use the block hash at height 4 as the seed.

To achieve a completely random effect, the second method should be used.

But then the problem arises, the current block height is constantly changing, won't this random value become a variable? Users generate a random number at block height 3 and want to know what this random number is, but they can never get this value because the number is actually produced at block height 5, and when querying at block height 5, they have to wait until block height 7...

Here we need to distinguish between two concepts: generating a random number and querying a random number. The above two situations are described based on the mechanism of querying a random number.

According to the mechanism of generating a random number, the second method is necessary, otherwise, the random number becomes a constant. What about querying? The undeniable fact is that as long as the user can query the result, the miner can also query it and will know in advance. The problem returns to the original dilemma, which seems unsolvable.

No, no.

Why set it to use the block hash of height +2 instead of +1? It is to avoid miners knowing the result in advance. If it is +1, miners can know the result after mining 1 block; if it is +2, miners generally find it difficult to lead the network by 2 blocks.

So a better approach is to request a random number at block height 1, and then use the block hashes of block heights 2 and 3 as the random number seed. In this case, the miner's third block will have some influence on the random number, but not a decisive influence. It is possible that the second block has already determined that the miner will not win, so the miner has no reason to misbehave at the third block.

But what if the third block can still greatly influence the random number? Miners can still refuse to produce blocks.

From this perspective, the more delayed blocks, the less influence the miner has. If the random number is determined by the block hash of +10 blocks, the first 9 blocks have already eliminated all miners, so miners will not misbehave.

Are there better solutions? It seems not. If the block hash of +2 blocks is used as the private key for symmetric encryption, the random value is generated before the +2 block is produced but cannot be decoded. The problem is that the contract cannot use the +2 block as the key to encrypt the random value in advance.

We can only reduce the miner's influence.

The future is unpredictable, but when it arrives, some will always have foresight.
