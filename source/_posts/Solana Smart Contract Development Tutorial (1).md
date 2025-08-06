---
title: Solana Smart Contract Development Tutorial (1)
tags:
  - Smart Contract
  - Tutorial
date: 2025-06-24 21:51:06
draft_date: 2025-06-24 18:36:25
---

> This is a beginner-friendly tutorial series that teaches Solana smart contract development from the very basics.
> 
> - [Part 1](/2025/06/24/Solana%20Smart%20Contract%20Development%20Tutorial%20(1)/): Basic environment setup, deploying HelloWorld contract, on-chain contract interaction
> - [Part 2](/2025/06/26/Solana%20Smart%20Contract%20Development%20Tutorial%20(2)/): Creating a minimal USDT contract model, custom data structures and methods
> - [Part 3](/2025/06/28/Solana%20Smart%20Contract%20Development%20Tutorial%20(3)/): Using the official SPL library to reuse contract functionality and issue a standard token

We’ll start from the basics to learn Solana smart contract development. You only need general programming knowledge, like understanding object-oriented concepts—no prior experience with other networks' smart contracts or the Rust language is required.

### 1. Install the Environment

Visit Solana's official installation guide: <https://solana.com/docs/intro/installation>

The documentation provides a one-line install command for all dependencies, as well as a step-by-step guide. Pay attention: Solana CLI requires editing your environment variables. Once installed, the `solana` command should be available:

```bash
solana --help
```

### 2. Initialize the Project

Use the `anchor` command to initialize a smart contract project. This CLI tool was installed in the previous step. Don’t worry about the generated directory structure for now:

```bash
anchor init hello_sol
cd hello_sol
```

### 3. Write the Contract Code

In the `programs/hello_sol/src` directory, there is a `lib.rs` file. Files ending in `.rs` are Rust source code. Copy the following code into that file. Note: the `declare_id` value is generated automatically when initializing your project—you don’t need to copy the one shown here exactly:

```rust
use anchor_lang::prelude::*;

declare_id!("3Zbdw1oWu1CiMiQr3moQeT4XzMgeqmCvjH5R5wroDWQH");

#[program]
pub mod hello_sol {
    use super::*;

    pub fn say_hello(ctx: Context<Hello>) -> Result<()> {
        msg!("Hello, world!");
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Hello {}
```

### 4. Compile the Smart Contract

Use the `anchor` command to compile the smart contract code you just added. Make sure it compiles successfully without syntax errors. Rust is a very strict language and may throw lots of warnings—don’t worry about them unless there are errors:

```bash
anchor build
```

### 5. Set the Default Network to Devnet

Run this command to set Solana CLI's default network to devnet. Devnet is for developers and allows testing without using real SOL tokens:

```bash
solana config set --url https://api.devnet.solana.com
```

### 6. Create a Local Account File

This command creates a Solana account file at the default local path. Deploying smart contracts requires fees, which this account will cover:

```bash
solana-keygen new -o ~/.config/solana/id.json  
```

After running this command, look for the output line starting with `pubkey:`—this is your local account address. Since we already set the default network to devnet, you can check your balance with:

```bash
solana balance
```

Or visit the devnet [Explorer](https://explorer.solana.com/?cluster=devnet) and search for your address. The resulting URL will look like:
https://explorer.solana.com/address/75sFifxBt7zw1YrDfCdPjDCGDyKEqLWrBarPCLg6PHwb?cluster=devnet

Of course, your account balance will initially show `0 SOL`.

### 7. Request an Airdrop on Devnet

Run this command to receive 2 SOL in your devnet account. The number `2` specifies how much to request. The maximum per airdrop is 2 SOL, which is sufficient for our use:

```bash
solana airdrop 2
```

### 8. Deploy the Contract to Devnet

Now that you have the contract code, a local account, and a funded wallet, you can deploy the contract to devnet:

```bash
anchor deploy --provider.cluster devnet 
```

If deployment succeeds, you’ll see `Deploy success`. Pay attention to the output line starting with `Program Id:`—that is your contract’s address. You can search it in the devnet Explorer, for example:
https://explorer.solana.com/address/3Zbdw1oWu1CiMiQr3moQeT4XzMgeqmCvjH5R5wroDWQH?cluster=devnet

### 9. Interact with the On-Chain Contract

Go to the `hello_sol/app` directory and create a new file named `app.js`. Paste the following code. It reads your local account file and uses it to send a transaction that calls the deployed smart contract. Every time you run this script, it will create a transaction on-chain:

```javascript
const anchor = require('@coral-xyz/anchor');
const fs     = require('fs');
const os     = require('os');
const path   = require('path');
const { Keypair, Connection } = anchor.web3;

const RPC_URL    = process.env.RPC_URL;
const connection = new Connection(RPC_URL, { commitment: 'confirmed' });

const secretKey = Uint8Array.from(
  JSON.parse(
    fs.readFileSync(
      path.join(os.homedir(), '.config/solana/id.json'),
      'utf8',
    ),
  ),
);

const wallet   = new anchor.Wallet(Keypair.fromSecretKey(secretKey));
const provider = new anchor.AnchorProvider(connection, wallet, {
  preflightCommitment: 'confirmed',
});
anchor.setProvider(provider);

const idlPath = path.resolve(__dirname, '../target/idl/hello_sol.json');
const idl     = JSON.parse(fs.readFileSync(idlPath, 'utf8'));
const program = new anchor.Program(idl, provider);

(async () => {
  try {
    const sig = await program.methods.sayHello().rpc();
    console.log('✅ tx', sig);
    console.log(`🌐 https://explorer.solana.com/tx/${sig}?cluster=devnet`);
  } catch (err) {
    console.error('❌', err);
  }
})();
```

Back in the root `hello_sol` directory, install the Node.js dependencies:

```bash
npm init -y 
npm install @coral-xyz/anchor
```

Then, still in the root directory, run this command to execute the script and invoke your deployed contract:

```bash
export RPC_URL=https://api.devnet.solana.com
node app/app.js
```

The `RPC_URL` environment variable is the API endpoint. Since Node.js scripts don’t use the system proxy by default, if you encounter connectivity issues, consider using a better RPC provider like [Helius](https://www.helius.dev/). If the script throws this error, it's likely a network issue—try a different RPC endpoint:

```javascript
❌ Error: failed to get recent blockhash: TypeError: fetch failed
    at Connection.getLatestBlockhash (/Users/smallyu/work/github/hello_sol/node_modules/@solana/web3.js/lib/index.cjs.js:7236:13)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async AnchorProvider.sendAndConfirm (/Users/smallyu/work/github/hello_sol/node_modules/@coral-xyz/anchor/dist/cjs/provider.js:89:35)
    at async MethodsBuilder.rpc [as _rpcFn] (/Users/smallyu/work/github/hello_sol/node_modules/@coral-xyz/anchor/dist/cjs/program/namespace/rpc.js:15:24)
    at async /Users/smallyu/work/github/hello_sol/app/app.js:40:17
```

You might wonder how the script knows which contract to call without specifying the contract address. Take a look at the `idlPath` variable—it points to the `target/idl/hello_sol.json` file, which contains compiled metadata about the contract, including its address. That's right, the contract address is generated offline and doesn’t require uploading to the chain—it’s deterministically derived.

If everything runs smoothly, the script will output the transaction hash and a browser URL. For example:
https://explorer.solana.com/tx/2fnPgKkv3tGKKq72hhRxmW6WFSXuofMzXfY2UYoFZXTdJi37btdESy9NzS2gjpWzXX4CL5F7QfxugpctBVaMcBFY?cluster=devnet

At the bottom of this transaction page, you should see `Program logged: "Hello, world!"`—this is the `msg!` line from your smart contract.

### 10. Troubleshooting

If you run into issues during any of the above steps, version mismatches may be the cause. The blockchain space evolves quickly, so tools often break with updates. Here are the versions from my local environment:

```text
rustup: rustup 1.28.2 (e4f3ad6f8 2025-04-28)
rustc: rustc 1.90.0-nightly (706f244db 2025-06-23)
solana: solana-cli 2.2.18 (src:8392f753; feat:3073396398, client:Agave)
anchor: anchor-cli 0.31.1
node: v24.2.0
@coral-xyz/anchor (nodejs): ^0.31.1
```
