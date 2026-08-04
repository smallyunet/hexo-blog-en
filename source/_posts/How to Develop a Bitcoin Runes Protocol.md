---
title: How to Develop a Bitcoin Runes Protocol
tags:
  - BTC
  - Tutorial
date: 2025-07-15 22:00:00
draft_date: 2025-07-11 22:18:37
---

Technically, Bitcoin Runes are simple and easy to understand. Implementing Runes only requires the use of the `OP_RETURN` opcode in Bitcoin Script. Precisely because of this simplicity, Runes are relatively clean from a technical perspective and not as complex as Inscriptions or RGB in terms of off-chain state management. The power of Runes lies in how it sparked an ecosystem around it. Though that hype has cooled down, we’re not focused on market trends here—we’ll look at Runes from a purely technical angle and develop a simplified version of the Runes protocol step-by-step. Once we fully understand Runes, we can dive into more complex projects like Alkanes, a protocol supporting WASM smart contracts on Bitcoin.

This tutorial builds on concepts covered in [Bitcoin Script Development Tutorial](</2025/07/10/Bitcoin Script Development Tutorial/>). If you are unfamiliar with Bitcoin Script, it is recommended to read that first.

### 1. Define the Data Structure

We define Runes operations as JSON-formatted structures. Language choice is flexible—this example uses Rust. To keep the tutorial simple, we skip some practical details (e.g., the `transfer` structure only supports one target address):

```rust
struct IssueRune {
    op: u8,          // Always 0 = Issue
    symbol: String,  // Rune name
    supply: String,  // Total supply
}

struct TransferRune {
    op: u8,          // Always 1 = Transfer
    id: u64,         // rune_id
    vout: u32,       // Output index of the receiving address
    amount: String,  // Amount to transfer
}
```

Why `vout` instead of address? In Runes, to save space, transfers target an output index (`vout`) in the same transaction:

```json
tx {
  vin:   [...]
  vout:  [
    { vout: 0, scriptPubKey: OP_RETURN <json_data> },
    { vout: 1, scriptPubKey: OP_0 <recipient_1> },
    { vout: 2, scriptPubKey: OP_0 <recipient_2> }
  ]
}
```

Add serialization functions to convert the structs into JSON strings:

```rust
impl IssueRune {
    fn toJson(&self) -> String {
        format!(
            "{{\"op\":{},\"symbol\":\"{}\",\"supply\":\"{}\"}}",
            self.op, self.symbol, self.supply
        )
    }
}

impl TransferRune {
    fn toJson(&self) -> String {
        format!(
            "{{\"op\":{},\"id\":{},\"vout\":{},\"amount\":\"{}\"}}",
            self.op, self.id, self.vout, self.amount
        )
    }
}
```

### 2. Issue Runes

Using a local `regtest` node, confirm the wallet is loaded and has balance. Define the Rune issuance:

```rust
fn issue_rune() {
    let issue = IssueRune {
        op: 0,
        symbol: "Doge".to_string(),
        supply: "1000".to_string(),
    };
    println!("Issue Doge JSON: {}", issue.toJson());
}
```

Output:

```bash
Issue Doge JSON: {"op":0,"symbol":"Doge","supply":"1000"}
```

Convert JSON to hex:

```bash
echo -n '{"op":0,"symbol":"Doge","supply":"1000"}' | xxd -p -c 999
```

This produces:

```bash
7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307d
```

This is the data placed after `OP_RETURN` and written on-chain. Note that `OP_RETURN` supports no more than 80 bytes, so the payload cannot be too long.

Next inspect the wallet and choose an unspent output, because every Runes operation must be attached to a UTXO:

```bash
bitcoin-cli -datadir=./ -regtest listunspent
```

For example, I used this UTXO:

```json
{
  "txid": "8bfd524e9fc150dab11289d7e6d07860b2b5d6acb54b278a5dc1d1d7631bc8fa",
  "vout": 0,
  "address": "bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw",
  "amount": 50.00000000
  // ...
}
```

Generate a change address for the remaining bitcoin. This example uses a legacy address, but that is not required; a SegWit address works as well:

```bash
bitcoin-cli -datadir=./ getrawchangeaddress legacy
```

Mine is `n4Ybvvzm9vRQepuMpXBnTWWbYuTgsPSZCV`. Use it to construct the transaction:

```bash
bitcoin-cli -datadir=./ createrawtransaction \
  '[{"txid":"8bfd524e9fc150dab11289d7e6d07860b2b5d6acb54b278a5dc1d1d7631bc8fa","vout":0}]' \
  '[{"data":"7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307d"},{"n4Ybvvzm9vRQepuMpXBnTWWbYuTgsPSZCV":49.99}]'
```

The command assigns 49.99 BTC to the change address. Do not use this shortcut on mainnet: you must calculate the difference between the input, the payment, and the fee precisely, or you could lose a substantial amount. The imprecise value is used here only to avoid extra calculation in a local test.

Sign the generated transaction:

```bash
bitcoin-cli -datadir=./ signrawtransactionwithwallet 0200000001fac81b63d7d1c15d8a274bb5acd6b5b26078d0e6d78912b1da50c19f4e52fd8b0000000000fdffffff020000000000000000206a1e7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307dc0aff629010000001976a914fc9ab9cd801c625c9fe323fe669e6a3e362eed8088ac00000000
```

Broadcast the signed transaction:

```bash
bitcoin-cli -datadir=./ sendrawtransaction 02000000000101fac81b63d7d1c15d8a274bb5acd6b5b26078d0e6d78912b1da50c19f4e52fd8b0000000000fdffffff020000000000000000206a1e7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307dc0aff629010000001976a914fc9ab9cd801c625c9fe323fe669e6a3e362eed8088ac02473044022004a2553cc5348dd4521c093149b0ba5e5603fe4134d06a455e12abeac097ea19022076e72632b2488e1316e54559ed733b37de9ce7fd04119e78a59546a3d2c1faea0121020b396a9dfa1655feef066fe03b403d3e4bdee41ef9b26551497c0921acbf6bc100000000
```

This command returns an important transaction ID. We will later spend the Doge tokens from this transaction, so keep a record of it. Mine is `e2061d0b8b2f98ee47ba6564c1e7409872432354c7617d278fe0e8c4485ff04a`. Mine one block to confirm it:

```bash
bitcoin-cli -datadir=./ generatetoaddress 1 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

If everything went well, the transaction is now on-chain. An off-chain parser will recognize the Rune issuance operation when it sees the transaction. To confirm that the `OP_RETURN` payload was written correctly, decode the transaction we just broadcast:

```bash
bitcoin-cli -datadir=./ decoderawtransaction 02000000000101fac81b63d7d1c15d8a274bb5acd6b5b26078d0e6d78912b1da50c19f4e52fd8b0000000000fdffffff020000000000000000206a1e7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307dc0aff629010000001976a914fc9ab9cd801c625c9fe323fe669e6a3e362eed8088ac02473044022004a2553cc5348dd4521c093149b0ba5e5603fe4134d06a455e12abeac097ea19022076e72632b2488e1316e54559ed733b37de9ce7fd04119e78a59546a3d2c1faea0121020b396a9dfa1655feef066fe03b403d3e4bdee41ef9b26551497c0921acbf6bc100000000
```

The relevant part of the output is:

```json
"vout": [
    {
      "value": 0.00000000,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_RETURN 7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307d"
        // ...
      }
    },
    {
      "value": 49.99000000,
      "n": 1,
      "scriptPubKey": { //... }
    }
  ]
```

Output 0 clearly contains `OP_RETURN` followed by the hexadecimal payload. Decode the payload as one final check:

```bash
echo -n "7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307d" | xxd -r -p
```

The result should be:

```bash
{op:0,symbol:Doge,supply:1000}
```

### 3. Transfer Runes

We have issued 1,000 Doge. Next, we will transfer all 1,000 in another transaction.

First calculate the `rune_id`, the unique ID of the Doge issuance. Symbol strings can be duplicated and occupy more space, so an implementation usually makes an engineering tradeoff. Here we reverse the transaction ID byte-by-byte and take the first eight bytes as a `u64`:

```rust
fn calc_run_idby_txid() {
    let txid = "e2061d0b8b2f98ee47ba6564c1e7409872432354c7617d278fe0e8c4485ff04a".to_string();
    let mut bytes = hex::decode(txid).unwrap();
    bytes.reverse();
    let run_id = u64::from_le_bytes(bytes[0..8].try_into().unwrap());
    println!("Run ID: {}", run_id);
}
```

The function returns `10367542271932362826`. Use that value as the `rune_id` when constructing the transfer payload:

```rust
fn transfer_rune() {
    let transfer = TransferRune {
        op: 1,
        id: 10367542271932362826,
        vout: 1,
        amount: "1000".to_string(),
    };
    println!("Transfer Rune JSON: {}", transfer.toJson());
}
```

Pay attention to `vout`: it is the output index of the address that will receive the Rune in the transaction we are about to construct. It has nothing to do with the issuance transaction's output index. Running the code produces:

```bash
Transfer Doge JSON: {"op":1,"id":10367542271932362826,"vout":1,"amount":"1000"}
```

Repeat the earlier process and convert the JSON to hexadecimal:

```bash
echo -n '{"op":1,"id":10367542271932362826,"vout":1,"amount":"1000"}' | xxd -p -c 999
```

The result is `7b6f703a312c69643a31303336373534323237313933323336323832362c766f75743a312c616d6f756e743a313030307d`.

Create a new address to receive Doge:

```bash
bitcoin-cli -datadir=./ getnewaddress
```

My new address is `bcrt1qc250507tws9z9wkurfcv3jue2nls6npzaqt7ka`.

Use the values above to assemble a Doge transfer transaction:

```bash
bitcoin-cli -datadir=./ createrawtransaction \
'[{"txid":"e2061d0b8b2f98ee47ba6564c1e7409872432354c7617d278fe0e8c4485ff04a","vout":1}]' \
'[{"data":"7b226f70223a312c226964223a31303336373534323237313933323336323832362c22766f7574223a312c22616d6f756e74223a2231303030227d"},{"bcrt1q0n2x7030x59j5ql9pp6mw0tps74ag0znrdp45r":0.01},{"n4Ybvvzm9vRQepuMpXBnTWWbYuTgsPSZCV":49.9798}]'
```

This transaction differs slightly from the earlier one because it has two non-data outputs. The first is the address receiving Doge; its BTC amount is arbitrary because the Doge balance, not the BTC balance, is what matters. The second is the change address used earlier.

The remaining operations are familiar: sign the transaction, broadcast it, and mine a block to confirm it:

```bash
# Sign the transaction
bitcoin-cli -datadir=./ signrawtransactionwithwallet 02000000014af05f48c4e8e08f277d61c7542343729840e7c16465ba47ee982f8b0b1d06e20100000000fdffffff0300000000000000003d6a3b7b226f70223a312c226964223a31303336373534323237313933323336323832362c22766f7574223a312c22616d6f756e74223a2231303030227d40420f00000000001600147cd46f3e2f350b2a03e50875b73d6187abd43c53601fe729010000001976a914fc9ab9cd801c625c9fe323fe669e6a3e362eed8088ac00000000

# Broadcast it
bitcoin-cli -datadir=./ sendrawtransaction 02000000014af05f48c4e8e08f277d61c7542343729840e7c16465ba47ee982f8b0b1d06e2010000006a47304402201437a9e83ae0c6842ebd9d355af9c7be1f6f2eaa070b5d7a6e02e13ca8f2d13102206d05753c428f526b8c6636022991591517cc7d7982badfc633519cb44715957a0121026f441e8156148d0bb4963edaff187873f9800a37bb5f0731256e38d632031283fdffffff0300000000000000003d6a3b7b226f70223a312c226964223a31303336373534323237313933323336323832362c22766f7574223a312c22616d6f756e74223a2231303030227d40420f00000000001600147cd46f3e2f350b2a03e50875b73d6187abd43c53601fe729010000001976a914fc9ab9cd801c625c9fe323fe669e6a3e362eed8088ac00000000
# Transaction ID: 80709a25e5355d51ee6d7fb625c40e9c4c49b049afa3aca18aeaa03bc685c1f0

# Confirm it
bitcoin-cli -datadir=./ generatetoaddress 1 bcrt1q6c8d9vw62rdee72xcqx3d97w8qh8mfg8ky8zjw
```

The Doge transfer is now complete and recorded on-chain.

### 4. Parse Runes Transactions

At first this may look like nothing more than two ordinary Bitcoin transactions with JSON payloads attached. That is exactly how Runes works: every operation fits inside the 80-byte space allowed by `OP_RETURN`. The chain records Runes operations but does not maintain final Runes state. Information such as which tokens exist and what their balances are must be derived according to the protocol and maintained by an off-chain program.

We can retrieve the complete data for the two transactions from their known transaction IDs. If the IDs were not known, an indexer could scan every transaction in every block and parse those matching the protocol. We use the simpler known-ID approach here.

Add these dependencies to `Cargo.toml`:

```toml
[dependencies]
hex              = "0.4"
bitcoin          = "0.31"
bitcoincore-rpc  = "0.18"
serde            = { version = "1.0", features = ["derive"] }
anyhow           = "1.0"
```

Query the node for transaction data over RPC:

```rust
// Required imports
use bitcoin::{Transaction, Txid};
use bitcoincore_rpc::{Auth, Client, RpcApi};
use serde::Deserialize;
use std::path::PathBuf;
use std::str::FromStr;

fn parse_tx()
{
    // Find the RPC authentication cookie in the Bitcoin node's data directory.
    let mut cookie = PathBuf::from("/Users/smallyu/work/github/bitcoin-regtest");
    cookie.push("regtest/.cookie");

    let rpc = Client::new(
        "http://127.0.0.1:18443",
        Auth::CookieFile(cookie),
    ).unwrap();

    // Known transaction IDs
    let issue_txid    = Txid::from_str("e2061d0b8b2f98ee47ba6564c1e7409872432354c7617d278fe0e8c4485ff04a").unwrap();
    let transfer_txid = Txid::from_str("80709a25e5355d51ee6d7fb625c40e9c4c49b049afa3aca18aeaa03bc685c1f0").unwrap();

    // Retrieve the complete transactions.
    let issue_hex    = rpc.get_raw_transaction_hex(&issue_txid, None).unwrap();
    let transfer_hex = rpc.get_raw_transaction_hex(&transfer_txid, None).unwrap();

    println!("Issue Hex: {}", issue_hex);
    println!("Transfer Hex: {}", transfer_hex);

    parse_op_return(issue_hex);
    parse_op_return(transfer_hex);
}
```

This function queries the node for the real on-chain transaction data. We can now parse the Runes operations in the two transactions:

```rust
fn parse_op_return(tx_str: String)
{
    let tx: Transaction = bitcoin::consensus::deserialize(&hex::decode(tx_str).unwrap()).unwrap();
    let script = tx.output[0].script_pubkey.clone();
    // OP_RETURN, DATA
    let mut iter = script.instructions();
    let mut op_return = iter.next();
    let mut data = iter.next();
    // Parse the data.
    match op_return
    {
        Some(Ok(op_return)) =>
            {
                match data
                {
                    Some(Ok(data)) =>
                        {
                            match (data)
                            {
                                bitcoin::blockdata::script::Instruction::PushBytes(bytes) =>
                                    {
                                        let json_str = std::str::from_utf8(bytes.as_ref()).unwrap();
                                        println!("{}", json_str);
                                    }
                                _ => panic!("Expected OP_RETURN with data"),
                            }
                        }
                    _ => panic!("No data found in OP_RETURN"),
                }
            }
        _ => panic!("No OP_RETURN found in script"),
    }
}
```

The parsing code is deeply nested because I prefer not to use syntactic sugar. It could be made more concise, but it would perform the same work. After decoding the transaction layer by layer, the function prints:

```bash
{op:0,symbol:Doge,supply:1000}
{"op":1,"id":10367542271932362826,"vout":1,"amount":"1000"}
```

These are the two expected Runes operations: issuing Doge and transferring Doge.

This is the core of the Runes protocol. What remains is to extend the off-chain program so that it records Runes state, associates operations using `rune_id`, and stores and displays balance changes.
