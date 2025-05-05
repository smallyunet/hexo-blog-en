---
title: About the Design of the Web3 Tipping System giveme.wtf
tags: 
- web3
- Plan
date: 2025-04-29 19:26:45
draft_date: 2025-04-29 00:45:36
---

### Product Form

#### Introduction

giveme.wtf is a domain name I just registered, planning to create a small Web3 tipping tool, similar to these Web2 platforms:

- https://buymeacoffee.com/
- https://linktr.ee/

The difference is that on a personal page of giveme.wtf, a Web3 wallet receiving address and a QR code will be displayed, just like PayPal's personal payment link, supporting multiple blockchain address formats, including Bitcoin, Ethereum, Dogecoin, etc., which can be freely chosen.

giveme.wtf does not handle any funds; it only displays tipping address information. For example, visiting giveme.wtf/{username} will show the receiving address information set by the user, including Ethereum address text, QR code, etc. It's that simple.

Of course, under giveme.wtf/{username}, users can also set a simple bio, avatar, domain name, social media links, etc., like a small personal homepage, making it a bit more worth sharing.

#### Expansion

Later, based on on-chain data, it can generate statistics for addresses using the tipping system and the total amount of tips received, creating a leaderboard categorized by username or chain.

If you rank high, your bio under your username can gain more exposure. Help your idol climb the ranks and stay on top!

Additional features like a 24-hour leaderboard, PK-style competitions, etc., can also be added.

It could also extend to social systems, where addresses with tipping records form a relationship graph, or even use IM tools for direct communication or auto-grouping.

### Technical Implementation

#### Minimum Viable Product

- Registration

Users register with MetaMask wallet. After connecting the wallet, they can set a username, which is globally unique and managed by a smart contract. Users need to send a transaction to interact with the contract to submit their desired username.

- Profile Information

After binding the EVM address with the username, users can set profile information, including avatar, bio, wallet addresses, etc.

Once filled out, the frontend submits the data to the backend, which saves it to an IPFS node (keeping it pinned long-term) and generates a CID, which is then returned to the frontend.

After receiving the CID, the frontend initiates another contract interaction to map username -> CID in the smart contract. This step can be combined with the registration or separated, depending on whether the user wants to set a profile immediately.

- Display

The username->CID mapping on the contract is the most authoritative data source. The frontend page retrieves the CID based on the username from giveme.wtf/{username}, queries the specific data from an IPFS gateway, and renders the page based on that data.

The profile will be very lightweight JSON data, and to speed up gateway queries, services like Cloudflare's Web3 gateway CDN can be used.

- Network Choice

The smart contract will be deployed on Base.

### Future Optimizations

#### Username Recovery

The problem with MetaMask registration is: what if the wallet is lost? Users would lose control over their username. A recovery mechanism could be designed where users can set a list of recovery addresses. Any address in the list could reclaim control over the username and change the corresponding CID. This mainly addresses wallet loss.

If a wallet is hacked, the hacker already controls the username and can modify the recovery list to solidify their control. In Web3, once compromised, there's no way to reclaim it.

#### Network Selection

Choosing which chain to deploy on is a critical issue. Users may not have the necessary tokens for gas fees.

For example, if Base is selected, users must have ETH first, then ETH on Base to operate. These steps alone could deter most users.

To address this, future directions could include gas fee sponsorship using ERC-4337's paymaster (although it has cooled down) or Meta Transactions. However, issues like "farming" gas fee subsidies must be considered.

#### Data Availability

In the MVP, data is stored on IPFS but maintained by only one server. IPFS is a low-level file routing protocol, and a lightweight layer (similar to but simpler than Filecoin) can be added.

Given giveme.wtf's small data volume, simple solutions like verifying Merkle Root Hashes can be used. A mechanism can be designed to reward nodes for storing complete data and regularly verify storage integrity.

#### Off-chain Data Caching

Querying username->CID from the smart contract for every page load is slow and consumes RPC resources. An off-chain caching mechanism is needed, such as a centralized backend service that listens to contract events and writes the username->CID mappings into Cloudflare Workers KV.

The frontend first queries Cloudflare Workers KV, falling back to the contract only if needed.

However, if the centralized service is compromised, it could modify mappings, causing funds to be misdirected.

This integrity issue is what Optimistic Rollups address, with relatively mature solutions. Combining this with Zetachain’s cross-chain logic:

- The off-chain service constructs a Merkle Tree using username->CID mappings, with the Root Hash acting as the integrity proof.
- The Merkle Root is periodically submitted to the contract, and the frontend can verify the Root Hash to ensure the CID hasn’t been tampered with.
- Multiple off-chain indexers can collectively agree on a private key through TSS, and only this key can submit the Merkle Root to the contract, similar to multisig.

Finally, introduce a cooldown and challenge period: the newly submitted Root Hash does not immediately take effect. Anyone can challenge it. If successful, the new Root is discarded, and the old Root continues to be used.

This challenge mechanism is complex and can be deferred to later stages.
