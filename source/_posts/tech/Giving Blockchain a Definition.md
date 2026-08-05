---
title: Giving Blockchain a Definition
date: 2020-08-09 16:06:45
tags: Blockchain
---

For a long time, blockchain seemed to lack a clear definition. The terms often associated with blockchain are decentralization, traceability, immutability, credit-based, and the next-generation value internet. These are all characteristics of blockchain, not its components. These terms describe what blockchain has, but not why it has those features or why it needs them.

It seems that many things don't have clear definitions. For instance, what is a computer? Everyone knows it's something you can play games on and browse the internet. A slightly more technical answer might describe it as a machine based on the Von Neumann architecture with five components. But a computer can simply be considered "a machine that performs computations." What is a refrigerator? "A cabinet that stores items and has freezing functions" suffices. What is a design pattern? A software program design paradigm. What is a microservice? A software architecture pattern. So, what is blockchain? Interestingly, the term blockchain (blockchain) has no clear origin; even the Bitcoin white paper only mentions a "chain of blocks."

I used to have some immature understandings of blockchain. Although not entirely incorrect, they were unclear, especially regarding the question of what blockchain is. Now, I believe the definition of blockchain should be:

> Blockchain is a type of data collaboration software, or in other words, blockchain is software used to synchronize data.

Data collaboration software determines what data structure to use and what communication mechanism to synchronize specific data. Blockchain is not a database; it doesn't store data. Storing data is left to actual databases. Blockchain doesn't care how data is stored on the disk, whether the storage structure is reasonable, whether the utilization rate is high, or whether the processing speed is fast. Blockchain cares about how data is synchronized to other machines, how to synchronize it timely, and whether the data synchronized from other machines has issues. In a sense, blockchain is an implementation of data collaboration software.

Because it is data collaboration software, blockchain is multi-node and decentralized, which is self-evident.

Traceability means that transactions can be traced as long as there are relationships between the data, which is determined by the data model, such as UTXO.

The chain-like data structure is to facilitate the verification of data integrity by the data collaboration software, similar to using md5 to determine if a file is complete. This data structure is not necessary; comparing the entirety of data can achieve the same goal, although very inefficiently. Using encryption algorithms to summarize data and place it in the next segment essentially ensures that a large block of data is complete, nothing more.

As for immutability, it is a feature brought by data collaboration software. Blockchain immutability doesn't mean data can't be modified; it means that modifications are not recognized by other nodes. This is different. Data immutability is a technical issue. For example, not providing an interface to update data leaves users with no way to modify data, which can be controlled by technical means. Not accepting modifications from other nodes is a mechanism, and this issue is beyond the realm of technology.

Is blockchain development currently constrained by technology? The theory of computation includes two main parts: computability theory and complexity theory. Computability theory determines whether a problem can be solved algorithmically, and complexity theory aims to improve algorithm efficiency. Is this related to blockchain? Stepping back, does blockchain need computation? It doesn't; it's not related and not constrained. Here's an interesting thought experiment: What would happen if everyone in the world joined a WeChat group? At least the messages on the screen would be overwhelming. What would happen if all the world's data were on one blockchain? So, ultimately, blockchain is a mechanism issue, not a technical one.

Bitcoin and blockchain are two concepts. Bitcoin is a transaction system that uses blockchain for data synchronization. Bitcoin is primarily a transaction system, with data synchronization as a secondary need. This is the conceptual mistake I made earlier, equating blockchain with Bitcoin. Many people with a vague understanding of blockchain also associate blockchain with digital currencies like Bitcoin. I remember attending a sharing session last year where the speaker, a director from a well-known exchange, talked about blockchain and national policies, but the whole session was about Bitcoin anecdotes.

Blockchain is a component of Bitcoin. The author of Bitcoin recognized the value of Bitcoin and released the software and white paper. Why didn't the author abstract the concept of blockchain and release a general-purpose software and manual? Was it because the author didn't realize blockchain's potential value? No. Blockchain was proposed because people saw the value of Bitcoin and wanted to replicate its success, so they extracted Bitcoin's technical components and called it blockchain. Unfortunately, Bitcoin is a cleverly designed system, and extracting certain technical features alone cannot produce the expected value, which is also blockchain's current situation. This is a modern version of imitating without understanding the essence.

Smart contracts were proposed as early as 1997 by a financial and legal practitioner. "Smart" refers to automatically executing certain actions when conditions are met, compared to paper contracts. Indeed, it was a bit smarter, especially in an era when digitalization wasn't widespread, making the term "smart" not an exaggeration. Moreover, the author explicitly stated that smart contracts did not use artificial intelligence.

Abstracting smart contracts, they automatically execute actions when conditions are met, similar to conditional statements in programming languages. In fact, most modern smart contracts are implemented using Turing-complete programming languages. The fatal problem of describing contracts with programming languages is that their expressive power is much weaker than natural language. If you try to rewrite all the clauses in an insurance policy using a programming language—"If X happens, then compensate Y..."—the cost of rewriting is too high. Furthermore, legal clauses often require professional lawyers and judges to interpret and judge. The logic of the real world is far more complex than program logic, which programming languages cannot handle.

A data synchronization software should not be venerated. Blockchain has been deified and demonized. Therefore, one cannot say blockchain has no value because it is merely a tool software.
