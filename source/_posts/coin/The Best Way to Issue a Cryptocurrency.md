---
title: The Best Way to Issue a Cryptocurrency
tags: Blockchain
date: 2025-01-10 00:39:12
draft_date: 2024-12-02 13:56:49
---

A few days ago, a well-known figure from a non-Internet-related industry issued a memecoin on pump.fun. Over the past two days, there has been public discussion about migrating the memecoin to a dedicated blockchain, such as launching a new chain while preserving original addresses and balances.

The motivation behind this memecoin is simple: the issuer needs to raise funds to support their activities. However, due to the nature of these activities, many wallets have blacklisted the memecoin.

Of course, we won’t discuss the motivations behind issuing a cryptocurrency here. Instead, we’ll focus on the technical methods of issuing a cryptocurrency and compare them.

### The Best Method

To start with the conclusion: the most reliable technical solution for issuing a cryptocurrency is to use an Ethereum client to run a blockchain with a different Chain ID from Ethereum.

### Why Not Pump.fun

Pump.fun is an excellent platform that allows for one-click token issuance while ensuring contract security, no rug pulls, no presales, and no security vulnerabilities. This makes it much more convenient and secure than writing a smart contract from scratch.

Pump.fun’s greatest strength is that tokens can be traded immediately using a bonding curve mechanism to define prices. This allows a token to be tradable even with very few traders, which is something other issuance methods cannot achieve.

However, pump.fun has its drawbacks. Firstly, it operates in the SOL ecosystem. It’s unclear why pump.fun chose Solana over Ethereum initially. Before purchasing a newly issued token, buyers need to understand and own SOL. While SOL is widely recognized, why should buyers have to learn about it first?

Secondly, if a token's market cap is below $100K, it is tied to the pump.fun platform. Without visiting the pump.fun website, there is no way to trade it. If the site or domain disappears, the token loses its trading entry, which is detrimental to long-term development. Tokens with a market cap above $100K can enter Raydium, but how many buyers understand Raydium? This creates reliance on a DEX platform.

Thus, while pump.fun offers convenience, users must bear the risks of the platform and the Solana blockchain. Will Solana exist long-term? How long is "long"?

Pump.fun accurately positions itself as a memecoin issuance platform.

### Why Not PoW

PoW is the most decentralized form of blockchain technology, but the cost of launching a PoW chain is too high. There are no ready-made technical frameworks to reuse, requiring hardcore technical expertise. Additionally, maintenance costs are high—if no one mines, the issuer has to mine themselves, and the hash rate cannot be too low.

### Why Not Cosmos

Cosmos-based projects often carry negative connotations such as BFT (Byzantine Fault Tolerance) and consortium chains, making it an unattractive choice.

ATOM, Cosmos’s native token, is only ranked around #50 in market cap. The ecosystem has its niche use cases, but if there's no compelling reason to use Cosmos, it’s best to avoid it.

Additionally, Cosmos’s ecosystem is underdeveloped—there is still no wallet like MetaMask that can connect to any RPC. Using Cosmos would bring many challenges.

### Why Not Polkadot / Avalanche

Even blockchain professionals struggle to fully understand and use Polkadot and Avalanche. The issue isn't that they are incomprehensible, but rather that they have a high learning curve.

For example, Polkadot’s Existential Deposit mechanism is perplexing—why does it even exist? There are many other unknown pitfalls, making it a risky choice.

### Why Not Ethereum Layer 2

Ethereum Layer 2 (L2) solutions were not designed for token issuance but rather for projects to earn protocol fees. L2s extend Ethereum, but their native tokens are still ETH.

While L2 technology is open-source and functional, it requires centralized operation and continuous fault proof submissions to Layer 1. Moreover, transaction fees must be paid in real ETH, which can be expensive. If there aren’t enough users, transaction fees alone could be a financial burden.

### Why Not Issuing an ERC-20 Token on Ethereum

Most people cannot write smart contracts safely. Security vulnerabilities are a major risk—even if the issuer believes the contract is secure, buyers may struggle to verify its safety. Identifying secure contracts is difficult, making this method unadvisable.

What about issuing an ERC-20 token on Layer 2? The problem is choosing which L2 network to use. While L2 fees are lower, data between different L2s is not interoperable. In a way, L2 solutions fragment Ethereum rather than strengthen it.

### Why Ethereum

There are undeniable facts:
1. Ethereum’s EVM is the most widely recognized and used smart contract standard in the blockchain industry.
2. Ethereum’s PoS is the most decentralized consensus mechanism aside from PoW.
3. Ethereum’s dominance is unshakable – ETH killers may surpass Ethereum in some metrics, but the EVM standard will remain.
4. Ethereum’s community and ecosystem are more active, with better infrastructure than other blockchains.
5. New blockchain projects aim to be EVM-compatible rather than replacing EVM.

Given these facts, the best way to launch a blockchain network is to use an Ethereum client, modify the configuration files and startup parameters, and launch a Layer 1 network.

From a network operations perspective, even if the Ethereum client is never upgraded again, the network can still run stably for a long time.

Regarding transaction fees—if the token price is low, fees will be cheap. Thus, fees are unlikely to become an insurmountable issue.

### How to Solve Initial Trading and Airdrop Issues

Apart from pump.fun and PoW chains, all other token issuance methods face the problem of initial liquidity—the token won’t be immediately listed on CEX or DEX platforms. How can buyers purchase it? What can they use to buy it?

The only viable solution is ICO-style presales, where the network officially launches at the Token Generation Event (TGE). Although this process often involves controversies and disputes, using Ethereum nodes ensures that the Genesis file clearly defines every address, making the network credible and widely accepted.

Once the network genesis is established without issues, the rest can be managed through the network’s own inflation model, allowing it to operate smoothly in the long run.
