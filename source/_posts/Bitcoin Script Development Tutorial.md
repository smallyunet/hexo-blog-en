---
title: Bitcoin Script Development Tutorial
tags:
  - Tutorial
  - BTC
date: 2025-07-10 00:42:10
draft_date: 2025-07-09 15:30:09
---

Bitcoin Script is a bit like the elephant in the room—everyone knows it exists, but few can see it clearly or care enough. This tutorial starts from the basics to help you understand how Bitcoin Script works and learn to write your own scripts. Since Bitcoin Script is not Turing-complete, the development process involves a lot of command-line operations and observing output.

### 1. Start a Local Node

Run this command to install the `bitcoind` binary, then test the installation with `bitcoind --help`:

```bash
brew install bitcoin
```

Create a directory for testing, such as `bitcoin-regtest`:

```bash
mkdir ./bitcoin-regtest
cd ./bitcoin-regtest
```

In this directory, create a `bitcoin.conf` file and add the following config:

```conf
regtest=1
txindex=1
fallbackfee=0.0001
```

This configuration sets up a local development node. The `regtest=1` setting enables the local regression test network, starting from block height 0 and avoiding syncing with the public blockchain. `txindex=1` enables transaction indexing for easier lookup, and `fallbackfee` sets the default transaction fee.

While in the directory with this config file, start the node:

```bash
bitcoind -datadir=./ -daemon
```

If successful, you'll see "Bitcoin Core starting". Check if the node is running:

```bash
bitcoin-cli -datadir=./ getblockchaininfo
```

To verify further, check the log:

```bash
cat ./regtest/debug.log
```

To stop the node:

```bash
bitcoin-cli -datadir=./ stop
```

Note: `bitcoind` starts the server, while `bitcoin-cli` is the client.

If you restart the node and find the wallet isn't working, use this to load it:

```bash
bitcoin-cli -datadir=./ loadwallet learn-script 
```

### 2. Create a Wallet

Create a Bitcoin wallet:

```bash
bitcoin-cli -datadir=./ createwallet "learn-script"
```

This will create a folder under `./regtest/wallets` named `learn-script`.

Generate a new wallet address:

```bash
bitcoin-cli -datadir=./ getnewaddress
```

My sample address: `bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw`

Mine 101 blocks to this address:

```bash
bitcoin-cli -datadir=./ generatetoaddress 101 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

Check the wallet balance (should be 50):

```bash
bitcoin-cli -datadir=./ getbalance
```

### 3. Send a Transaction

Generate a new receiving address:

```bash
bitcoin-cli -datadir=./ getnewaddress
```

Example: `bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc`

Check the balance (should be 0):

```bash
bitcoin-cli -datadir=./ getreceivedbyaddress bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc 0
```

Send 0.01 BTC to it:

```bash
bitcoin-cli -datadir=./ sendtoaddress bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc 0.01
```

Get transaction details:

```bash
bitcoin-cli -datadir=./ gettransaction <txid>
```

Mine one block to confirm the transaction:

```bash
bitcoin-cli -datadir=./ generatetoaddress 1 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

### 4. Inspect the Script

Use `getrawtransaction` and `decoderawtransaction` to view the transaction script:

```bash
bitcoin-cli -datadir=./ getrawtransaction <txid>
bitcoin-cli -datadir=./ decoderawtransaction <hex>
```

You'll see `txinwitness` (signature and pubkey) and `scriptPubKey` (locking script). `OP_0` in the script means an empty push (used in SegWit).

### 5. Debug with btcdeb

Use `btcdeb` to debug opcodes. Example:

```bash
btcdeb OP_0
```

And for a simple calculation:

```bash
btcdeb '[OP_2 OP_3 OP_ADD]'
```

Use `step` to execute each operation and watch the stack.

### 6. Write Bitcoin Script (1)

Write this simple script (insecure, for demo only):

```
[OP_2 OP_3 OP_ADD OP_5 OP_EQUAL]
```

Convert to hex: `5253935587`

Generate P2SH address:

```bash
bitcoin-cli -datadir=./ decodescript 5253935587
```

Get descriptor:

```bash
bitcoin-cli -datadir=./ getdescriptorinfo "addr(<p2sh-address>)"
```

Create a watch-only wallet:

```bash
bitcoin-cli -datadir=./ createwallet "arith-watch" true true "" true
```

Import the script:

```bash
bitcoin-cli -datadir=./ -rpcwallet=arith-watch importdescriptors '[{"desc":"addr(<p2sh-address>)#<checksum>","timestamp":"now","label":"arith-2+3=5"}]'
```

Send BTC to the script address:

```bash
bitcoin-cli -datadir=./ -rpcwallet=learn-script sendtoaddress <p2sh-address> 0.01
```

Mine a block to confirm:

```bash
bitcoin-cli -datadir=./ generatetoaddress 1 <your-address>
```

### 7. Write Bitcoin Script (2)

Create a new address:

```bash
bitcoin-cli -datadir=./ -rpcwallet=learn-script getnewaddress
```

Use `createrawtransaction`, `fundrawtransaction`, `signrawtransactionwithwallet`, and `sendrawtransaction` to construct and broadcast a transaction spending from the script.

Mine another block to confirm:

```bash
bitcoin-cli -datadir=./ generatetoaddress 1 <your-address>
```

Check if the UTXO is spent:

```bash
bitcoin-cli -datadir=./ gettxout <txid> 0
```

### 8. Troubleshooting

Environment used:

```text
OS: MacOS
bitcoind: v29.0.0
btcdeb: 5.0.24
```
