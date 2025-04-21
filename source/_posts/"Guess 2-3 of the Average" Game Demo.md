---
title: '"Guess 2/3 of the Average" Game Demo'
date: 2025-04-22 00:13:05
tags: dApp
---

Over a year ago, I wrote a concept for a [“Guess 2/3 of the Average” dApp Game Design](/2022/12/27/"Designing%20a%20'Guess%202_3%20of%20the%20Average'%20dApp%20Game"/).

I happened to think about it again, and now with the powerful assistance of ChatGPT, implementing the game has become much simpler—both the contract and the frontend can be completed easily. Today, I spent just 2 hours to finish a demo of the web version.

You can access the game through this page: https://guessavg.oiia.network/

A few things to note:

1. This dApp mini-game is currently running on the Oiia Network. Contract address: [`0x6eb07...BA8F1`](https://explorer.oiia.network/account/0x6eb079C9D3005Bd596E8a0E5065fA33C80aBA8F1)

2. Participating in the game essentially means calling a contract function and transferring some money to the contract, for example: transaction [`0x1bfb2...10936`](https://explorer.oiia.network/tx/0x1bfb286c9ed796e16870cc36488bd3c11db6eef43e34c425e58ac76715010936)

2. The contract code is in the repository [guessavg/contract](https://github.com/guessavg/contract). In theory, it can be deployed on any network. Since OIIA is free, I used it.

3. The frontend code is in [guessavg/game](https://github.com/guessavg/game), used in conjunction with the contract code.

4. You can get some OIIA from the faucet: https://faucet.oiia.network/

5. This private key has 10 OIIA, and can be used to try the game. However, from a game mechanics standpoint, the same address cannot participate multiple times. You need to transfer the OIIA to a new address first.

===
fdf0aec857f3ac4fe146e0d00fb3a7a729646a081719df3f4e168a541a21893b
===

6. When the frontend tries to add the Oiia network to Metamask, an error pops up, but it actually succeeds. I didn’t investigate the cause in depth. A month ago, the same code didn’t throw an error.

7. The Oiia Network may disappear at any time.

8. Just for fun...
