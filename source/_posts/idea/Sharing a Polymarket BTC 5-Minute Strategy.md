---
title: Sharing a Polymarket BTC 5-Minute Strategy
tags: Strategy
date: 2026-07-05 01:32:20
draft_date: 2026-07-02 12:13:20
---


### Disclaimer

1. This is not risk-free arbitrage. It is an ordinary strategy, is not mathematically positive-expectation, and carries clear strategic risks.
2. It may make or lose money as market conditions change. Past returns do not represent future returns.
3. As for performance data, I have been adjusting the strategy and its parameters frequently over the past few days, so I am not sure what the actual return will be after it runs continuously for several more days. What I can confirm is that I have been running it for the past half month, placing orders in more than 300 markets. The total profit is small, but it has not lost money. At the very least, this shows that the strategy is not simply a money-losing machine.

### Code Repository

- https://github.com/smallyunet/poly-btc5m

The repository is updated frequently, so use it with caution. This is code I actually run myself. Although the strategy is simple, implementing it took a substantial amount of development work. If you want to try it, you can use the repository directly. Also, if you do not believe my reasoning about the strategy below, be sure to test it yourself 🐶

One related observation from development: many people trading prediction markets on X are using vibe coding to write and run their own strategy bots. In my experience, it is essentially impossible for non-programmers to build a reliable, working strategy this way, except for highly capable people with strong logical reasoning skills.

Some strategies, including the one described below, look extraordinarily simple to understand. But implementing them in code raises many concrete problems: preventing duplicate orders, verifying that exit logic executes correctly, and determining whether a sudden flood of live orders is expected strategy behavior or a software error. All of these require repeated debugging. The problem is not that non-programmers cannot think these issues through; it is that the debugging process is exhausting. You have to keep adjusting parameters, observing the results, revising the strategy when results are poor, and observing again. This is exactly what software development has always involved. Even though you no longer need to write the code yourself, you still have to review the results.

### Strategy Description

#### Basic Strategy

For the next BTC 5-minute market that has not yet opened, place limit orders in advance: buy Up at 45c and Down at 45c. If both orders fill, then because 0.45 + 0.45 < 1, settlement guarantees a 10% return on the principal. Once both orders have filled, the profit is locked in and no further action is required.

The downside risk is equally clear. If the market moves in one direction after opening and never reverses by more than 55% during the entire five-minute period, the strategy loses 50% of its principal. In other words, one losing market requires five winning markets just to recover the loss.

To reduce losses, the strategy includes two stop-loss mechanisms:

1. If only one leg fills after the market opens, that leg reaches a 5% profit, and the other leg remains unfilled, immediately take profit on the filled leg and cancel the other limit order.
2. If only one leg has filled by the final minute and the price of the other leg is below 75c, buy twice the quantity of the other leg at the current price to cap the loss.

#### The Subjective Part of the Strategy

From a purely logical and quantitative perspective, this strategy clearly does not hold up. Its risks are both large and obvious, there is no programmatic way to avoid them, and the strategy lacks sufficient backtesting data. The strategy is therefore subjective to a considerable degree. What I want to explain is why I subjectively believe it can work.

There are two kinds of price action in a BTC 5-minute market. One is oscillating price action, in which opening both sides with this strategy can be profitable. The other is a one-way move, where the settlement direction is effectively decided as soon as the market opens and the price never returns.

Now consider the reverse: if one-way markets are more common than oscillating ones, would that mean you could simply buy the side priced above 55c a few seconds after the market opens and hold it through settlement? Each trade would return more than 80%. Combined with the premise that one-way markets are more common, simply following the move would become a consistently profitable strategy. It would be a kind of “super endgame” strategy: ordinary endgame strategies enter close to settlement at prices such as 99c, whereas this probability would tell you that 55c is already enough to enter.

In reality, however, anyone who trades these markets knows that reversals in the final five seconds of BTC 5-minute markets happen frequently. Buying one side outright can lose badly; even buying near settlement can be disastrous. I also find it hard to believe that making money could be as simple as continuously buying one side. It is far too counterintuitive.

So, reversing the reasoning again: if one-way markets cannot be more common than oscillating markets, oscillating markets should be the majority.

#### The Objective Part I Ignored

If the judgment above is purely subjective, could we instead use BTC price momentum to predict whether BTC will fluctuate sharply over the next five minutes?

I have already tested this. I used Binance's WebSocket API to obtain real-time prices and checked whether the price crossed above and below a midpoint during the 120 seconds before the next round began. I also added a complex scoring system: the price had to cross in both directions at least twice, fluctuate by more than 12 bps, and produce the signal three consecutive times. The system also incorporated conditions based on the current Polymarket order book. I even considered using Binance funding rates and the top holders in the next Polymarket market as additional signals.

I also experimented with dynamically adjusting parameters. For example, entry price and position size could vary with the score: a higher score might use 42c and 1.2\*amount, while a lower score might use 46c and 0.8\*amount.

The result was that very few markets passed all the filters, so very few orders were placed, and there was no clear improvement. If I placed orders blindly, perhaps nine out of ten markets would end in a paired state (profitable) and one in a single state (unprofitable). With the scoring system and a requirement that volatility exceed the threshold, the strategy might place orders in only ten markets per day, yet one of those ten could still end in a single state. A high score did not reduce the risk of a single state; it merely reduced the number of markets traded substantially.

Of course, you may think my quantitative thinking is not professional enough and that I cannot build a sufficiently sophisticated and effective model to predict price movements in the next market. I admit that...

#### Return Ceiling

Even if this strategy really works and makes money, its returns have an upper limit. Judging from the liquidity depth in recent Polymarket markets, an order of around $100 per leg is still safe. Above $200, there is a risk that there will not be enough liquidity for the order to fill.

This is somewhat counterintuitive. You might assume that because Polymarket has such enormous trading volume, measured in tens of billions of dollars, many people must trade BTC 5-minute markets. But the official site directly shows each market's top holders and top PnL. The largest holders own only a few thousand shares, while the highest profits are in the hundreds or thousands of dollars.

There are 288 five-minute periods in a day. If every market were profitable and the principal were $200, a 10% return would mean $20 per market, or $5,760 per day. That is the theoretical ceiling. Of course, winning every market is impossible. If the strategy wins nine and loses one, the return is cut in half. One additional loss wipes out the profit from five winning markets, reducing the return to one-tenth, and then...

#### Extending the Strategy

If this approach works for BTC 5-minute markets, could the same strategy be used for BTC 15-minute, 1-hour, 4-hour, and 1-day markets, as well as ETH 15-minute and SOL 1-hour markets? Clearly, it could.

### FAQ

- Q: If this strategy can really make money, why share it?
- A: Because genuinely profitable strategies have always been open secrets. As I have repeatedly said, the only strategy that can support large, long-term allocations is dollar-cost averaging into BTC.

<br>

- Q: Why not run it for a few more days and wait until you have attractive performance data before sharing it?
- A: I do not have the patience. Even if a strategy makes money for the past few days, months, or years, it may lose all of it again over the next few days, months, or years. My view remains the same: as long as you stay at the table, there are no winners.

### Related Reading

- [Do Not Copy Trade](https://crazy.smallyu.net/2026/06/30/%E4%B8%8D%E8%A6%81Copy-Trading/) — 2026-06-30
- [Do Not Become an Airdrop Farmer in Crypto](https://crazy.smallyu.net/2026/05/10/%E5%9C%A8%E5%B8%81%E5%9C%88%E4%B8%8D%E8%A6%81%E5%81%9A%E6%92%B8%E6%AF%9B%E5%85%9A/) — 2026-05-10
- [My Cryptocurrency Trading Bot](/2022/12/03/My%20Cryptocurrency%20Trading%20Bot/) — 2022-12-03

<br>

### Update (2026.07.17)

This is a lesson that cost me $200. I should have understood this long ago—or perhaps I already did: strategies cannot make money. But because of my work, and perhaps some other influences, I suddenly decided to experiment with a Polymarket strategy.

Actually testing this strategy was far more complicated than the article suggests and consumed a great deal of time and energy. After going through the experiment, however, I found even stronger support for the conclusion that strategies cannot make money. They may make money periodically through luck, but their long-term expected value must be negative.

I did not have the time or patience to write a retrospective, so I asked ChatGPT to summarize the entire story:

[How We Took a BTC 5-Minute Strategy That Looked Profitable and Ran It Until We Finally Shut It Down](https://github.com/smallyunet/poly-btc5m/wiki/%E6%88%91%E4%BB%AC%E6%98%AF%E6%80%8E%E4%B9%88%E6%8A%8A%E4%B8%80%E4%B8%AA%E7%9C%8B%E8%B5%B7%E6%9D%A5%E8%83%BD%E8%B5%9A%E9%92%B1%E7%9A%84-BTC-5-%E5%88%86%E9%92%9F%E7%AD%96%E7%95%A5%EF%BC%8C%E8%B7%91%E5%88%B0%E6%9C%80%E7%BB%88%E5%81%9C%E6%9C%8D%E7%9A%84)

Finally, I want to explain and emphasize what I mean here by “lesson”: do not rely on wishful thinking. Once you know that “strategies cannot make money,” you may still occasionally want to try again. Maybe this time will be different; maybe some adjustment will make it work. Then you discover that, in the end, you not only failed to make money but also lost money. That is the lesson.

<br>

### Update (2026.08.09)

The strategy is still being maintained, but with much greater caution. Having learned from the earlier rush for results, I now run it only in simulation and analyze the resulting regression-test data. I will update the article again when there are new findings or results.
