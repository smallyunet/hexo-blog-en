---
title: 'Proofs of Retrievability: Understanding Document Verification'
date: 2022-12-16 17:22:05
tags: File Proof
---

PORs (Proofs of Retrievability) and PDP were published in the same year, 2007. The paper is titled "PORs: Proofs of Retrievability for Large Files," with Ari Juels as the first author.

PORs not only prove that a file is indeed stored on the server, but also whether the file has been modified or deleted, a capability that PDP lacks. However, the limitation of PORs is that they can only be used for encrypted files, meaning the file must be encrypted. The key to implementing PORs lies in the complex setup phase.

The idea behind PORs is that the client generates some random encodings recorded in an array, called sentinels. For example, `[6, 4, 0]` are three sentinels.

First, the client's file is symmetrically encrypted, and it must be done using a block cipher method. During the encryption process, the sentinels are inserted into random positions in the file. Between two sentinels, there might be 1 block or countless blocks; the positions of the sentinels are random.

Then the encrypted file is sent to the server for storage.

The content of the client's challenge is to ask the server to return the encodings at n random positions in the file. The challenge includes the positions of the sentinels.

Since the file is encrypted, the server cannot determine whether the challenge positions are from the original file data or the sentinel data. The server finds it difficult to predict the positions of the sentinels.

For the client, it only needs to verify the encodings corresponding to the sentinel positions to determine if the file exists and is intact. If the file is modified or some data in between is lost, the encoding at the sentinel positions will be vastly different, making the challenge impossible to complete.

Retrievability means that for the client, it can verify a part of the file by specifying the sentinel positions. For example, it can verify the file between the 1st and 3rd sentinels, or between the 5th and 9th sentinels, pointing and checking as needed.

As for why the file must be encrypted, on one hand, PORs rely on symmetric encryption's block cipher, and on the other hand, for unencrypted files, the server can more easily predict the sentinel positions. Therefore, PORs can only be used for encrypted files.

The paper also introduces error-correcting codes to enhance the file's fault tolerance. In the setup phase, error-correcting codes are added to the blocks before symmetric encryption. The advantage of introducing error-correcting codes is that within a certain error range between two sentinels, if the file is damaged, it can be repaired.

Why do error-correcting codes become important in the PORs mechanism? Because they combine with the characteristic of PORs, Retrievability. If the file is stored in multiple server environments and suffers different degrees of damage, the presence of sentinels makes it easier to recover parts of the file from other servers. In this context, the introduction of error-correcting codes further strengthens the file's fault tolerance.
