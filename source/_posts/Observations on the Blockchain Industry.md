---
title: Observations on the Blockchain Industry
date: 2022-11-15 17:05:50
tags: Blockchain
---

Recently, I've been feeling a bit lost, so I'm just writing randomly. This might be somewhat disorganized. Previously, I've only written about work retrospectives, not about conceptual content. This year has passed quickly with many events, yet no significant changes.

The past year has been a bear market, especially after May, with the blockchain industry entering a severe winter. Unlike before, where various concepts were emerging continuously, it seems like nothing major has happened, and everyone is still wondering what the next narrative in the cryptocurrency industry will be.

### ETH

Ethereum is half of the crypto industry. Many projects are built on Ethereum, many tokens are in Ethereum contracts, and many concepts were proposed by Ethereum, such as DAOs. Such a large ecosystem won't collapse easily. If it does, those projects will go down with it. Bitcoin's value lies in pioneering the entire industry, but for future blockchain applications and ecosystem expansion, it has to be Ethereum. Ethereum can thrive.

Another point is the long development cycle of public chains. Back in the day, Ethereum struggled to raise investments. "In 2017, Vitalik came to China to learn Chinese," and later returned to Western countries to develop for two years before EVM took shape. Now, it has almost become the standard for smart contracts. Various chains are trying to be compatible with EVM because no virtual machine or ecosystem is as complete as EVM. The white paper initially released by Ethereum highlighted smart contracts, while PoS appeared in later versions of the white paper.

From 2017 to 2019, there was a boom in public chains with innovations in consensus mechanisms or application scenarios. Many chains from that period remain, and investors were willing to invest in underlying chains. Nowadays, few new chains are being launched because competition is minimal, and the return cycle is long. This year's Aptos and Sui focus on the Move programming language, with no significant innovation in consensus mechanisms. I don't think Move will become very popular.

The Ethereum merge, touted as the biggest upgrade in history, actually did nothing. For ordinary users, it felt the same whether it happened or not. After the merge, the price of ETH fell from 1700 to 1400. The expected reduction in issuance, deflation, and price increase did not materialize.

However, the fact that Ethereum's price didn't collapse after the merge indicates that its value doesn't come from mining. Some believe that mining guarantees the coin's price because there is a cost involved. Ethereum shows that it can work without mining power and hardware collateral.

### CEX

The price of cryptocurrencies is closely linked to US stocks, with fluctuations depending on the Federal Reserve's interest rate hikes and CPI indicators. However, there are times when they are less correlated. If users lose confidence in US stocks, funds may flow into cryptocurrencies, and vice versa.

The cryptocurrency market is relatively small, seemingly only a few hundred billion dollars compared to the tens of trillions in the US stock market, so its impact is not significant. Some conspiracy theories suggest that cryptocurrencies are a reservoir for US currency to alleviate inflation.

Recently, FTX collapsed, misappropriating users' cryptocurrency assets, causing users to lose confidence in cryptocurrencies, especially CEX. This led to another bear market, with Ethereum's price falling back to around 1300 from 1700. Major exchanges began to disclose their proof of assets.

Trading on exchanges is truly gambling, and I can't handle it. I once had a dream of getting rich quickly, holding $100 and leveraging 125x to short Mask, only to get liquidated in seconds. Later, I used $100 to long Sol, but ended up with only half left. I don't dare to play anymore. The crypto market is full of gamblers, with prices driven by news. Doge coin relies entirely on Musk's endorsement. FTX held more SHIB than ETH.

By the way, Liangxi is of low quality, Ma Yilong is uneducated, and KOLs are all clowns. Sun Yuchen actually despises these people, but uses their traffic and hype.

### Finance

The rise of NFTs was quite sudden, but now the hype has died down. I read an article that said there are two blockchain scenarios: one is migrating from Web 2 to Web 3, which isn't innovation but chain transformation, and the other is native to Web 3. Initially, it was thought that NFTs could serve as on-chain proof of art, but it didn't take off. Instead, blue-chip NFTs succeeded by creating original works on-chain and peripherals off-chain. Currently, NFTs still have more investment attributes, relying on brand and rarity.

Various Fi projects ultimately fall under Finance, such as SocialFi, GameFi, and DeFi. There are currently no reliable scenarios that work. The biggest Web 3.0 and metaverse are still conceptual stages. If it doesn't work abroad, it definitely won't work domestically due to a natural resistance to Fi. In this context, you might feel hopeless.

There are chat rooms and email services based on ENS, but who uses them? There are small games that combine on-chain assets, but neither players nor project teams make money. So what do they do?

When buying VPNs at some major airports, I found they support TRC-20, which was surprising. It turns out Tron is still alive and being used by merchants. This gave me a reason to download the Trx wallet. It can really be used to buy things, without worrying about domestic payment methods. However, it is still in the Finance scenario.

### DSN

There are two leaders in the distributed storage network: Filecoin and Arweave.

Filecoin is typical of off-chain storage or contract-based DSN, using IPFS to store data off-chain, with on-chain proof, settlement, and challenges. Filecoin has strong academic capabilities, first developing IPFS and leading in PoC with many innovations in file proof, surpassing basic PDP and Proof of Retrivailiabily. Their papers on chained file proofs are published in top conferences.

The advantage of off-chain storage is that files can be large without affecting chain efficiency. However, Filecoin is known for the risk of data loss. If data is lost, it's really gone. Also, Filecoin's nodes and SDK are not very user-friendly. It's said that to calculate power based on disk space, they deliberately control the encoding speed of files, leading to a poor user experience, sometimes taking one or two hours to know if a file was successfully stored. Few projects use Filecoin to build.

Arweave is typical of on-chain storage or chain-based DSN. Arweave has less innovation compared to Filecoin. Its block structure is similar to Filecoin, recording a calldata in the block and randomly verifying 10% of the previous block's calldata. Filecoin's PoC consensus is purer, while Arweave relies on PoW mining, first verifying files and then mining with randomX.

Arweave uses an economic model where the rarer the file, the higher the mining probability, achieving permanent storage, unlike Filecoin's temporary storage. Arweave's collaboration with Meta also increased its price significantly.

The advantage of on-chain storage is that files are never lost. However, data bloating is a significant issue, making it less suitable for storing large files, but good for NFT data. Ethereum has long had a solution combining IPFS for off-chain NFT storage.

I understand the distinction between on-chain and off-chain storage or chain-based and contract-based storage simply by checking if generating new blocks relies on the previous block's file data.

Some believe that the primary direction for blockchain is still distributed computing, and the path for distributed storage is not very clear. Filecoin is also working on actor nodes supporting EVM execution environments. Years ago, the TV show "Silicon Valley" already envisioned the future of distributed storage.

Following previous thought patterns, distributed storage aims to either replace Web 2's cloud server market or innovate in Web 3.

### Layer 2

Firstly, layer 2 is still within the Finance scope. Ethereum plans to promote layer 2 to enhance the mainnet's performance and distribute computational pressure.

Currently, layer 2 lacks a true leader. Each layer 2 project is isolated, unable to interact with one another, posing a significant user experience problem. Users need to choose a layer 2 platform, so only small projects are placed on layer 2. Large contracts on layer 1 are unlikely to move to layer 2.

Layer 2's technical infrastructure is incomplete. zk is not yet feasible, op and arb are relatively usable but quite centralized, and interactions with layer 1 are slow. During this year's Arbitrum Odyssey, the sudden increase in users caused gas fees to reach three times the mainnet, which became a famous issue, and the problem was hard to fix.

op's challenge period is relatively long, reportedly one to two weeks, which is unacceptable for ordinary users. The question is, who validates transactions on the mainnet? Users won't do it themselves, and trusting third parties means placing faith in them.

State channels and sidechains have greater limitations compared to rollups, being less secure. op seems to have evolved from plasma.

Ethereum has many blueprints for layer and sharding, but as someone said, "Ethereum is great at everything, just slow at everything." We'll have to wait a long time for Ethereum to meet our expectations.

### Others

DID has been around for years, but no project has truly succeeded. Currently, it is still believed that MetaMask or wallets are the entry point to Web 3. If DID is to take off, it might require a major market shake-up.

Solana's price dropped significantly during the FTX incident. Solana's feature is its PoH consensus, but after several outages, it has lost much of its appeal.

