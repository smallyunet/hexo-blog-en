---
title: Want to Develop a Minimal EVM Virtual Machine
date: 2025-05-11 01:15:07
tags:
- Plan
- EVM
---

I've named this project echoevm.com. The main goal is to start from the simplest stack operations and gradually build a complete Ethereum bytecode execution environment.

Why choose this direction? Let’s analyze the technical modules of an Ethereum client:
1. RPC: A GRPC wrapper? The focus is more on protocol design than technical implementation.
2. P2P: There’s an existing libp2p available — it's mostly about node discovery and routing tables, maybe dig into Kademlia DHT?
3. Account system: ECDSA? Cryptography?
4. Transaction pool: Transaction analysis, sealed transactions, MEV protection directions.
5. Consensus mechanism: The design of consensus mechanisms belongs in the research realm — typically PhD-level research in labs, publishing papers, producing test data to prove cutting-edge optimizations, and finally securing funding to hire engineers for implementation.
6. Storage: Experts in database internals are useful anywhere; it's not specific to blockchain.
7. Data structures: Study the implementation of Merkle Patricia Trees?
8. State synchronization: Light client direction — for example, using Celestia’s core tech to separate execution from storage, or offloading Archive node data?

Overall, I lean toward doing something engineering-focused rather than academic, yet still technically challenging. It should be meaningful both for personal technical growth and for the potential outcomes. If this minimal EVM is successfully developed, it can lead to a series of follow-up achievements, and many valuable products can be built on top of it.

The conversion from Solidity to bytecode is compiler expert territory. What I aim to do is execute the bytecode — starting with the simplest operations like addition and jump, then move on to gas calculation, context switching, and ultimately be able to execute all historical Ethereum transactions.

<br><br>

### v0.0.2 (2025.06.09)

This version adds the ability to run runtime bytecode, meaning you can first deploy a contract and then call the deployed contract's functions with specific parameters. For example:

```
go run ./cmd/echoevm -bin ./build/Add.bin -function 'add(uint256,uint256)' -args "3,5"
```

This command executes the bytecode from the `./build/Add.bin` file and calls the [add function](https://github.com/smallyunet/echoevm/blob/v0.0.2/test/contracts/Add.sol#L7), passing in the arguments 3 and 5. After the program completes, it will return the computation result, which is 8.

<br><br>

### v0.0.1 (2025.05.27)

Implemented a very simple version. Now you can use `solc` to compile an [Add.sol](https://github.com/smallyunet/echoevm/blob/v0.0.1/test/contracts/Add.sol) contract, and then have `echoevm` read the generated `Add.bin` deployment code, which will output the contract's runtime code after deployment.

During the implementation of this version, I learned the difference between deployment code and runtime code. Typically, we first deploy a contract to the blockchain and then make calls to it. These are actually two different operations, but both use the same EVM execution. The EVM doesn’t care whether the input bytecode is for deployment or invocation; it simply handles different opcodes differently. Deployment code generally includes both the `CODECOPY` and `RETURN` opcodes, which can be used to distinguish the type of input.


