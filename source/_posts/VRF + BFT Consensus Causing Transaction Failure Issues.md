---
title: VRF + BFT Consensus Causing Transaction Failure Issues
date: 2022-09-03 06:39:51
tags: 
- Blockchain
- BFT
---

Yesterday, I encountered an issue where Ontology's node stopped producing blocks. The node uses VBFT consensus, the network connectivity was intact without any isolation, thus no forks occurred. The error reported was that the block coming from the proposal had a different expected block hash (MerkleRoot). For various reasons, we didn't delve deeply into the problem yesterday and instead used a somewhat brute-force method to restore the network to normal. However, the mention of VBFT reminded me of some incidents at my previous company.

I first got involved with blockchain at my previous company. The project was touted as a self-developed blockchain and also used VBFT (VRF + BFT) consensus, though it wasn't referred to as such, it was called UBFT or something similar.

I suspect that the idea of VRF + BFT was proposed by Ontology, and my previous company copied this concept and implemented it. Ironically.

What I mainly want to talk about is a hidden bug caused by the unreliable implementation of VRF + BFT at my previous company. This incident happened almost two years ago, and I didn't document it in a blog back then, possibly because I was busy making PPTs? When I encountered the consensus-related issue yesterday, I remembered this incident and now have the time and inclination to write about it. Given the long passage of time, there might be some discrepancies in the details.

### Background

First, let me introduce the project's situation. It had several key features.

One feature was heterogeneous multi-chains, meaning that multiple heterogeneous chains could be included on the same node. Heterogeneous means that a chain can use different consensus mechanisms and run based on different databases. Multi-chain means multiple chains can run on the same node, as one chain was deemed insufficient, akin to a single data table in traditional business. Multiple chains facilitate data partitioning, better supporting business in consortium chain scenarios. Heterogeneous multi-chain can be understood as launching Ethereum chains with different chain IDs using the same binary package. Now, a certain open-source consortium chain still touts the flexible assembly evolved from heterogeneous multi-chain as a major feature, guess why.

Another feature was support for multiple databases, supporting many relational and non-relational databases simultaneously by writing a middle layer for data operations to adapt to various databases.

Then there was the consensus mechanism, based on the open-source Tendermint project. Tendermint core implements BFT-like consensus, and the modification made was to replace the round-robin selection of proposal nodes with the VRF function, enabling random selection of proposal nodes. Additionally, hierarchical consensus support was added, meaning the concept of a consensus group that changes every few blocks. The method of changing consensus groups borrowed from the BFT process, ensuring the safety of the group-switching process. The origin of the hierarchical consensus concept is unclear, perhaps it was popular in projects of that era?

These features were developed before my involvement, so I only had some understanding of them.

The issue I encountered was that if a failed transaction was sent to the blockchain, the node would immediately return a failed transaction result. If another failed transaction was sent again, the result of the second transaction would not be returned, and the node would stop producing blocks. Subsequently, any failed transactions would exhibit the same phenomenon. However, if a correct transaction was sent, the node would immediately return the result, and everything would return to normal. This phenomenon occurred probabilistically, not every failed transaction would trigger the issue.

### Preconditions

First, a failed transaction refers to a transaction where the contract returns a failed execution result. Blockchain systems can have two types of transaction failures: one where the transaction cannot be executed, and another where the transaction is executed but the contract returns a failure at the contract layer. The project did not strictly distinguish between these two types of failures, allowing contracts to return transaction-level failures, which is problematic design.

Invalid transactions would be ignored during the proposal because there was no need to record invalid transactions on the blockchain. Coupled with the project's erroneous handling of failed transactions, the phenomenon was that contract-execution-failed transactions would be ignored. This is the first precondition.

The basis of BFT consensus is to vote twice to finalize a block. No matter what BFT, prefixing with any letter, no matter how complex the process to decide which node proposes and how to propose, no matter what optimizations for consensus efficiency like parallel proposals or pipelined consensus, as long as it is BFT consensus, it involves two rounds of voting. The names for these two stages may vary, whether using proposal or prepare to describe it, the process is essentially the same.

The BFT process involves one node generating a block, sending it to other nodes, and if more than 2/3 nodes agree, proceeding to the next round of voting. If more than 2/3 nodes agree in the second round of voting, the block is finalized. Understanding BFT consensus hinges on the two rounds of voting. Why two rounds of voting achieve 3f + 1 fault tolerance and why at least two rounds are required is something I don't know either.

Ignoring failed transactions happens during transaction verification. There are two checks in the project, pre-consensus check and post-consensus check. Some transactions can't be checked pre-consensus, such as database write operations in contracts. If the database is written pre-consensus and consensus fails, the data would be chaotic. Thus, checks can only be done post-consensus. This is the second precondition.

### The First Failed Transaction

Let's analyze the bug based on the phenomenon. The first failed transaction proceeds normally. A failed transaction enters, it isn't detected as a failure pre-consensus, so the pre-proposal node proposes a block as usual, distributing it to other nodes for the first round of voting. Then it proceeds to the second round of voting. During the block confirmation phase before writing the block, post-consensus checks are conducted. If a transaction failure is detected and the block contains only this transaction, the block is discarded, and no block is produced. Other nodes also return messages to the proposal node, indicating the block didn't materialize and the transaction failed. Thus, the first failed transaction returns a normal result.

For the second failed transaction, the same processing flow should occur, everything should be normal. Even if the transaction fails during the block confirmation phase, the failure result would be broadcasted to other nodes. The protocol uses empty messages to represent voting or block finalization failures. Other nodes wouldn't be left waiting due to transaction failures. Since the BFT protocol flow isn't problematic, why does the bug still occur?

Here, the project's VRF modifications come into play.

In the BFT protocol, one node generates a block, distributing it to others to initiate the first round of voting. Which node generates the block? It can't always be the same node as that would be too centralized. Tendermint does this in round-robin fashion, e.g., A, B, C, D, then back to A.

The VRF (Verifiable Random Function) alters this selection method. If nodes are selected in order, it's easy to predict the next node to generate a block, posing risks like node bribery or attacks. VRF, with consistent parameters, yields the same result; with different parameters, the result is random. Using block height and voting round as parameters, each block can be generated by a randomly selected node, avoiding predictability. This modification was a project highlight.

However, VRF has an issue. Given its randomness, there's a chance that node A might be selected consecutively, which isn't negligible. If node A is malicious and generates blocks twice in a row, it could burden the network, though not necessarily breaking it. To mitigate this, the project added a blacklist mechanism on top of VRF.

If node A generated the last block, it is blacklisted. If the VRF result is in the blacklist, VRF runs again to avoid re-selecting the same node.

### The Second Failed Transaction

#### No Result Returned

Considering VRF and the blacklist, let's examine the first failed transaction. Node A receives the transaction, broadcasts it to other nodes, and then packages it into a block for voting. At this point, node A is blacklisted. After the voting failure, node A returns a failure and the transaction is no longer in node A's transaction pool because it's already processed.

What about node B? The block's transaction verification failed, but the transaction remains in node B's pool as it hasn't been processed yet. Should the transaction pool transaction be deleted? Yes, but it wasn't. This led to node B, chosen to generate the next block, packaging and broadcasting the same transaction, resulting in another proposal failure. Now, both nodes A and B are blacklisted.

Following this logic, the same transaction blacklists all four nodes in one round. However, the transaction result return isn't affected because node A already returned the result.

The second failed transaction arrives, and all nodes are blacklisted. What happens? If all nodes are blacklisted, the blacklist becomes ineffective. The VRF result dictates the node.

Let's analyze the second failed transaction. Node A receives the transaction, assuming node B is responsible for generating the block this time. If node B's block fails verification, it deletes the transaction and returns the failure result. Node B, chosen, generates the block and returns the notification. But the client submitting this transaction is connected to node A!

Why did the first failed transaction receive a response? Because the blacklist hadn't failed yet, all nodes processed the transaction and returned the result. Now with the blacklist failed, only node B returns the result, so node A's client doesn't receive it.

Why can't the failed blacklist work like the first transaction, processing by all nodes?

#### Blocking Subsequent Transactions

Let's continue analyzing the second failed transaction. Node C
