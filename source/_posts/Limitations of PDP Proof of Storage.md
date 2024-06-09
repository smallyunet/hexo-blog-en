---
title: Limitations of PDP Proof of Storage
tags: Proof of Storage
date: 2023-05-17 15:53:06
draft_date: 2023-05-17 09:50:57
---

### Proof of Storage

The most basic use of PDP (Provable Data Possession) for a file is to generate a corresponding TAG based on the file content, then send the file to another environment. Subsequently, use the saved TAG to verify if the other party has indeed stored the file. If the other party hasn't stored the file, it is impossible to pass the initial verification. In the subsequent verifications, since the challenges are randomly generated and select segments from different parts of the file, the reliability of the verification can be probabilistically improved.

Therefore, this usage at least proves that the file is stored in the other party's environment. However, this method of proof has a flaw: the file does not need to be complete, or in other words, it lacks proof of file integrity. If the file is very large and the number of challenges is generally fixed, the larger the file, the less comprehensive the challenges. Although the file segments for the challenges are random, there is still a possibility that the other party does not need the complete file but only a part of it—2/3, 1/2, or 1/3—to complete the challenge.

### Proof of File Integrity

To enable PDP to prove file integrity, PDP proof can be combined with the data structure of a Merkle tree. PDP proof typically requires the file to be split into data segments and generates TAGs based on these segments, without emphasizing the serialization of the data segments.

The combination with a Merkle tree comes into play after splitting the file into data segments, then serializing these data segments into the Merkle tree. Due to the properties of a Merkle tree, the value of the parent node depends on the values of the child nodes to be computed. If the parent node is verified to be correct, it indicates that all child nodes exist, indirectly proving the file's integrity.

In practical use, some nodes of the Merkle tree are often extracted, and the paths of these Merkle nodes are verified, which is a common method to verify the integrity of the Merkle tree. By using the Merkle tree as the data structure for the file and verifying the Merkle tree's integrity while verifying the PDP proof, it can be ensured that the file exists in its entirety.

The coverage of Merkle tree verification cannot be 100%, but it is generally believed that the cost of forging Merkle tree nodes is higher than the cost of actually retaining the complete data.

### Proof of File Size

PDP proof itself does not include the file's metadata; it can only prove that the file exists and is complete, but it does not provide information about what the file is, the file name, file type, or file size. PDP proof information does not include file metadata, and useful metadata includes file size. PDP is not a technology of the blockchain era, and it does not concern itself with this issue.

Some blockchains describe the computational power of nodes by the size of disk space or charge users based on file size or reward storage nodes according to the stored file size. This makes the file size information crucial.

To address the issue of missing file metadata, one approach is to associate metadata with file data outside the PDP proof system. Another approach is to modify the file by attaching metadata to the file data. As long as the integrity of the file data is proven, the accuracy of the file metadata can be confirmed.

However, neither method can guarantee the reliability of file metadata. In the blockchain scenario, the trust model has changed, differing from the issues PDP aims to solve. Previously, PDP was to let users trust storage nodes, but now, it is to let the blockchain trust storage nodes.

The first approach requires additional mechanisms to ensure the mapping relationship between metadata and file data, which incurs significant overhead. The second approach is simpler to implement, but the problem lies in who attaches the metadata to the file data. Is it trustworthy? What if incorrect information is maliciously attached? Additionally, how to read this metadata? Should the file data or part of the file data be downloaded? Even so, the downloaded information cannot be guaranteed to be correct.

In conclusion, within the realm of cryptography, PDP cannot solve the proof of file size.

### Proof of File Continuity

PDP proof requires continuous challenge requests, where successful challenges indicate the file is intact. For example, if a challenge is initiated now and another one an hour later, it at least shows that the file was intact at the time of each challenge. But what about the times when there is no challenge? There are no restrictions.

If, after the first challenge, the file is moved elsewhere and then brought back for the second challenge, this is entirely possible. PDP does not and cannot prevent such situations. PDP cannot provide proof of file continuity.

What impact does this limitation have on blockchain? The first thing that comes to mind is exchanging computational power for space. Because in some blockchain rules, the larger the file, the higher the miner's earnings. Miners can utilize the interval between PDP proofs to free up space for other files, exchanging disk IO for disk space size.

Additionally, the cost of proof increases. The more frequent the challenges, the safer the file, which imposes pressure on the party seeking proof.

### Proof of Multiple File Copies

PDP naturally does not have the ability to prove the existence of multiple copies of a file. If two copies of the file are required, but the other party only actually stores one, PDP will have no awareness of this, which is easy to understand.

If you want to disperse the risk of file loss by storing the file in different locations, using PDP as a verification method cannot achieve this goal.
