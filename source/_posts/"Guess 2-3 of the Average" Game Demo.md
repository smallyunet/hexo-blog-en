---
title: "The 'Guess 2/3 of the Average' Game Demo"
date: 2025-04-22 00:13:05
tags: dApp
---

Over a year ago, I wrote an idea for a blog post titled [“Designing a 'Guess 2/3 of the Average' dApp Game”](/2022/12/27/“猜均值的2-3”dApp-游戏设计/).

I happened to think of it again, and now with the powerful help of ChatGPT, implementing this game has become much simpler—both the smart contract and frontend page can be completed with ease. Today I spent just two hours to build a demo webpage for the game.

You can access the game through this page: https://guessavg.oiia.network/

A few notes:

1. This dApp mini-game is currently running on the Oiia Network, with the contract address [`0x6eb07...BA8F1`](https://explorer.oiia.network/account/0x6eb079C9D3005Bd596E8a0E5065fA33C80aBA8F1)
2. Participating in the game is essentially calling a function of the contract while transferring some funds to it, as shown in this transaction [`0x1bfb2...10936`](https://explorer.oiia.network/tx/0x1bfb286c9ed796e16870cc36488bd3c11db6eef43e34c425e58ac76715010936)
3. The contract code is in the repo [guessavg/contract](https://github.com/guessavg/contract); in theory, it can be deployed to any network. I used Oiia simply because it’s free.
4. The frontend code is in the repo [guessavg/game](https://github.com/guessavg/game), to be used in conjunction with the contract code.
5. You can get some OIIA tokens from the faucet: https://faucet.oiia.network/
6. This private key has 10 OIIA tokens and can be used to try the game. However, due to the game’s rules, the same address is not allowed to participate more than once. You’ll need to transfer OIIA to a new address first: `fdf0aec857f3ac4fe146e0d00fb3a7a729646a081719df3f4e168a541a21893b`
7. The frontend page throws an error when adding the Oiia network to Metamask, but it actually succeeds. I haven’t looked into the issue deeply. A month ago, the same code didn’t throw any errors.
8. The Oiia Network may disappear at any time.
9. Just for fun...

### Update (2025.05.06)

Starting today, the Oiia Network has completely shut down because I didn’t want to keep paying server fees for a network that nobody uses. The “Guess 2/3 of the Average” game demo has now been redeployed to the Base network.

1. Game Demo address: [https://guessavg.github.io/game/](https://guessavg.github.io/game/)
2. Contract address on Base: [`0x4BbeE...868D2`](https://basescan.org/address/0x4BbeE9F876ff56832E724DC9a7bD06538C8868D2)
3. You’ll need ETH on the Base network to participate.
4. The game’s difficulty has been reduced—it now ends a round with just two or three players.
