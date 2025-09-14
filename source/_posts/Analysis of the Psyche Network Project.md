---
title: Analysis of the Psyche Network Project
date: 2025-08-01 14:33:21
tags:
- Project Analysis
- AI
---

### Project Background

[Psyche Network](https://psyche.network/runs) is a project in the AI + Web3 space, developed by the [Nous Research](https://nousresearch.com/) team. Two months ago, it secured a [Series A funding](https://cointelegraph.com/news/nous-research-raises-50m-paradigm-decentralized-ai-solana) of $50 million from Paradigm.

The project background is detailed in [the official article](https://nousresearch.com/nous-psyche/). The Nous Research team developed a decentralized algorithm called DeMo, which enables the training of large language models (LLMs) on a distributed network, avoiding the high coupling of cluster-based services. It's similar in concept to how mining pools break down large computational tasks and distribute them to various Client nodes, which then compute and return results for aggregation.

Of course, LLM training and mining pool algorithms are fundamentally different; the analogy is just to aid understanding. The detailed algorithmic explanation of how DeMo splits and merges tasks can be found in [the official post](https://blog.lambdaclass.com/introducing-demo-decoupled-momentum-optimization-for-efficient-distributed-llm-training/). Honestly, I didn’t fully grasp it—it's full of vector, weight, and loss function jargon. How it prevents nodes from submitting fake data should also be part of the algorithm's design, so I won’t go deeper into the algorithm's effectiveness here.

In DeMo’s [paper](https://arxiv.org/pdf/2411.19870), they used 100 billion tokens for training and testing, achieving good results. What does 100B tokens mean? For example, [DeepSeek-V3](https://github.com/deepseek-ai/DeepSeek-V3?tab=readme-ov-file#4-evaluation-results) has 15 TB of tokens. So, DeMo’s experimental scale is still far from commercial-grade products. Here's a comparison with other models' token counts:

| Model | Parameters | Pretraining Tokens | Public Sources or Leaks |
|-------|------------|--------------------|--------------------------|
| GPT-3 | 175 B | ≈ 499 B | Papers and follow-up reviews |
| GPT-3.5 | 175 B | Estimated ~1 T | — |
| GPT-4 | 1.7 T | ≈ 13 T tokens | SemiAnalysis / The Decoder reports |
| Llama 3 | 70 B | > 15 T tokens | Meta official model card |
| DeMo OLMo | 1 B | 0.1 T tokens (100 B) | DeMo paper |

Psyche Network builds a distributed network based on DeMo’s algorithm principles, combining it with blockchain. The first phase aims to train a model with 40 B parameters and 20 T tokens. Regarding `parameters` and `tokens`, my understanding is that `parameters` are a fixed metric defined at the beginning of training, while `tokens` require ongoing computation and training—DeMo solves the distributed computing for `tokens`. The Psyche Network website displays real-time training progress, and it has already reached 1 TB of tokens:

<img src="1.png" width="80%">

Once training is complete, the model might approach GPT-3's level. While the token count is higher than GPT-3, the parameters are fewer, so the final performance is likely inferior.

### Project Architecture

Psyche Network’s [documentation](https://docs.psyche.network/explain/index.html) outlines the overall architecture, which is fairly easy to understand. There is a centralized Coordinator responsible for creating training tasks, while Clients receive and execute those tasks and submit results. Without blockchain, communication between Coordinator and Clients occurs via direct TCP connections. With blockchain, messages are passed through the blockchain instead.

<img src="2.png" width="40%">

In the [code repository](https://github.com/PsycheFoundation/psyche/tree/main/architectures), both `centralized` and `decentralized` architectures are maintained. This isn't exactly a good sign, as it suggests the project was initially designed to run in a centralized manner and is now being retrofitted for decentralization. So, the degree of decentralization is inherently limited.

For the decentralized part, Psyche Network chose Solana as the smart contract platform, likely due to its original use of Rust.

Under the decentralized directory in the codebase, there are several Solana contracts responsible for creating training tasks, calculating each Client node’s reward, and distributing those rewards.

Psyche Network is currently in the testnet stage, and all on-chain transactions occur on Solana’s Devnet. You can see the contract address in the `declare_id!()` statements in the source files. For example, the coordinator contract's address is `HR8RN2TP9E9zsi2kjhvPbirJWA1R6L6ruf4xNNGpjU5Y`, which shows frequent transactions on [the blockchain explorer](https://solscan.io/account/HR8RN2TP9E9zsi2kjhvPbirJWA1R6L6ruf4xNNGpjU5Y?cluster=devnet).

As for reward calculation, because there is a centralized Coordinator, the process is simple: the Coordinator validates the result from a Client, and if valid, initiates an on-chain transaction to credit the Client. The relevant code is in [these two lines](https://github.com/PsycheFoundation/psyche/blob/main/architectures/decentralized/solana-coordinator/programs/solana-coordinator/src/instance_state.rs#L146-L149):

<img src="3.png" width="80%">

Each Client’s score is recorded in the contract. To claim rewards, Clients go to the treasurer contract and initiate a claim. The treasurer calculates and transfers tokens based on the score and exchange rate.

So which token does the treasurer distribute? The token is [specified](https://github.com/PsycheFoundation/psyche/blob/main/architectures/decentralized/solana-treasurer/programs/solana-treasurer/src/logic/run_create.rs#L34) by the Coordinator when creating the task. Any standard SPL token is supported.

<img src="4.png" width="70%">

Overall, Psyche Network uses the Solana blockchain to record task meta information, compute rewards, and distribute them. As long as Client participation is permissionless, the project indeed achieves its goal of decentralized LLM training.

Token distribution and reward mechanisms are common in blockchain projects, but they at least offer transparency. It’s likely that Psyche Network will eventually issue its own token, and rewards will be paid exclusively in it. It might even evolve into a full-fledged LLM training task platform, where third parties can create tasks and distribute rewards—similar to Eigen Layer.
