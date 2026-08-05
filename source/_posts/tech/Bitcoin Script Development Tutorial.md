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

Because the command uses `-datadir` to specify the data directory, the wallet files are also stored there. If you inspect `./regtest/wallets`, you will find a `learn-script` directory containing the wallet we just created.

Generate a new wallet address. Mine is `bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw`:

```bash
bitcoin-cli -datadir=./ getnewaddress
```

Next, mine some bitcoin to the wallet address on the local node. The argument `101` means mining 101 blocks. Why 101? Coinbase rewards have a maturity period of 100 blocks before they can be spent. If we mined only 99 blocks, the rewards would exist in theory but would not yet be spendable.

```bash
bitcoin-cli -datadir=./ generatetoaddress 101 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

The command outputs the hash of every block it mines. Once it finishes, check the wallet balance, which should be 50 BTC:

```bash
bitcoin-cli -datadir=./ getbalance
```

Why 50? Before Bitcoin's first halving, each block paid a reward of 50 BTC. Why do 101 mined blocks yield only 50 spendable BTC? Because the latest 100 block rewards are still within the maturity period.

### 3. Send a Transaction

We now have a running local node and a funded wallet, so we can make an ordinary transfer. First generate a new receiving address. Mine is `bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc`:

```bash
bitcoin-cli -datadir=./ getnewaddress
```

Verify that the newly generated address has a balance of 0. The final argument, `0`, tells the command to include unconfirmed transactions:

```bash
bitcoin-cli -datadir=./ getreceivedbyaddress bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc 0
```

Now send 0.01 BTC to the new address:

```bash
bitcoin-cli -datadir=./ sendtoaddress bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc 0.01
```

The command returns a transaction ID. Mine is `81be2e97507d7a274029ec4d5ce9728a54fe6d885aa0f12a13ec6f54eee66c26`. Use it to query the transaction result and details:

```bash
bitcoin-cli -datadir=./ gettransaction 81be2e97507d7a274029ec4d5ce9728a54fe6d885aa0f12a13ec6f54eee66c26
```

The transaction has now been submitted, but the returned status contains `"confirmations": 0`, and the block height remains `lastprocessedblock: 101`. Bitcoin does not produce blocks automatically in regtest mode. The difference is visible when querying the receiving address:

```bash
# The returned balance is 0.01
bitcoin-cli -datadir=./ getreceivedbyaddress bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc 0
# The returned balance is 0
bitcoin-cli -datadir=./ getreceivedbyaddress bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc 1
```

As explained above, a final argument of `0` includes unconfirmed transactions, while `1` requires one confirmation. The transaction we just sent is still unconfirmed. Mine one more block to confirm it:

```bash
bitcoin-cli -datadir=./ generatetoaddress 1 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

If you query the transaction again, both its confirmation count and the wallet balance will now match expectations.

### 4. Inspect the Script

The transfer above is a P2WPKH transaction because Bitcoin Core now uses native SegWit addresses by default.

First, what is P2PKH? The name stands for Pay to Public-Key Hash. A Bitcoin address is derived from a public key, and a P2PKH transaction uses an account address as its recipient. What people often call a legacy Bitcoin address is a P2PKH address, usually beginning with `1`.

The native SegWit format is called P2WPKH—Pay to Witness Public-Key Hash. Its distinguishing feature is that signature data are placed in the witness field rather than in each UTXO's output. To see this, first retrieve the complete encoded transaction using its transaction ID:

```bash
bitcoin-cli -datadir=./ getrawtransaction 81be2e97507d7a274029ec4d5ce9728a54fe6d885aa0f12a13ec6f54eee66c26
```

This returns a long encoded value. Decode it with:

```bash
bitcoin-cli -datadir=./ decoderawtransaction 020000000001018f4e8514038b93d6cc1d4f77b011f4726ba765d338bfdf1e6724d1844bc5d36e0000000000fdffffff0240420f0000000000160014400a517208b473618b98817840328c09a77d6b123eaaf629010000001600147ef4555b42b71e6ebecd687170c92ab64cce35500247304402202417ff3f6959a7d449849ae78fd5272826339cd7096ab02cdd7eccfc7779fb14022077e43ce155259a602b6172261b1d830d30e0de8b06cd6479cac02ea7c6928ff10121020b396a9dfa1655feef066fe03b403d3e4bdee41ef9b26551497c0921acbf6bc196000000
```

The decoded structure looks like this:

```json
{
  // ...
  "vin": [
    {
      // ...
      "txinwitness": [
        "304402202417ff3f6959a7d449849ae78fd5272826339cd7096ab02cdd7eccfc7779fb14022077e43ce155259a602b6172261b1d830d30e0de8b06cd6479cac02ea7c6928ff101",
        "020b396a9dfa1655feef066fe03b403d3e4bdee41ef9b26551497c0921acbf6bc1"
      ]
    }
  ],
  "vout": [
    {
      "value": 0.01000000,
      "scriptPubKey": {
        "asm": "0 400a517208b473618b98817840328c09a77d6b12",
        "desc": "addr(bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc)#nry368tt",
        "hex": "0014400a517208b473618b98817840328c09a77d6b12",
        "address": "bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc",
        "type": "witness_v0_keyhash"
      }
    },
    {
      "value": 49.98998590,
      "scriptPubKey": {
        // ...
      }
    }
  ]
}
```

First look at `txinwitness`, an array with two elements: the signature and the public key. This is the SegWit behavior mentioned above—the signature is stored with the input rather than in an output.

Next look at `scriptPubKey.asm`. The ASM form describes the condition that must be satisfied to spend the amount locked by the script. Even an ordinary transfer is a Bitcoin script whose amount is locked under specific conditions. The script shown here has two parts. The first, `0`, is the `OP_0` opcode. The second, `400a517208b473618b98817840328c09a77d6b12`, is the wallet's key hash; after Bech32 encoding, it becomes the familiar address `bcrt1qgq99zusgk3ekrzucs9uyqv5vpxnh66cjtwl6zc`.

### 5. Debug with btcdeb

What exactly does the `OP_0` opcode do? Opcodes are central to Bitcoin Script, and the `btcdeb` tool lets us debug and observe them. btcdeb does not offer a one-command installer; follow its [official installation instructions](https://github.com/bitcoin-core/btcdeb?tab=readme-ov-file#installation) to download and compile it. Verify the installation:

```bash
btcdeb --version
```

`OP_0` simply pushes an empty value onto the stack. Start a debugging session with:

```bash
btcdeb OP_0
```

The initial output is:

```bash
script  |  stack
--------+--------
0       |
#0000 0
```

The `script` column contains the pending operation `0`, which is `OP_0` with the prefix omitted. `#0000 0` indicates that the current stack value is empty. Enter `step` to execute the opcode:

```bash
step
		<> PUSH stack
```

The empty value has now been pushed onto the stack. For a more illustrative example, run:

```bash
btcdeb '[OP_2 OP_3 OP_ADD]'
```

Enter `step`, pressing Return after each step until execution finishes. Initially, three opcodes—`OP_2`, `OP_3`, and `OP_ADD`—are waiting to run:

```bash
script  |  stack
--------+--------
2       |
3       |
OP_ADD  |
#0000 2
```

The first step executes `OP_2`, pushing 2 onto the stack. Two opcodes remain:

```bash
step
		<> PUSH stack 02

btcdeb> script  |  stack
--------+--------
3       |      02
OP_ADD  |
#0001 3
```

The second step executes `OP_3`, pushing 3 onto the stack:

```bash
		<> PUSH stack 03

btcdeb> script  |  stack
--------+--------
OP_ADD  |      03
        |      02
#0002 OP_ADD
```

The third step executes `OP_ADD`. It pops two numbers, adds them, and pushes the result, 5, back onto the stack:

```bash
		<> POP  stack
		<> POP  stack
		<> PUSH stack 05
btcdeb> script  |  stack
--------+--------
        |      05
```

btcdeb's command-line output is not especially intuitive, so the complete output is included and separated into steps despite its length. Every opcode corresponds to behavior defined by Bitcoin Core: arithmetic operations such as addition and subtraction, more complex operations, and combinations of simple opcodes that implement richer functionality. Bitcoin Script is stack-based, so all of its behavior takes place within the stack; there is no dynamic memory allocation.

### 6. Write Bitcoin Script (1)

After trying addition in btcdeb, we can put script code into an actual Bitcoin transaction and execute it on-chain. This is the script in its raw opcode form. It is deliberately insecure and self-validating: anyone can spend the funds locked in it. Spending merely causes the arithmetic expression to execute on-chain.

```
[OP_2 OP_3 OP_ADD OP_5 OP_EQUAL]
```

First convert each opcode to hexadecimal. This can be done manually or in code. The [Bitcoin Script opcode documentation](https://wiki.bitcoinsv.io/index.php/Opcodes_used_in_Bitcoin_Script) lists the supported opcodes and their hexadecimal encodings. For this script:

| Opcode | Hex |
|:------:|:---:|
| OP_2 | 52 |
| OP_3 | 53 |
| OP_ADD | 93 |
| OP_5 | 55 |
| OP_EQUAL | 87 |

Concatenating them in order gives this hexadecimal script:

```
5253935587
```

Next generate a P2SH address. P2SH stands for Pay to Script Hash: funds are paid to, or locked in, a script hash. It functions as the address of the on-chain script:

```bash
bitcoin-cli -datadir=./ decodescript 5253935587
```

The output contains a `p2sh-segwit` field whose value is `2NAzGPjCcg8DiykVTKLJRYbU2fejCEbdPbX`. Pass that P2SH address to `getdescriptorinfo` to obtain the descriptor checksum required when constructing the transaction:

```bash
bitcoin-cli -datadir=./ getdescriptorinfo "addr(2NAzGPjCcg8DiykVTKLJRYbU2fejCEbdPbX)"
```

The returned descriptor is `addr(2NAzGPjCcg8DiykVTKLJRYbU2fejCEbdPbX)#s260u65e`, which we will use below.

There is one complication: a P2SH script can only be imported into a watch-only wallet, so create a new wallet with private keys disabled:

```bash
bitcoin-cli -datadir=./ createwallet "arith-watch" true true "" true
```

Import the P2SH script into the new wallet. The import succeeded only if the command returns `"success": true`:

```bash
bitcoin-cli -datadir=./ -rpcwallet=arith-watch importdescriptors '[{"desc":"addr(2NAzGPjCcg8DiykVTKLJRYbU2fejCEbdPbX)#s260u65e","timestamp":"now","label":"arith-2+3=5"}]'
```

We now have the P2SH address and have imported the script into a wallet. Send 0.01 BTC from the `learn-script` wallet to the script:

```bash
bitcoin-cli -datadir=./ -rpcwallet=learn-script sendtoaddress 2NAzGPjCcg8DiykVTKLJRYbU2fejCEbdPbX 0.01
```

Mine one block to confirm the transaction:

```bash
bitcoin-cli -datadir=./ generatetoaddress 1 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

The script is now on-chain and has a balance.

### 7. Write Bitcoin Script (2)

Anyone can currently spend the money at this script address. During the spend, the network evaluates `2 + 3` and checks whether the result is `5`. We will now construct a transaction that spends the funds deposited into the script. First prepare a receiving address:

```bash
bitcoin-cli -datadir=./ -rpcwallet=learn-script getnewaddress
```

My new address is `bcrt1q0n2x7030x59j5ql9pp6mw0tps74ag0znrdp45r`. Use it to construct a transaction. The `txid` in `inputs` is the ID of the transaction that funded the P2SH address:

```bash
bitcoin-cli -datadir=./ -named createrawtransaction \
  inputs='[{"txid":"b952acd06a4f7edd7b2d5da0d509d01dfbb8e49fa15123d9cd5d3d23f944cdc2","vout":0}]' \
  outputs='{"bcrt1q0n2x7030x59j5ql9pp6mw0tps74ag0znrdp45r":0.009}'
```

Add automatic change handling to the raw transaction:

```bash
bitcoin-cli -datadir=./ -rpcwallet=learn-script \
  fundrawtransaction 0200000001c2cd44f9233d5dcdd92351a19fe4b8fb1dd009d5a05d2d7bdd7e4f6ad0ac52b90000000000fdffffff01a0bb0d00000000001600147cd46f3e2f350b2a03e50875b73d6187abd43c5300000000
```

The critical step is signing the funded transaction with the wallet. You must sign the transaction returned after change has been added. Without that step, the node treats the change as a fee, which exceeds its default fee limit and causes the next command to fail:

```bash
bitcoin-cli -datadir=./ -rpcwallet=learn-script \
  signrawtransactionwithwallet 0200000001c2cd44f9233d5dcdd92351a19fe4b8fb1dd009d5a05d2d7bdd7e4f6ad0ac52b90000000000fdffffff02a0bb0d00000000001600147cd46f3e2f350b2a03e50875b73d6187abd43c5360a0d92901000000160014a3e136e24d5a8db14f15016b99fb21ea4b0b69da00000000
```

Finally, broadcast the signed transaction:

```bash
bitcoin-cli -datadir=./ sendrawtransaction 02000000000101c2cd44f9233d5dcdd92351a19fe4b8fb1dd009d5a05d2d7bdd7e4f6ad0ac52b90000000017160014c2d5ade24c1d0b9f27f651a71c3fe49d23d0ae13fdffffff02a0bb0d00000000001600147cd46f3e2f350b2a03e50875b73d6187abd43c5360a0d92901000000160014a3e136e24d5a8db14f15016b99fb21ea4b0b69da024730440220406a51d43ade05b240fcf2d14b58c90f31ebc705ab262189949355cac54d0431022051b592c570ef960a35e8509766e903ba836e3bcd1fb3c5cc211f0ff3442283550121021ff283ca8c9ecb45c8e19eacb7e8ae6fcb27d8addd38011d633e396487db44e300000000
```

Mine another block to confirm it:

```bash
bitcoin-cli -datadir=./ generatetoaddress 1 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

Check the transaction state to verify that the output has been spent. An empty result means it is spent. The transaction ID below is the earlier transaction in which the wallet sent 0.01 BTC to the script:

```bash
bitcoin-cli -datadir=./ gettxout b952acd06a4f7edd7b2d5da0d509d01dfbb8e49fa15123d9cd5d3d23f944cdc2 0
```

### 8. Troubleshooting

Environment used:

```text
OS: MacOS
bitcoind: v29.0.0
btcdeb: 5.0.24
```
