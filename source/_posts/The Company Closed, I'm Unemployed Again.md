---
title: The Company Closed, I'm Unemployed Again
tags: Work
date: 2025-05-15 20:37:11
draft_date: 2025-05-15 12:27:34
---

In the past month, I worked as a backend developer for a company called [Tantin Chain](https://www.tantin.com/), focusing on wallet backend development. During my time there, I was mainly responsible for developing a transaction record aggregation service. The core function of this aggregation service was to query transaction records for a given address from data sources upon receiving requests from the client, process the data into a format defined by the business, and return the result.

Since it involved multiple chains, and the data came from several sources, each address also needed to handle three types of transactions: regular transactions, ERC-20 transactions, and internal transactions. Plus, there was the issue of writing to the Redis cache. Therefore, a dedicated aggregation service was needed for this purpose. Currently, the service supports four data sources: Ankr, Quick Node, Blockscout, and Etherscan. The complete code is open-sourced here: [smallyunet/tx-aggregator](https://github.com/smallyunet/tx-aggregator)

This company had close ties with Coinstore. Initially, the core team was borrowed from Coinstore, but after forming a new team by hiring externally, the Coinstore employees returned to their original positions. The newly formed team had two groups, mainly responsible for three product lines: public chain, cross-chain bridge, and wallet. After a month of development, the plan was to release the first official version today. However, unexpectedly, the company laid off all employees in the two teams—right on the day of the planned release.

Back to my own work—the transaction aggregation service was actually quite simple, probably requiring just one or two weeks to develop. For the remaining time, I was looking for things to do: optimizing multi-environment configurations, writing unit tests, writing integration tests, writing documentation, optimizing code structure... I must say, this period of work was very pleasant. I hadn't done such an easy job in the past two years. The team atmosphere was also great and cheerful. Having been immersed in the serious blockchain field for too long, it was quite refreshing to occasionally do regular backend development.
