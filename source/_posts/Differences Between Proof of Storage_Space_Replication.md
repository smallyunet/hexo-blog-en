---
title: Differences Between Proof of Storage/Space/Replication
date: 2022-12-20 17:58:48
tags: 
- File Proof
- Consensus Mechanism
---

### Timeline

<style>
table {
    display: inline;
}
</style>

<center>

| Abbreviation | Full Name                  | Year  |
|--------------|-----------------------------|-------|
| PDP          | Provable Data Possession    | 2007  |
| PORs         | Proofs of Retrievability    | 2007  |
| PoS          | Proofs of Storage           | 2009  |
| PoS          | Proofs of Space             | 2013  |
| PoST         | Proofs of Space-Time        | 2016  |
| PoRep        | Proof of Replication        | 2017  |
| PoC          | Proof of Capacity           | 2017  |

</center>

### Proofs of Storage

PDP and PORs were independently published in 2007, each having its own advantages and disadvantages in terms of file proof methods, addressing different branches of the same problem.

The concept of PoS (Proofs of Storage) emerged as early as 2009, serving as a general term for interactive protocols where a client verifies files on a server, encompassing both PDP and PORs. For example, the paper "Proofs of Storage from Homomorphic Identification Protocols," with Giuseppe Ateniese as the first author.

Since blockchain was not well-developed in 2009, PoS (Proofs of Storage) had no relation to consensus mechanisms and merely shared an acronym with PoS (Proof of Stake) without any actual connection.

PDP and PORs are predecessors to PoS, which unified them under a single name.

### Proofs of Space

PoS (Proofs of Space) began with the 2013 paper "Proofs of Space," with Stefan Dziembowski as the first author.

Coincidentally, PoS (Proofs of Space) and PoS (Proofs of Storage) share the same abbreviation, which can sometimes cause confusion. In this section, PoS refers to Proofs of Space.

The concept of PoS aims to parallel PoW (Proof of Work). Hence, from PoS onwards, it is a concept within blockchain, serving as a consensus mechanism.

PoW involves mining using CPU power, whereas PoS proposes mining using disk capacity, seeking ways to prove a server has a certain amount of disk space.

The most basic method involves a client generating a file, for example, 1GB, sending it to the server, and then verifying the server has saved that file, thereby proving the server indeed has 1GB of disk space. However, this method is too cumbersome, consuming the client's disk space and putting immense pressure on network transmission.

PoS offers a method using a data structure known as hard to pebble graphs, such as a Merkle hash tree. This data structure's feature is that generating upper-level data must rely on lower-level data.

For example, in a system using a Merkle hash tree, a client can request the server to return the entire chain of a certain upper-level node, and then verify the path's correctness. Cross-verifying multiple paths can generally ensure the server's reliability.

### Proofs of Space-Time

PoST (Proofs of Space-Time) emerged in 2016 with the paper "Simple Proofs of Space-Time and Rational Proofs of Storage," with Tal Moran as the first author.

PoST builds upon the PoS (Proofs of Space) scheme because while PoS can prove a server has a certain amount of disk space, it cannot prove that the server's capacity consistently remains at the expected level. For example, during verification, the server's disk space is 1GB, but once verification ends, the server uses the space for other purposes. During the next verification, the server regenerates the 1GB file for verification.

Hence, PoS suggests verifying every minute to ensure the server's honesty. This is clearly not a smart approach.

PoST aims to solve this problem by increasing the difficulty of the initialization phase, that is, the phase where PoS generates the file, ensuring that the server must spend enough time to generate the file.

To ensure sufficient time is needed, PoW (Proof of Work) can be used, such as computing 2^30 hash values, which implies a certain amount of time spent.

PoST combines PoW and PoS by requiring the server to spend enough time generating the file during the initialization phase and then verifying the file generated during the proof phase.

### Proof of Replication

PoRep (Proof of Replication) originates from the 2017 paper "Proof of Replication," with Juan Bene as the first author.

PoRep is a type of Proofs of Storage and is a research outcome of Protocol Labs, which also developed IPFS and Filecoin. PoRep is the consensus mechanism used by Filecoin.

PoRep builds upon Proofs of Space and Proofs of Retrievability, adding the capability to distinguish the number of replicas on the server. The approach is relatively simple: during the tag generation phase, a unique identifier is attached to each replica, making each replica unique.

Because Filecoin operates in a decentralized network, it needs to ensure multiple replicas exist throughout the network. If nodes collude maliciously, previous proof methods are inadequate, so Filecoin employs the PoRep consensus mechanism.

The 2017 version of PoRep is relatively simple. The 2018 version introduces the use of Depth Robust Graphs data structures. The paper is titled "PoReps: Proofs of Space on Useful Data," with Ben Fisch as the first author.

### Proof of Capacity

PoC (Proof of Capacity) is a consensus mechanism used by the Burstcoin blockchain in 2017.

PoC provides a mining method where generating new blocks requires a nonce value:

``` 
1 nonce = 8192 hash value = 4095 scoops
```

The hash value is calculated using the Shabal hash function, with every two hash values forming one scoop.

A scoop number is randomly selected from 0 to 4095, and then combined with the corresponding nonce to calculate a `deadline` value. Among all nodes, the one with the smallest deadline value can generate a new block.

PoC leans towards being a purely consensus mechanism.
