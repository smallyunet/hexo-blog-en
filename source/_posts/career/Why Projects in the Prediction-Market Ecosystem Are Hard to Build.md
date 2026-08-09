---
title: Why Projects in the Prediction-Market Ecosystem Are Hard to Build
date: 2026-08-09 23:14:31
tags: Work
---

Previously:

- “[My Work Over the Past Six Months](https://b.smallyu.net/2026/05/27/%E6%9C%80%E8%BF%91%E5%A4%A7%E5%8D%8A%E5%B9%B4%E7%9A%84%E5%B7%A5%E4%BD%9C/)” — 2026-05-27
- “[Why the DM KOL GTM Strategy Is Necessarily Correct](/2026/06/29/Why%20the%20DM%20KOL%20GTM%20Strategy%20Is%20Necessarily%20Correct/)” — 2026-06-29
- “[Pitfalls in Building a Polymarket Project](/2026/07/10/Pitfalls%20in%20Building%20a%20Polymarket%20Project/)” — 2026-07-10

I want to summarize and analyze the various difficulties our team has encountered while building this project over the past six months. In short, the project still has not found product-market fit or reached a point where its user base grows organically. Unlike with projects I worked on in the past, I can say without reservation that this project's website is http://virae.ai/

The team has always tried to build this project with the smallest possible headcount and the lowest possible infrastructure costs. The main purpose of keeping costs low is to leave room for experimentation—at least, that is how I understand it.

### Data Services vs. Trading Tools

When we first considered entering the prediction-market space and building a product in its ecosystem, we came across a [news story](https://x.com/pmx_trade/status/2008997887678820862?s=46) about [Polycule](https://www.polycule.trade/), a Telegram bot that was among the better-known and more prominent projects in the Polymarket ecosystem. Its servers were hacked, funds were lost, and there were even rumors that the team had run off with the money. It has since changed its name and taken the entire product in a different direction.

Because of the security risks involved in holding users' private keys, the team imposed one constraint on the project: we should not build a trading tool that takes custody of private keys.

If not a trading tool, then what? We went back and forth among several directions:

1. Alerting products, such as alerts for market activity and position changes. Typical examples include [Cryptocurrency Alerting](http://cryptocurrencyalerting.com) and [PolyAlertHub](https://polyalerthub.com/).
2. A DexScreener-style product that aggregates events from different prediction markets—such as Polymarket, Predict.fun, and Kalshi—and compares similar events. The obvious reference is [DexScreener](https://dexscreener.com/).
3. A Lookonchain-style product that analyzes on-chain data, uncovers interesting activity, and turns it into stories. The obvious reference is [Lookonchain](https://x.com/lookonchain).

The team also came up with another direction of its own: semantic search for prediction-market events. Whether an alert starts with a news story or on-chain data, it runs into the same crucial problem: how do you accurately find the event you are looking for? I might see a news story about Trump but have no idea which prediction-market event relates to it or where I can place a bet.

Here is why none of these directions worked out:

1. For semantic search, we built a website and tried promoting it, but nothing came of it and nobody used it. On the infrastructure side, we needed to index roughly 40 GB of market data in a database, including embeddings used for retrieval.
2. For a DexScreener-style product, prediction-market platforms deliberately avoid offering events with identical subjects and settlement rules, because doing so would enable cross-platform arbitrage. This is different from crypto DEXs, where the same token is expected to circulate on as many venues as possible. Prediction-market events, by contrast, are maintained independently by each platform.
3. For Lookonchain-style data mining, strong storytelling ability is essential. Seeing the data is not the hard part; the hard part is connecting the information and turning it into a story. That requires both data-analysis skills and an editor who can write the content. A signal generated purely by software is not enough.
4. Alerting services appear lightweight, but they require fast, unthrottled APIs or a locally maintained index of on-chain data. Maintaining that index yourself inevitably means running your own node or subgraph.

I mentioned at the beginning that we also kept infrastructure spending low. How low? Virae's entire backend currently runs on 3 vCPUs, 6 GB of memory, and 100 GB of storage on AWS.

I am not saying this configuration is insufficient. I am saying that, under this cost constraint, running our own nodes or subgraphs is naturally out of the question. For comparison, an Ethereum node might require 8 vCPUs, 32 GB of memory, a 1 TB SSD, and more than 10,000 IOPS.

### Information-Aggregation Terminal

Once trading tools were back on the table, this was the first direction we felt optimistic about. Suppose a user wants to bet on an NBA game: they need both the NBA game information and the relevant Polymarket event page open. Could we build a trading terminal that displays sports information and aggregates Polymarket's trading capabilities in one place?

There were clear competitors in this direction: [GMGN](https://gmgn.ai/) and [Axiom](https://axiom.trade/) in crypto, and [Betmoar](https://betmoar.fun/), [Ares](https://ares.pro/), and [stand.trade](https://www.stand.trade/) in prediction markets.

The result was that nobody used it.

Why did nobody use it? We have kept reflecting on that question, but we still do not know the answer.

As an aside, every project in the Polymarket ecosystem appears on Polymarket's Builder leaderboard. The official leaderboard shows only total trading volume and provides no further information. So I built an auxiliary website, https://poly.dark20.xyz/ , using data from Polymarket's public APIs. It lists each project's trading volume, number of traders, and even the details of every individual trade. Because of the constraints imposed by the trading API, every project in this category must appear on the leaderboard. Conversely, a project that does not appear there cannot be part of the Polymarket ecosystem.

### Copy-Trading Bot

Crypto has many Telegram bots that let users snipe tokens or start trading simply by pasting a token's contract address. Would prediction markets benefit from a copy-trading bot that can replicate another person's trades with one click?

There were already many leading competitors, and their products were quite good: [Polycule](https://www.polycule.trade/), mentioned earlier, as well as [PolyCop](https://polycop.ai/), [Kreo](https://kreo.app/), and [PolyBot](https://polybot.trading/).

Again, the result was that nobody used it.

This time, we can identify at least some reasons:

1. Achieving copy trades with zero-block delay requires running your own node, monitoring transactions in the mempool, and then copying them. As mentioned above, running your own node requires a meaningful investment in hardware.
2. The Matthew effect: the leading bots attract more and more users, while smaller bots attract fewer and fewer.
3. Distribution: nobody on our team is good at operating an X account or advertising and promoting a product on other platforms.

### Desktop vs. Mobile

During the World Cup, the project set aside a budget for a promotion that essentially paid people to trade. It attracted quite a few reward farmers from India. We noticed that most users operated on their phones, either by opening the website in a mobile browser or by using a Telegram bot. Very few used a desktop computer.

This differed greatly from our expectations. We had always been building a professional trading terminal, and a trading terminal, in our minds, naturally meant a desktop web interface. As a result, we had made almost no deliberate effort to adapt the product for mobile devices.

Later, we noticed [polymtrade](https://polym.trade/), a project with a large user base and a mobile app available in Apple's App Store. Its interface is extremely simple, and it does not support high-frequency trading, yet it has more than 1,000 monthly active traders and over a million dollars in trading volume. I looked into it: the project has no funding background and only three developers.

We therefore improved the mobile website and adapted it into a Telegram Mini App, hoping users could open the Mini App directly in Telegram and get the complete experience.

The result was still the same: nobody used it.

### High-Frequency vs. Low-Frequency Trading

High-frequency trading is for professional traders. A trader might place an order, watch the candlestick chart, notice a few seconds of momentum, and immediately sell. They might also place many orders at once, set take-profit and stop-loss orders, and trade frequently. This demands a responsive interface, fast order placement, and accurate execution feedback. Accordingly, it is technically difficult to build.

Low-frequency trading is for casual retail traders. They might see a news story today and place a bet on an event, remember another event tomorrow and place another bet, then ignore candlestick charts and liquidity altogether. They may never close the position and simply wait six months for settlement.

The difference between these two trading frequencies also creates a split in product direction. High-frequency traders are inevitably more professional and fewer in number, but they generate large trading volumes. Low-frequency traders may be more numerous, but they generate very little volume.

One research report found that 80% of retail traders on Polymarket had total trading volumes below $100. That figure covers the entire Polymarket ecosystem, where Polymarket's own website accounts for roughly 90% of traders and trading volume, leaving only 10% for third-party projects. I can no longer find the original report, but those are approximately the figures. If you do not believe them, you can ask an AI to research the question again.

If those figures are reliable, projects in the Polymarket ecosystem have a fairly miserable time. Prediction-market volume and revenue may look as though they are reaching new highs every year, but what does any of that have to do with third-party projects?

### Building Yet Another Website

As we added more and more features, we gradually recognized a misconception: building yet another website.

It was the World Cup, and both Polymarket and Kalshi had launched combo trading. We explored whether we could integrate combo trading through their APIs, only to discover that the official APIs did not support it.

This created an awkward situation. A professional trading terminal should have been an enhanced version of Polymarket, yet whenever Polymarket launched a new feature, third-party terminals could not keep up.

That realization also served as a warning: Polymarket is a centralized platform, not a decentralized protocol.

During the CLOB v1 era, perhaps there were still traces of decentralization. Since CLOB v2, however, Polymarket has indisputably become a centralized platform.

So when Polymarket later launched perpetuals trading, we did not even investigate whether we could integrate it. Even if we did, nobody would use it.

Kalshi then launched Kalshi Pro, an official web-based trading terminal. Game over.

In short, building yet another website is definitely not a viable direction.

### Agentic Trading

Later, agentic trading became popular, and many exchanges introduced related features. We also began shifting toward automated and agentic trading. The result of that shift is a set of late-window strategies for BTC and ETH 15-minute and 1-hour markets. Users can configure the parameters and launch a strategy with one click. The most direct competitor we have found in this direction is [EVPLUS](https://pm.evplus.ai/).

One question is whether to build an execution tool or a strategy. An execution tool lets users set their own strategy parameters while we handle only execution. Building a strategy means finding robust strategies or parameter sets ourselves and recommending them to users. Users obviously want a ready-made strategy, but for the project, the risks involved make it safer to provide only the tool.

And I will be honest with you: our project has no edge. Not only do we have no edge, but I can also tell you responsibly that our competitor EVPLUS has none either, because its strategy source code is public.

To promote the current Auto Trade feature, we also developed Paper Trading accounts and a leaderboard. We hoped to run an event where users could participate for free, with a $100 reward for the user with the highest PnL, while also helping users build the habit of using the product.

The paper-trading competition was originally scheduled to begin tomorrow, Monday. Then a “member” from outside the team simply told the operations staff to postpone it—in a commanding tone and without any discussion with the team. As a result, I do not even know whether the event will still happen or, if it does, when it will begin. (Did that sentence confuse you? What exactly is a “member” from outside the team?)

### Single Domain vs. Multi-Domain Aggregation

The World Cup was a major moment for prediction markets, yet it proved that we had failed to make prediction-market tooling work. We then began considering integrations with other domains. For example, when Robinhood Chain became popular recently, we integrated trading for its memecoins.

Later, we saw that an on-chain game called [SLVR](https://slvr.fun/) was generating a great deal of activity and trading volume on Robinhood Chain. I took its public smart-contract source code, developed the frontend and backend myself, and built a similar clone called [VORE](https://c.dark20.xyz/) (it is only a demonstration—please do not play it). VORE was built to prove that we could create an on-chain game like SLVR. At the same time, we were considering whether Virae could learn anything from SLVR's game mechanics.

In short, by this stage we were no longer limiting ourselves to prediction markets. We had begun looking for opportunities in memecoins and other popular on-chain projects as well.

### Conclusion

Why can I draw this conclusion now? As you can see, we tried all kinds of product directions, and none of the results were very encouraging. From my perspective as a developer, I no longer have any inspiration for what else we could build next. It is obvious that no new feature, whatever it is, will bring in more users. This is no longer a development problem.

I have repeatedly made the same point to the team. If 100 users came in, found the product difficult to use or full of bugs, and then left, that would be a product or development problem. We could improve the product, fix the bugs, and increase retention. But in reality, 100 users never came in at all. They never even arrived. So where does the problem lie?
