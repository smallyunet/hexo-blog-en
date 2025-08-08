---
title: Design of a ZK-Based On-Chain Identity System
tags: 
- zk
- planning
date: 2025-04-30 22:18:54
draft_date: 2025-04-30 21:59:39
---

I've named this system **zkgate.fun**, aiming to leverage the features of zero-knowledge proofs in combination with blockchain to create a small tool. The key function is to allow users to prove they belong to a certain group without revealing their real on-chain identity.

Here's the current idea: the administrator starts with a list of addresses (e.g., an array of Ethereum addresses), then calculates a Merkle Root Hash based on this list and submits it to a smart contract. Individuals in the list can use a Circom circuit's proving key to generate a zk proof for themselves, and then submit this zk proof to the smart contract.

The smart contract will use the `verifier.sol` generated from the Circom circuit to verify the zk proof and determine whether the address used to generate the proof is included in the Merkle Root Hash. The result of this verification is then returned.

With this approach, the admin doesn't need to publicly disclose the addresses in the group, and the addresses within the group also don't need to declare their identity. They just need to submit the zk proof generated using zero-knowledge proof to prove their membership in the group. I’ll proceed to implement this technically.

<br>

### Update v0.1.0 (2025.05.09)

First, a correction to the previous design: the administrator **must** publicly disclose the address list of the group. Otherwise, it’s impossible to generate a Merkle Tree, and users can’t locate their address’s position in the tree or generate the necessary path proof.

On a positive note, a very basic demo now works ([smallyunet/zkgate-demo](https://github.com/smallyunet/zkgate-demo)). While the demo lacks features (it can’t even verify address ownership in the circuit), it proves the toolchain process.

The specific implementation steps:

1. An [off-chain script](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/offchain/smt.js) generates [inputs.json](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/offchain/inputs.json) from the address list and the user's own address. This file includes the Merkle Root Hash and path information needed for verification.
2. The [circuit code](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/circuits/merkleSmtProof.circom) is compiled into [binary files](https://github.com/smallyunet/zkgate-demo/tree/main/circuits/build) used to generate witness files.
3. A `.zkey` file is created using the public [ptau file](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/circuits/run.sh#L17-L28).
4. From the `.zkey` file, export [proof.json](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/circuits/proof.json), [public.json](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/circuits/public.json), and [verification_key.json](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/circuits/verification_key.json). These three can be used for off-chain verification.
5. Export the [.sol contract file](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/circuits/contracts/Groth16Verifier.sol) from the `.zkey` file and deploy it on-chain.
6. Use the contents of `prove.json` and `public.json` as [parameters](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/hardhat/scripts/prove.js#L41) to call the contract’s [`verifyProof`](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/circuits/contracts/Groth16Verifier.sol) function. If the proof is valid, it returns `true`; otherwise, `false`.

If an address is not in the group list, there are two cases:

1. Attempting to generate `inputs.json` using an [unauthorized address](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/offchain/smt_non_member.js#L24) will result in an error during proof generation.
2. Attempting to submit a fake [proof](https://github.com/smallyunet/zkgate-demo/blob/v0.1.0/hardhat/scripts/fakeProofWithCorrectRoot.js#L26) to the contract will fail verification.

Currently, this primitive demo uses plaintext addresses to build the proof, like:

```js
const members = [
  "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266",
  "0x70997970C51812dc3A010C7d01b50e0d17dc79C8",
  "0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC",
];

const proofKey = toField(members[0]);
const { siblings } = await tree.find(proofKey);
```

This checks whether `members[0]` is part of the tree built from `members`. Clearly, it is. To fake it, just replace the address:

```js
const nonMemberAddress = "0x1234567890123456789012345678901234567890";
const proofKey = toField(nonMemberAddress);
const { siblings } = await tree.find(proofKey);
```

So the `members` list must be public, and the current program only checks if an address is in that list. Even if `members[0]` isn’t my address, I can still use it to build a valid proof. Then what’s the point of zk?

Next step: require users to **sign a message** with their private key, then recover the address from the signature **inside the zk circuit**, and check if the recovered address is in the members list.

Sounds simple? In reality, using a zk circuit to recover an address from an ECDSA signature is absurdly complex—like building a nuclear reactor out of Lego. No wonder zk devs are losing their hair.

<br>

### Update v0.2.0 (2025.05.13)

This version solves the problem of address ownership verification. The main idea is to separate the zk proof from the address ownership proof: off-chain zk proves the address's position in the Merkle Root, while on-chain the user submits a signature of the root using their private key. The contract recovers the address from the signature and compares it with the address in the zk proof.

```
1. zk proof includes address info -> on-chain verification reveals address info
2. Private key signs root -> signature submitted -> contract recovers address

3. Compare zk proof address == recovered address
```

Code changes:

1. Off-chain code doesn't need changes — the [inputs](https://github.com/smallyunet/zkgate-demo/blob/v0.2.0/offchain/smt.js#L37) already include the key.
2. In the circuit code, the key is made [public](https://github.com/smallyunet/zkgate-demo/blob/v0.2.0/circuits/merkleSmtProof.circom#L27).
3. The smart contract accepts the [signature](https://github.com/smallyunet/zkgate-demo/blob/v0.2.0/hardhat/contracts/ZkGateRegistry.sol#L38), [recovers the address](https://github.com/smallyunet/zkgate-demo/blob/v0.2.0/hardhat/contracts/ZkGateRegistry.sol#L49), and compares it with the address in the proof.
4. The script that calls the contract must [sign the root](https://github.com/smallyunet/zkgate-demo/blob/v0.2.0/hardhat/scripts/prove.js#L44-L45) and pass the signature to the contract.

As of now, **zkgate.fun** allows group admins to only publish a Merkle Root Hash on-chain, keeping the full member list private. Group members just need the full list and a signature from their address’s private key to generate a zk proof and prove their membership.

The only information not hidden by zk is the user’s address, which must still be submitted for verification.

<br>

### Update (2025.05.14)

There is an existing zk protocol supported by the Ethereum Foundation, with a mature toolchain and ecosystem, called **Semaphore**. Try its demo with a frontend UI here:

- https://semaphore.pse.dev/

In the first two versions of zkgate.fun, I avoided using Semaphore’s EdDSA account system, preferring to stick with Ethereum’s native ECDSA. However, only EdDSA is zk-friendly: it can use Poseidon Hash signatures that zk circuits can verify directly—unlike the "off-chain sign, on-chain recover" clumsy approach I used.

From a learning standpoint, this project taught me how zk toolchains work in just a few days. But from an industry perspective, there’s no way I could outperform Semaphore on my own. Even with a frontend UI and visual interactivity, zkgate.fun would just end up like [Semaphore’s Demo](https://demo.semaphore.pse.dev/), but technically inferior.

Therefore, I’ve decided to stop developing **zkgate.fun**. The domain will expire in a year and will not be renewed.
