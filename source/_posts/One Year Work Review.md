---
title: One Year Work Review
date: 2022-08-27 11:17:16
tags: Work
---

It's been more than a year since I joined the company, and I want to do a simple phase review. Because I usually record the general work content on the internal Confluence, summarizing it now has some references to follow.

A lot has happened over the past year. I made a very good friend, which made me less focused at work. Compared to before, both my work efficiency and dedication have significantly decreased. However, overall, there have still been great gains.

### State channels

In the first month of joining the company, I mainly familiarized myself with the project. This project is much larger in scale than the previous company's project, integrating functionalities equivalent to Filecoin, IPFS, Raiden Network, Ontology, and other public chain projects. It also implemented its own network module and network proxy similar to libp2p. Initially, it was a bit challenging, partly due to the large codebase and partly because I was unfamiliar with public chains. Although I knew the technical modules of blockchain itself, I didn't know what Layer 2, IPFS, or PoC were. In the first month, I managed to set up and run the project, understood the basic process of uploading and downloading, and mainly looked at the interaction implementation of the p2p network at the protocol level, which was the first time I learned what DHT meant.

In the second month, besides delving into code details, I made one change: replacing the DFS in the State channels routing with Dijkstra's algorithm. The project has a Layer 2 similar to Raiden Network, used to solve the issue of frequent payments to other nodes during file downloads. The routing change was relatively independent and wouldn't cause trouble for the entire project. The optimization with Dijkstra's algorithm, considering path length, was actually insignificant because our DNS nodes are not that numerous, unlike Raiden Network's fully P2P mode.

In the third and fourth months, the main task was adding fees in State channels, deducting part of the transfer amount at the relay nodes. That was a painful process because I didn't understand Layer 2 or State channels at the time. Fortunately, after two months, I figured out the protocol. Due to project scenario constraints, the transaction couldn't be directly verified by the initiating node, causing some instability in channel states, but it was harmless.

After joining the company, I hardly asked colleagues about the project, whether it was the overall architecture or specific code details. I read the code and documentation on my own, especially facing many completely unfamiliar concepts. Doing so was intentional. The inefficiency of doing everything by oneself is natural, but the benefit is that after that process, I had enough confidence to understand and complete such a large-scale project on my own.

Since it was my first job change, I doubted my abilities, wondering if my feeling about the previous company's project and technology was an illusion or reality. With a salary increase from job-hopping, I felt somewhat guilty, questioning if I could competently handle such a project in a new company and live up to the salary. Asking colleagues for help is so simple! In the previous company, I also received much care, but I hoped to have the ability to stand alone, and this was a perfect opportunity to solve problems without relying on external help.

### Solidity contracts

In the fifth month, I was conflicted about the direction to optimize Layer 2. It was difficult to start, and I was preparing to solve the performance issue but had no ideas. Later, a requirement came to rewrite the project's native contracts in Solidity, as there were plans to support EVM. I spent about a week reading Solidity documentation, as I didn't know Ethereum or Solidity at the time.

In the sixth month, January this year, I rewrote the contracts in Solidity. That was a pleasant time because it didn't require much thinking about what to do or how to do it; I just followed the existing implementation. The code output was substantial. Switching programming languages isn't stressful.

In the seventh month, February this year, I added support for the Ethereum SDK in the node's SDK. I fiddled with Ethereum's testnet, found it not very useful, and ended up using the node's development mode.

Although Solidity's syntax is easy to understand, due to EVM's limitations, there are many things to pay attention to and many language details that need time to get familiar with. Although it took a month to write the contract, it took a lot of time afterward to modify and improve it. In short, during that process and requirement, I learned to write Solidity contracts and became familiar with Ethereum smart contract development, though still lacking experience with production-level contract security issues.

### Upload and download of folders

Starting in March this year, I worked on uploading and downloading folders. This idea originated from the project's support for Git, wanting to support the Git protocol and directly use the git command to clone Git repositories. It turned out that ordinary folder uploads and downloads were missing, only supporting file uploads and downloads.

Our project used part of the IPFS IPLD protocol, converting files into blocks for transmission, but didn't use IPFS's file management part. IPFS has a layer of API for file system operations, unifying file and folder processing, and easily converting between data structures. We directly read files and convert them into blocks, complicating folder upload and download implementation.

This task has been ongoing with various small issues, thanks to the patient cooperation of testing colleagues. Folder processing is slightly more complex than single files due to infinite nesting, containing both files and folders within folders, and handling empty folders and large files.

IPLD nodes are stored in a Merkle DAG data structure. A single file generates a Merkle Tree, while folder uploads need to organize multiple Merkle Trees into one tree, then deserialize this tree into a folder during download and write the content to the disk.

The implementation idea is simple: the tree structure has data blocks as raw nodes and proto nodes connecting data blocks as intermediate nodes. Just write the extra folder information into the links of proto nodes to store and transfer the association information between files to other nodes.

However, the actual implementation took much effort, with some detours. For example, adding extra data during upload caused block data validation failures, preventing the complete tree structure from forming, requiring deep debugging into IPLD code. During download, unfamiliarity with block data parsing and the unordered nature of block data transmission between nodes led to suspected overall logic issues when features failed.

Due to unordered block data transmission, sorting blocks during download was necessary. Confusing tree level-order traversal with pre-order traversal initially caused misordered blocks, taking a long time to debug and switch to breadth-first traversal.

This feature, though trivial, deepened my understanding of file upload and download.

### Asymmetric encryption of files

From April to May, during a severe pandemic period, I worked from home for over a month, focusing on supporting asymmetric encryption of files. Previously, only AES symmetric encryption was supported.

There's not much to say about this, just noting that ECDSA is a digital signature algorithm, not for encryption/decryption, requiring hybrid modes like ECIES, combining symmetric encryption to achieve asymmetric encryption/decryption of files.

### Support for Ethereum accounts

From June to August, I focused on new Layer 2 solutions, aiming to implement Layer 2 based on Optimistic rollups.

Rollups haven't been tackled much yet, currently focused on storage node support for Ethereum accounts. During this period, besides compiling and running the Optimism project and adding options for various network modes to storage nodes, much time was spent perfecting previous Solidity contracts, resolving issues like contract size limits and discrepancies between actual and expected results during node operation.

Since account addresses and public-private keys have a different format, protocol messages of storage nodes also require a new signature method. These changes are still ongoing.

### Summary

Not much has been done, but I haven't been idle either. Not too diligent, but I've gained a lot.
