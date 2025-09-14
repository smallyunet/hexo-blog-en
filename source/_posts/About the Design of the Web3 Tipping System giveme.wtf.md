---
title: Design of a Web3 Tipping System
tags: 
- web3
- Plan
date: 2025-04-29 19:26:45
draft_date: 2025-04-29 00:45:36
---

### Product Form

giveme.wtf is a domain I just registered, intended as a small Web3 tipping tool. Similar Web2 platforms include:

- https://buymeacoffee.com/
- https://linktr.ee/

The difference is that the personal page on giveme.wtf will display Web3 wallet receiving addresses and QR codes, much like a personal PayPal payment link. It will support multiple chain address formats including Bitcoin, Ethereum, Dogecoin, etc., and users can choose freely.

giveme.wtf will not act as an intermediary for funds; it will simply display tipping address information. For example, visiting giveme.wtf/{username} will show the receiving address info set up by the user, including Ethereum address text and QR code. That's it.

Of course, on giveme.wtf/{username}, users can also set a simple bio, avatar, domain, social media, etc., like a small personal homepage, making it more shareable.

### Technical Implementation

- Registration

Users register with a MetaMask wallet. After connecting the wallet, they can set a unique, globally-managed username via a smart contract. This requires a transaction to submit the desired username to the contract.

- Profile Info

Once the EVM address is bound to the username, users can set their profile info including avatar, bio, wallet addresses, etc.

After entering the info, the frontend sends it to the backend, which saves it on an IPFS node (pinned for long-term), and returns the CID to the frontend.

The frontend, after receiving the CID, initiates another contract interaction to write the username->CID mapping into the smart contract. This step can be combined with registration or kept separate, since users may want to register without setting a profile.

- Display

The on-chain username->CID is the authoritative data. The frontend fetches the CID from the contract based on the username in giveme.wtf/{username}, then queries the IPFS gateway for the data and renders the page.

Profiles are minimal JSON data, very small in size. To speed up gateway queries, Cloudflare's Web3 gateway CDN can be used.

- Network Selection

The smart contract is deployed on Base.

### Extensions and Optimizations

Later, on-chain data can be used to generate statistics like which receiving addresses are using the tipping system, total amount tipped, and make a leaderboard categorized by username or chain.

Ranking can boost the exposure of the bio under each username. Upvote your idol to keep them at the top.

We can also add 24-hour leaderboards or competitive ranking formats.

The system could also be extended into a social network. Tipping history could form relationship graphs, and even evolve into an IM tool with auto group creation, etc.

#### Username Recovery

A drawback of MetaMask registration is what happens if a wallet is lost—does that mean username control is lost too? A recovery mechanism can be designed, such as allowing each username to set a list of recovery addresses. Any address in this list can reclaim control and update the CID. This is aimed at lost wallets.

What if the wallet gets hacked? Wouldn’t the hacker just change the recovery list? Yes, once a hacker has control of the username, they could reinforce it by changing to their own address. Is there a way to regain control after a hack? Not in Web3.

#### Network Selection

Currently, a chain must be chosen to deploy the smart contract, which ensures data integrity. Choosing a chain is tricky, as users might not hold tokens on-chain.

For example, choosing Base requires users to have ETH and also ETH on Base, which can be a big hurdle.

To solve this, gas fee abstraction can be considered—using a paymaster from ERC-4337 (which has mostly faded), or the older Meta Transaction method. However, we also have to prevent abuse and ensure we can afford to subsidize fees.

#### Data Availability

In the MVP, data is stored on IPFS, but there’s only one server. IPFS is a low-level file routing protocol, and we can wrap it with a simpler layer like Filecoin, but less complex due to small data volume. PoST is complex due to encryption/decryption for large files, but giveme.wtf’s data is small. We can just verify a Merkle Root Hash, meaning a lightweight verification and incentive system on top of IPFS. Nodes that store full data could receive rewards after periodic checks. Details of rewards are TBD.

#### Off-Chain Data Caching

Querying username->CID from the contract every time is slow and uses RPC resources. We should cache this off-chain via a centralized backend that listens to contract events and writes username->CID to Cloudflare Workers KV.

Frontend should first query Cloudflare Workers KV, and fallback to the contract if unavailable.

But what if the centralized service is compromised and changes username->CID? Funds could be misdirected.

This is a data integrity problem that Optimistic Rollup also solves. Here's a possible approach combined with Zetachain’s cross-chain logic.

First, the off-chain caching service builds a Merkle Tree using all username->CID data. The resulting Merkle Root Hash is the integrity proof and is regularly submitted on-chain. Frontend checks this root hash on-chain to verify if the cached CID is tampered.

Also, multiple indexer programs can agree on a TSS key. Only this key can submit Merkle Root Hashes. They will only agree if all their root hashes match—effectively multi-signature.

Finally, introduce a cool-down + challenge period. After submission, the root hash doesn’t take effect immediately. Anyone can challenge it. If successful, the new root is invalidated, and the old one remains. Designing a reliable challenge mechanism is complex, but it’s something to optimize in the future.
