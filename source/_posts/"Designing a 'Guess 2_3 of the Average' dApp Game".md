---
title: "Designing a 'Guess 2/3 of the Average' dApp Game"
date: 2022-12-27 15:48:46
tags: dApp
---

[Guess 2/3 of the Average](https://zh.m.wikipedia.org/zh-cn/%E7%8C%9C%E5%9D%87%E5%80%BC%E7%9A%842/3) is a classic game theory game, now redesigned to run as a dApp on the blockchain. Here are the basic rules of the game:

1. Players pay `any amount` to participate in the game.
2. Calculate the `total amount` paid by all users.
3. Calculate `2/3 of the average` of the `total amount` as the `winning number`.
4. The player who paid an amount closest to the `winning number` wins.

The reward rules are:

5. The winner receives the `total amount` paid by all users in the round.
6. The contract takes 1% of the `total amount` as a service fee each round.

An important question is how many players participate in each round, or when does each round end? The rules for ending the game are as follows:

7. Each player, upon joining the game, knows their position as the `n-th` participant.
8. A random number `x` (10<=x<=100) is generated.
9. Based on `n` and `x`, a boundary for a random number `l, r = n-x, n+x` is generated.
10. A random number `y` is generated within the range `[l, r]`.
11. If `y == n`, the current round ends.

Additional rules:

12. If a user participates multiple times, only the result of their first participation counts.
13. If multiple users pay the same `any amount`, the first participant wins.

These are all the rules of the game.

The rules for ending the game are a bit complex. For example, if a player is the `n=50`th participant and a random number `x=10` is generated, the boundary for the random number becomes `l, r = 45, 55`. A random number is then generated within the range [45, 55], and if it happens to be 50, the current round ends.

Why do it this way? Because data on the blockchain is public. In the original "Guess 2/3 of the Average" game, players' chosen numbers are secret. But it's hard to achieve this on the blockchain. Therefore, the dApp version's rules differ from the original in at least two ways:

- The amount players can pay to participate is unlimited; it can be 0 or infinitely large, whereas the original game has an upper limit.
- The number of players is uncertain, while the original game knows how many players are participating.

These differences are due to the public nature of blockchain data, requiring the introduction of some random mechanisms. Without a random end mechanism, with a fixed number of players, the later participants would have an advantage as they could deduce the most likely winning number based on earlier participants' amounts.

Of course, the final participant doesn't always have the ability to change the outcome, especially if there is a cap on the participation amount. But if the number of players is random, there is no "later participant" advantage since no one knows when the game will end or who will be the last participant. Thus, even if a player could pay an amount to turn the tide, they could be outdone by subsequent participants.

In the original game, participation amounts must be integers. However, in the dApp version, participation amounts can be unrestricted, including decimals. This is because, in the original game, participation amounts are manually given, while in the dApp version, they can be freely input on a computer, and the results are calculated by a smart contract, so precision is not limited.

To explain the game's end rule, its effect is that in most cases, the game will end when the number of participants is within [10, 100]. This range corresponds to the range of `x` in `rule 9`.

This rule can be understood as follows: every player has the ability to end the game, but the ability is random, with a maximum of 1/10 and a minimum of 1/100. If they have a 1/10 ability, it means there is a 1/10 chance to end the game.

If all users have the highest ability of 1/10, the game will likely end after 10 participants. Each person's probability of triggering the "game end" event is independent, and it is 1/10 regardless of the number of participants. The more people participate, the greater the chance of ending the game, because the probability of not ending is (9/10) to the power of the number of participants.

If all users have the lowest ability of 1/100, the game will likely end after 100 participants.

So the range of `x` in `rule 9` roughly limits the range of participants. Of course, this range [10, 100] is not a strict probability calculation.

Based on the defined game end rule, I simulated 100,000 runs and recorded when the game ends based on the number of participants. The code is here: [guessavg/emulate_tool](https://github.com/guessavg/emulate_tool)

![Chart 1](chart1.png)

The horizontal axis is the number of players participating in the game, and the vertical axis is the number of times the game ended at that number. For example, around the position of 1, there are 2500 times, indicating that in 100,000 games, 2500 times the game ended with just one participant.

![Chart 2](chart2.png)

If the number of times is not intuitive enough, you can look at this proportion chart. The proportion of ending at each number does not exceed 2.5%. The earlier the position, the more likely the game ends because each time it starts from 0, the earlier positions have a slightly higher chance of ending the game.

![Chart 3](chart3.png)

This proportional statistics chart better illustrates the result. The game has about a 20% chance of ending with fewer than 10 participants and about a 70% chance of ending with [10, 100] participants. The probability of ending with more than 300 participants is only 0.07%.

Although the result formed by this rule is not a complete normal distribution probability, it basically meets the initial requirement, which is to end the game at a random point in time without being too outrageous, not resulting in too few or too many participants, and still providing a small probability for the number of participants to reach around 500.

I believe this is a reasonable design.
