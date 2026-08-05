---
title: Example and Explanation of S-PDP File Proof
date: 2022-12-14 21:39:59
tags: File Proof
---

What we now refer to as PDP generally refers to the PDP mentioned in the 2007 paper "Provable Data Possession at Untrusted Stores," with Giuseppe Ateniese as the first author. Before this paper, there were already some concepts of file proof, such as B-PDP, but none could ensure that the server had actually stored the file.

S-PDP is a scheme first proposed in the paper, which can be used by the client to confirm that a file has indeed been saved to the server environment, which is untrusted.

PDP addresses the problem of saving a file to a server where you don't just trust the server's claim of having saved it. Instead, you need a mechanism to confirm the file is really on the server.

There are many types of PDP, including public, private, static, and dynamic. S-PDP is a basic type of public verification PDP.

Homomorphic encryption is key to implementing S-PDP.

### Example

I want to give a simple example to illustrate the process of S-PDP. The paper contains a lot of general definitions and is not very detailed, so I am explaining based on my understanding.

Now the client has an original file with the content:

```
F = 12345
```

Divide this file into smaller blocks, say into 5 parts:

```
F = [1, 2, 3, 4, 5]
```

On the client side, generate an array W composed of random numbers, with the array length matching the number of file blocks. The content of W must be unpredictable:

```
W = [8, 1, 7, 3, 6]
```

Generating homomorphic encryption tags involves 2 steps. First, use additive homomorphic encryption on F and W, then use the client's public key to perform asymmetric encryption on the homomorphically encrypted array:

```
T = r[ h(9),  h(3),  h(10),  h(7),  h(11)]
  =  [rh(9), rh(3), rh(10), rh(7), rh(11)]
```

The client will send the original file F and the homomorphic encryption tags T to the server for storage, keeping only the locally generated random array W, which is the only private content that cannot be leaked. After sending, the client can delete the local original file F and the homomorphic encryption tags T.

When the client wants to verify the file on the server, the client generates a challenge, for example, to randomly verify the 1st and 3rd blocks:

```
chal = [1, 3]
```

Upon receiving the challenge, the server generates the proof in 2 steps. First, use the client's public key to asymmetrically encrypt the 1st and 3rd blocks of the original file F, then use the homomorphic encryption tags T to perform additive homomorphic encryption:

```
V = [rh(9), rh(10)] - r[h(1),  h(3)]
  = [rh(9), rh(10)] - [rh(1), rh(3)]
  = [rh(8), rh(7)]
```

After the client receives the proof V, it uses the private key to perform asymmetric decryption on the proof:

```
sW = r'[rh(8), rh(7)]
   = [h(8), h(7)]
```

The decrypted sW corresponds to the values at the 1st and 3rd indices of the random array W. Due to the use of homomorphic encryption, the content of W is not leaked during the process.

As long as the user has the initially generated random array W, they can verify that the file exists on the server without the original file. The data occupied by this random array W is very small. Also, because of the use of asymmetric encryption, the server must have both the homomorphic encryption tags T and the original file F to complete the challenge. If the server cheats, the client will not be able to decrypt it, and the server will fail the challenge.

### Limitations

There is an issue with PDP: since V = T - F, if the server pre-saves the entire V, it can pass the challenge even after deleting T and F, and the client would not notice.

This is a limitation of PDP. PDP can only ensure that the server has saved the file at least once (if it doesn't save it at all, it cannot generate V), but it cannot guarantee that the file is continuously stored on the server, nor can it repeatedly verify the validity of the proof.

If the first challenge is [1, 3], and the server passes the challenge and saves the proof for [1, 3], then for any future challenges of [1, 3], the server can directly return the pre-saved proof without needing to compute it again. The client cannot tell whether the proof was generated immediately or earlier. This is a limitation of all PDP proofs.
