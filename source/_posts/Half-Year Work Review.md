---
title: Half-Year Work Review
tags: Work
date: 2023-05-06 19:24:50
draft_date: 2023-05-06 16:33:02
---

A review of the past six months of work. Six months ago, I felt lost because I knew Filecoin was adapting to EVM, shifting the storage track towards the computing track. We couldn't surpass Filecoin.

However, I still gained something, mainly a deeper understanding of file proofs and a more detailed familiarity with the project's code, which I hadn't grasped before.

### Sector Proofs

Sector proofs have always had a problem: the proofs can fail. Initially, I thought the issue was that the contract did not include the file list information during proof checks, leading to expired files not being deleted. Later, I discovered more issues. After fixing the code, if a file was stored on only one storage node, it was fine. But if there were multiple storage nodes, the proof still failed.

The final reason found was that when a file is stored on multiple nodes, and other nodes submit proofs to the contract, if they find the file has expired, they delete the node's corresponding file information in the contract and broadcast to other nodes to delete this file. However, when other nodes received the notification, they only deleted the local issue, not the file information in the contract, leading to frequent discrepancies between local files and those recorded in the contract, causing the proof to fail.

Solving this problem involved thoroughly reviewing the proof-related code.

### EVM Contracts

In last year's [Annual Work Review](/2022/08/27/One Year Work Review/), I mentioned rewriting a Go language contract in Solidity, migrating the original contract to run on Ethereum.

The part of the project that supports Ethereum accounts was completed long ago, allowing storage nodes to use Ethereum nodes and implement file upload and download through Ethereum smart contracts. However, the contract proof section remained unfinished because the project used Bulletproof's zero-knowledge proof, which was too difficult to rewrite in Solidity.

The current approach is to use the POA method, where the contract should perform the proof, replacing it with submitting proof parameters and recording them in the contract. Verification nodes then retrieve the proof parameters from the contract, verify the zero-knowledge proof locally, and submit the proof results back to the contract.

Having reviewed the proof-related code, I can now complete the proof-related part of the EVM contract.

### Why Ignore Layer2

I once considered performing the proofs on Layer2 nodes. I made no progress in this direction for two reasons:

1. Layer2 is positioned as EVM complete, without additional functionalities.
2. Layer2 is also difficult to validate.

The first point means that for users or developers, the significance of Layer2 is to reduce fees. If it performs identically to Layer1, it would be exceptional. Adding extra features to Layer2 might not align with the purpose of such projects.

The second point means that Layer2 projects modify Layer1's node header information, making it hard to implement file validation. It would require recognizing a transaction as a function call of a specific contract in the bytecode layer, verifying zero-knowledge proof parameters, and writing the verification results back into variables at the bytecode level. This is challenging but, if accomplished, would essentially create a business-specific EVM virtual machine, boasting high technical barriers and showing off potential.

In contrast, a simpler approach is to use a client to verify proof parameters and submit the results back. Whether the client is on Layer2 nodes or elsewhere doesn't matter. I chose this simpler approach, which, though less impressive, can be completed within my lifetime and shows results, which is now achieved.

### Solidity Contracts

A brief summary of the issues encountered when migrating Go contracts to Solidity.

Contract size cannot exceed 24 KB, or it will fail to compile. For business contracts, 24 KB is insufficient, requiring splitting into two or more contracts and achieving functionality through inter-contract calls. Splitting isn't difficult; it just requires clarity on the functions, distinguishing external from internal ones.

Storage structures can't include lists, as lists are not fixed-length, making it hard for contracts to calculate space usage. This requires separating lists from structures and using them independently. If familiar with business logic, this is not an issue; otherwise, encountering unfamiliar variable names can be annoying. I was lazy and didn't write the full logic in the contract, taking two days to figure out the problem.

Mappings can't be iterated. Simple original methods need complex rewrites using libraries.

Mappings return default values if elements don't exist. It's impossible to check if a mapping contains a key. If the default value type has meaning in the business, unexpected situations might arise.

These are minor issues but can complicate the coding process as they require different methods to achieve identical logic.
