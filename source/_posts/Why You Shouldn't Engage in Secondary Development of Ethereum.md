---
title: Why You Shouldn't Engage in Secondary Development of Ethereum
tags: Programmer
date: 2024-09-12 15:29:05
draft_date: 2024-09-12 14:33:41
---

The reasoning is simple. Ethereum has a core development team. The Ethereum Foundation, at its center, provides funding to various Ethereum client development teams, who in turn develop for Ethereum. Vitalik came to China to raise funds, and after securing the money, formed a development team in another country. After a year of intensive development, Ethereum was born. Back in the day, the Ethereum Foundation participated in Ethereum’s ICO at less than $1, and now the price of ETH has increased thousands of times. That's how the Ethereum Foundation got its money.

The Ethereum ecosystem is mainly composed of three parts: Layer 1, Layer 2, and ecosystem projects. (Staking-related projects don't require your development. Does Restaking have anything to do with tech?)

For Layer 1, there are several major client teams: the execution layer includes Geth, Nethermind, Besu, Erigon, and Reth, while the consensus layer includes Prysm, Lighthouse, Teku, Nimbus, and Lodestar. Regardless of the relationship between these teams and Ethereum, if you or your company claims to want to conduct secondary development based on Ethereum clients, then what exactly do you want to develop?

If it's an improvement beneficial to the Ethereum network itself, such as improving performance or optimizing data structures, you can directly submit an Issue or PR to Ethereum, or even establish a cooperative relationship, allowing the official Ethereum client version to support your optimizations. Moreover, the founding teams of these clients are still actively developing. Do you think you have a reason to outperform the "insiders" in terms of either client functionality or performance, whether as an individual or as a company?

For example, take parallel EVM, which aims to speed up EVM transaction execution. This is a challenge even the Geth team has been unable to solve. Can any small team just casually pull it off?

If it's a modification that benefits your own chain but not Ethereum, and you expect the Ethereum development team not to accept your proposals and improvements, then the issue becomes even stranger. What makes your chain's requirements so unique that these "self-owned" developments are necessary? Ethereum is already quite complete. In such a scenario, it seems you need to reevaluate the entire endeavor starting from the initial requirements.

On the Layer 2 side, the major teams include Polygon, Optimism, Zksync, and others. Why have Ethereum Layer 2 solutions become so popular and successful? Because Layer 2 is the direction endorsed by Vitalik. Why has the ENS project gained widespread adoption? Because ENS is a project approved by Vitalik. Why was there a buzz around AA wallets for a while? Because it was brought up by Vitalik’s soulbound tokens concept. You can see from Vitalik’s blog that all major current ecosystem projects have direct ties to Vitalik himself. The founders of these projects can all talk to Vitalik.

Vitalik is the big boss behind the entire Ethereum ecosystem. So, if you say you want to create a project within the Ethereum ecosystem, is technical ability your primary concern? What determines if a project will succeed? First, you look at whether Ethereum's roadmap aligns with this direction. Then, see which project in this direction is leading within the Ethereum ecosystem. And then? You’ll realize that it has nothing to do with you. The Ethereum Foundation didn’t give you money, so why are you getting involved?

From a developer’s perspective, if you want to participate in Ethereum ecosystem development, what do you need the Ethereum core team for? If you want to improve Op Stack's Fault Proofs, what do you need Optimism's core team for? As an outsider, you're spending time and resources just to carry water for others?