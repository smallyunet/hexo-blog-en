---
title: Beware of Tantin Technology and Its Products
tags: 
- Work
- Unemployment
date: 2025-05-15 20:37:11
draft_date: 2025-05-15 12:27:34
---


Over the past month, I worked at a company called [Tantin Chain](https://www.tantin.com/) as a backend wallet developer. During my time there, I was mainly responsible for developing a transaction aggregation service. The core function of this service is to receive client requests, query transaction record information for the requested address from data sources, process the data into a business-defined format, and return the result. 

Because it involves multiple chains, multiple data sources, and processing three types of transactions for each address—regular transactions, ERC-20 transactions, and internal transactions—while also handling Redis caching, a dedicated aggregation service was needed. 

This service currently supports four data sources: Ankr, Quick Node, Blockscout, and Etherscan. The full source code is open-sourced here: [smallyunet/tx-aggregator](https://github.com/smallyunet/tx-aggregator)

This company has close ties with Coinstore. Initially, some of the early employees were borrowed from Coinstore. Later, after forming a new team through external recruitment, the Coinstore staff returned to their original positions. Two new teams were formed, mainly responsible for developing three product lines: public chain, cross-chain bridge, and wallet. After a month of development, we had planned to launch and release the first official version today. But for some unknown reason, the company laid off all employees from both teams—on the same day as the scheduled launch.

Back to my own work: this transaction aggregation service is actually quite simple—it only took about one or two weeks to develop. The rest of the time was spent on polishing: optimizing environment configurations, writing unit and integration tests, adding documentation, improving code structure... I must say, the work during this time was quite enjoyable. I haven’t had such a relaxing job in the past two years. The team atmosphere was also great—fun and lively. Having been immersed in serious blockchain tech for so long, switching to regular backend development for a while was a refreshing change of pace.

<br>

### Reminder (2025.05.22)

After some investigation, Tantin Chain's Chinese name is 天体链, and its sister department is Tantin Exchange (TTX), known in Chinese as 天体交易所. TTX was previously called ttsmart, and it issued a token named CTC.

A word of caution: this company originated in Cambodia and recently established an office in Singapore. It has a deeply rooted background in pyramid schemes and has a history of disappearing with funds. Traces of this information can be found online. Everyone should avoid using Tantin’s products to prevent potential losses of personal assets. 

The company recruits under the name Tantin Technology. Industry professionals should also be cautious and avoid joining this company. If any issues arise on the user side, there could be associated liabilities.
