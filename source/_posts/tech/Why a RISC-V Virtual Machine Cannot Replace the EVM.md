---
title: Why a RISC-V Virtual Machine Cannot Replace the EVM
tags: Blockchain
date: 2026-04-24 20:34:05
draft_date: 2026-04-24 19:03:12
---

1. RISC-V is too complex and depends heavily on standardizing the virtual-machine sandbox environment, but there is no unified specification today.
2. The EVM has been tested for years by a financial ecosystem worth tens of billions of dollars and is relatively mature from development through auditing. The cost of switching to RISC-V is very high.
3. A RISC-V virtual machine pays a higher von Neumann tax and has more fragile determinism.

When Gavin Wood designed the EVM, he faced two questions: should it be stack-based or register-based? The actual EVM is stack-based, perhaps because of external factors such as the need to launch and iterate, or perhaps because of Gavin's historical limitations at the time. After Gavin left Ethereum and created Polkadot, he initially designed Polkadot's virtual machine around WASM and more recently shifted toward a RISC-V instruction-set virtual machine for general-purpose computation. In other words, Gavin abandoned the old path of the stack-based EVM and eventually moved to the register-based structure of RISC-V.

At its foundation, the EVM uses 256-bit unsigned numbers. Every instruction and every piece of data is broken down and represented as a 256-bit number. Why 256 bits? Perhaps because Ethereum evolved from Bitcoin and retained designs such as the Keccak-256 hash algorithm and the secp256k1 signature algorithm, both of which produce 256-bit outputs. A 256-bit word size allowed these cryptographic algorithms to fit seamlessly.

The CPUs in ordinary computers today are mostly 64-bit. CPUs went through a 32-bit era, but under memory addressing, 32 bits can represent at most 2^32 bytes = 4 GB of memory, which no longer meets modern needs. CPUs today are therefore 64-bit.

We now have two numbers: the EVM is 256-bit, while the CPU is 64-bit. This means that every EVM instruction requires at least ten CPU instructions to implement: simulating stack memory, calculating the low bits, calculating the high bits, pushing the result back onto the stack, and so on. After this whole sequence, executing a 256-bit EVM instruction is roughly 10 to 100 times slower than using 64-bit CPU instructions.

RISC-V is a reduced instruction-set architecture standard that includes a 64-bit RISC-V virtual-machine instruction standard. This means that with JIT compilation, RISC-V instructions can map almost one-to-one onto modern 64-bit CPUs, giving execution efficiency close to that of the CPU itself.

Arbitrum once had an upgrade called Stylus. Its [release material](https://chronicle.castlecapital.vc/p/state-arbitrum) compared the performance of a WASM virtual machine with the EVM. The official claim was that a simple `ADD` operation consumed 150 times less gas in WASM than in the EVM, and this factor of 150 refers to the overhead of instruction-set conversion. WASM performs worse than RISC-V because WASM was designed from the beginning as a virtual machine for running multiple programming languages in the browser. It is therefore reasonable to infer that RISC-V instructions are at least 100 times faster than EVM instructions.

In terms of instruction-execution efficiency, RISC-V is unquestionably far better than the EVM.

RISC-V is also highly suitable for general-purpose computation. [Cartesi](https://cartesi.io/), for example, effectively runs a small Linux system locally on every on-chain node. What can run inside that system is not limited to smart contracts; it can include all kinds of process-level programs, opening up enormous room for imagination.

Many zkVMs are also based on RISC-V. Developing ZK circuits for a 256-bit virtual machine with a variety of complex instructions creates too much cognitive burden. RISC-V is a reduced instruction set containing only relatively low-level instructions, making a zkVM based on it easier to implement and more general than a zkEVM.

On the EVM side, chains such as [Monad](https://www.monad.xyz/) have also achieved outstanding performance breakthroughs by implementing a genuinely parallel EVM. Monad did not improve performance at the underlying instruction-set level. Instead, it uses optimistic execution and rolls back on conflicts, and it implements a specialized database rather than a general-purpose key-value database to improve disk I/O efficiency. Together, these changes significantly increase EVM execution speed.

Up to this point, we can see that virtual machines and smart-contract languages based on the RISC-V instruction set have many performance advantages.

Does that mean RISC-V is the best trend for replacing the EVM? Obviously not.

The ecosystem moat is important. RISC-V completely abandons the EVM ecosystem, builds an independent system, and supports a variety of compiled programming languages. That means all the lessons developers and auditing firms learned from mistakes in the EVM ecosystem would have to be learned again from scratch. Nobody wants that. The EVM's simplicity creates a performance disadvantage, but it also makes security risks and execution logic easier to inspect. Because it uses a stack structure, Solidity does not even provide pointer operations, and its determinism is extremely high. Under RISC-V's register model, the contract ABI is no longer bytecode but an ecall ABI. It involves memory allocation, heap memory, garbage collection, and very high complexity.

To solve the determinism problem, the usual approach is to restrict another layer on top of the programming languages supported by RISC-V, execute contract logic in a sandbox, and disable certain language features so that a deterministic result is visible outside the sandbox. This naturally leads to every project defining its own sandbox standard, with no unified industry solution.

There is also a problem with the way many projects market themselves: they magnify RISC-V's performance advantages while deliberately ignoring the von Neumann tax introduced by the RISC-V model. In the EVM, modifying a balance is a single `SSTORE` instruction. The EVM inherently knows what an account is, what a balance is, and where to find the data in the database. RISC-V is too general-purpose. Modifying a balance may require allocating memory, handling language-runtime boundary checks, writing data, freeing memory, and so on. Although each individual instruction executes more efficiently, the business logic consumes more CPU cycles instead.

Overall, a RISC-V virtual machine still cannot replace the EVM.
