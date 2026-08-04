---
title: On Ethereum Private Key Collision
tags: Blockchain
date: 2023-01-18 15:27:25
draft_date: 2023-01-18 09:51:38
---

Would you try your luck if you had a 1/100000000 chance to get rich?

### Ethereum Private Key

Ethereum accounts are entirely controlled by private keys, and an account's address can be derived from its private key. A private key is a 64-character hexadecimal string, such as:

```
0xd110227375ab838e8743192d278c105e30f253c966987c50b754412c9b986fe3
```

You can import the corresponding account into any Ethereum wallet using this private key. The address for this private key is:

```
0x00000006A3D4DA3A559829B1730603CAeE97cC3D
```

You can also check account-related [transaction records](https://etherscan.io/address/0x00000006A3D4DA3A559829B1730603CAeE97cC3D) on Ethereum explorers based on this address.

### Private Key Collision

Since a private key is just a string, is it possible to randomly generate a string such that the corresponding account address happens to have money in it? With the private key, you can control all assets in the account. There are many accounts on Ethereum with substantial balances. What if you happened to generate a private key corresponding to one of these accounts? You'd instantly become rich.

Of course, the randomly generated string must be a 64-character hexadecimal string. To avoid any collision prevention mechanisms in the Ethereum SDK, we can generate it straightforwardly, like this:

```javascript
let s = "0123456789abcdef";
let hex = "0x";
for (let i = 0; i < 64; i++) {
  hex += s[Math.floor(Math.random() * 16)];
}
```

This is straightforward: it randomly assembles a 64-character string. It is the sort of thing that appears every day in easy LeetCode problems. In practice, Ethereum does not have any collision-prevention mechanism.

### Collision Probability

Let's calculate the probability of randomly generating a private key that collides with an existing account. If there's an address, how likely is it to randomly generate the private key for that address?

In hexadecimal, each character has a 1/16 chance of matching a target private key character. For a 64-character string, the probability is (1/16)<sup>64</sup>.

```
p = (1/16)^64
  = 1 / 16^64
  = 1 / 115792089237316195423570985008687907853269984665640564039457584007913129639936
```

Rounding off and considering just the order of magnitude:

```
p = 1 / 115792089237316195423570985008687907853269984665640564039457584007913129639936
  = 1 / 100000000000000000000000000000000000000000000000000000000000000000000000000000
  = 1 / 10^77
```

This is an extremely low probability. The lowest winning rate for the Chinese lottery, "Daletou," is one in ten million, or 1/10^8. The probability of an Ethereum private key collision being a specific account is equivalent to winning the lottery ten times in a row. It's almost impossible.

However, lottery winning has a speed limit, such as requiring a day to announce the result. Cryptocurrency private keys don't have such a limit. Can increasing the speed of random private key generation improve the collision probability?

At the time this article was written, the [hash rate](https://www.blockchain.com/explorer/charts/hash-rate) of the Bitcoin network was 270M TH/s. Bitcoin mining involves continuous hashing until a value with a required number of leading zeros is found, so the network hash rate can be used here as a rough upper-bound analogy for private-key generation speed. After [unit conversion](https://en.bitcoinwiki.org/wiki/Hashrate):

```
r = 270M TH/s
  = 270000000 TH/s
  = 270000000000 GH/s
  = 270000000000000 MH/s
  = 270000000000000000 kH/s
  = 270000000000000000000 /s
  = 100000000000000000000 /s
  = 10^20
```

This result is rounded to the nearest order of magnitude. If you had the global Bitcoin mining power to perform Ethereum private key collisions, the per-second probability would be:

```
ps = p * r
   = 1 / 10^57
   = 1 / 1000000000000000000000000000000000000000000000000000000000
```

This is still a remote probability. What if the collision attempt continues for one year?

```
py = ps * 60 * 60 * 24 * 365
   = ps * 31536000
   = ps * 10000000
   = ps * 10^7
   = 1 / 10^50
   = 1 / 100000000000000000000000000000000000000000000000000
```

What about 100 million years of continuous operation?

```
pb = py * 100000000
   = py * 10^8
   = 1 / 10^42
   = 1 / 1000000000000000000000000000000000000000000
```

As shown, time increases the probability only slightly. Computing power may increase exponentially, like the speed improvements of Apple's M series chips over Intel chips. However, this is still insignificant compared to the order of magnitude with 70 zeros.

Even with the computing power of 100 million Earths' worth of Bitcoin miners running for 100 million years, you'd only reduce a few zeros from the likelihood of collision success.

### Giving It a Try

Despite the improbability, I still want to try.

I wrote a script, [eth-collision/eth-collision-random](https://github.com/eth-collision/eth-collision-random), which randomly generates private keys and checks the account balance on [etherscan.io](https://etherscan.io) for addresses derived from these keys. If the balance is greater than zero, it logs the private key. Due to Etherscan's API call rate limit, the script checks 20 addresses per second, attempting 1.72 million addresses per day.

This speed isn't fast enough. Is there a better way? Most third-party services have API rate limits. To query without restrictions, you'd need to run your own Ethereum node. The minimum hardware requirements for an Ethereum node are a 4-core CPU, 16GB of RAM, and a 1TB hard drive. Such a server costs at least $300 per month on [Vultr](https://www.vultr.com/). It's expensive. After setting up the node, you'd need to write a program to collect addresses with balances from transaction data in each block, which takes a long time.

I noticed that [this page](https://etherscan.io/accounts/1?ps=100) provides a list of Ethereum addresses with the highest balances, 100 per page, for a total of 100 pages. I wrote a crawler, [eth-collision/eth-address-top-list](https://github.com/eth-collision/eth-address-top-list.git), to fetch data from this page, giving me a list of the top 10,000 Ethereum addresses by balance.

With these 10,000 addresses, the program can run faster without API rate limits or needing a full node. I used Golang to write a program, [eth-collision/eth-collision-match-address](https://github.com/eth-collision/eth-collision-match-address.git), starting 100 goroutines to speed up private key generation. The program loads the 10,000 addresses into a map and checks if the randomly generated account is in the target list. A JavaScript for-loop can max out the CPU, but intuitively, Golang feels faster.

By the way, remember to add a mutex to the map in Golang because maps aren't concurrency-safe. Adding locks reduces speed. To solve the lock issue, since there are only 10,000 addresses, not much memory is needed, so instantiate multiple maps, one for each goroutine. This ensures the efficiency of private key generation and verification.

Are 10,000 addresses too few? How many Ethereum addresses were there? According to Etherscan [statistics](https://etherscan.io/chart/address), there were about 230 million at the time of writing—roughly 200 million for an order-of-magnitude estimate.

I found a repository, [eth-collision/Wallet-private-key-collision-brute-force-tool](https://github.com/eth-collision/Wallet-private-key-collision-brute-force-tool), providing a OneDrive download link for a file containing 180 million account addresses with transaction records on the chain. The compressed file is 4.4GB, and after decompression, it's about 16GB in pkl format, which I converted to a txt file using Python.

With this many account addresses, the question becomes how to use the data.

First, load all data into a map. This amount of data in memory would require at least 16GB, necessitating a 24GB server. The cheapest suitable server on Vultr costs nearly $150 per month. Furthermore, Golang's query efficiency with such a large data set and the need for locking are concerns.

Are there better solutions? MySQL cannot efficiently support queries over hundreds of millions of rows. In a previous project, even the simplest single query over more than 100 million records took over ten seconds. Here we need thousands of lookups per second. What about Redis? Its lookup performance should be sufficient, but it still loads all data into memory. According to the Redis [FAQ](https://redis.io/docs/getting-started/faq/), one million keys require 85 MB of memory. Scaling that up:

```
1M   -> 85M
180M -> 180 * 85M
     = 15300M
     = 15G
```

A 16 GB Redis instance on Vultr is very expensive—far more than a server—at about $480 per month. Heavier databases such as Elasticsearch or a more specialized system such as HBase would require setting up the database, importing the data, and processing it, making the overall workflow rather cumbersome. The server cost would not necessarily be lower either.

Later I came across Bloom filters, which happen to fit this use case. I tried one configured for 200 million entries with a false-positive rate of one in a billion. After loading 180 million addresses, the exported [binary file](https://github.com/eth-collision/eth-address-all) occupied only 1 GB on disk and required only 1 GB of memory when loaded.

That is a fairly good solution. I ran the collision program on servers: a two-core, 4 GB instance costing $30 per month could test 60 million addresses per hour, while an eight-core, 16 GB instance costing RMB 600 per month could test 160 million per hour.

Of course, no server is good enough. Even all the computing power on Earth would not be enough.

### Vanity Addresses

Ethereum also has vanity addresses. For example, `0x00000006A3D4DA3A559829B1730603CAeE97cC3D` contains six leading zeros. See [this article](https://www.theblockbeats.info/news/33808?from=telegram) for more about vanity addresses.

I wrote [eth-collision/eth-collision-find-address](https://github.com/eth-collision/eth-collision-find-address) to search for them. Finding an address with six leading zeros is generally easy and takes only a few minutes on a MacBook Air M1. Seven leading zeros may take half an hour. Eight can take a day or two.

An address with ten leading zeros is extremely rare. Vanity addresses are of little practical use, of course: once you know the private key, the account is yours, and nobody else would want it from you.

### Manual Online Collision

This webpage lets you experiment with private-key collisions manually. It can derive a public key from a private key, generate random key pairs, and link directly to Etherscan to check the corresponding account balance.

Website: [http://eag.smallyu.net](http://eag.smallyu.net)

Source: [smallyunet/eth-address-generator](https://github.com/smallyunet/eth-address-generator)

The page is implemented entirely in the browser, works offline, and introduces no security risk.
