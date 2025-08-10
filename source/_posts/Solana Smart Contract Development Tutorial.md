---
title: Solana Smart Contract Development Tutorial
tags:
  - Smart Contracts
  - Tutorial
date: 2025-06-24 21:51:06
draft_date: 2025-06-24 18:36:25
---


This is a beginner-friendly series that teaches Solana smart contract development from the ground up. It has 3 parts:

- [Part 1](#Development-Tutorial-1): Install the basics, deploy a HelloWorld program, and call it on-chain
- [Part 2](#Development-Tutorial-2): Implement a minimal USDT-like program with custom data structures and methods
- [Part 3](#Development-Tutorial-3): Reuse functionality via the official SPL library to issue a standards-compliant token


<br>

## Development-Tutorial-1

We’ll start from first principles. You only need general programming knowledge—concepts like OOP are enough. You don’t need prior smart contract experience on other networks, nor do you need to know Rust beforehand.

### 1. Install the toolchain

Follow Solana’s official installation guide: <https://solana.com/docs/intro/installation>

There’s a one-liner to install everything, and also a detailed step-by-step guide. Note that the Solana CLI requires editing your shell’s environment file. After installation, the `solana` command should be available:

```
solana --help
```

### 2. Initialize a project

Use the `anchor` CLI to initialize a new smart contract project. This tool was installed in the previous step. Don’t worry yet about the generated folder structure:

```
anchor init hello_sol
cd hello_sol
```

### 3. Write the program code

In `programs/hello_sol/src` there’s a `lib.rs`. The `.rs` extension means it’s a Rust source file. Paste the following. Note: the value inside `declare_id` is generated for your project during initialization—you’ll already have your own; you don’t need to copy the one below verbatim.

```
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

### 4. Build the smart contract

Use `anchor` to compile the program you just pasted. Make sure it compiles successfully. You may see some warnings—that’s fine. Rust is strict and will warn on small issues. If all goes well, there should be no errors in the output:

```
anchor build
```

### 5. Set the default local cluster

Run this to make the `solana` CLI default to devnet. Devnet is for developers—it lets you test without spending real SOL:

```
solana config set --url https://api.devnet.solana.com
```

### 6. Create a local keypair

This creates a local Solana account used to deploy programs. Deploying costs fees, which must be paid from an account:

```
solana-keygen new -o ~/.config/solana/id.json  
```

In the output, look for the line starting with `pubkey:`—that’s your local account address. Since you set devnet as the default network, you can check your balance directly:

```
solana balance
```

You can also open the devnet [explorer](https://explorer.solana.com/?cluster=devnet) and search for your address. The URL will look like:
https://explorer.solana.com/address/75sFifxBt7zw1YrDfCdPjDCGDyKEqLWrBarPCLg6PHwb?cluster=devnet

You’ll see the balance is `0 SOL`.

### 7. Request a devnet airdrop

Run this to receive 2 SOL (the `2` parameter is the amount). Due to faucet limits, 2 is the max per request—but it’s enough for the steps ahead.

```
solana airdrop 2
```

### 8. Deploy to devnet

You have the program code, a local account, and some SOL. Time to deploy:

```
anchor deploy --provider.cluster devnet 
```

On success you’ll see `Deploy success`. Also look for `Program Id:`—that’s your program’s address. You can search it on the devnet explorer. For example, here’s a page like this (the `3Zbd...DWQH` is the program ID deployed in my example):
https://explorer.solana.com/address/3Zbdw1oWu1CiMiQr3moQeT4XzMgeqmCvjH5R5wroDWQH?cluster=devnet

### 9. Call the on-chain program

In `hello_sol/app`, create a file `app.js` and paste the following. In short, it loads your default local keypair and uses your Solana account to send a transaction that calls the on-chain program. Each run creates one transaction:

```
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

Back at the project root (`hello_sol`), install the Node.js deps:

```
npm init -y 
npm install @coral-xyz/anchor
```

Still at the project root, run the script to call your deployed program on devnet:

```
export RPC_URL=https://api.devnet.solana.com
node app/app.js
```

`RPC_URL` is the endpoint used by the script. Since Node.js doesn’t go through a system proxy by default, if your network is restricted you’ll want a better endpoint than the public RPC—for example, [Helius](https://www.helius.dev/) offers free accounts. If you see an error like below, it’s most likely a network issue—switch to a more reliable RPC:

```
❌ Error: failed to get recent blockhash: TypeError: fetch failed
    at Connection.getLatestBlockhash (/Users/smallyu/work/github/hello_sol/node_modules/@solana/web3.js/lib/index.cjs.js:7236:13)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async AnchorProvider.sendAndConfirm (/Users/smallyu/work/github/hello_sol/node_modules/@coral-xyz/anchor/dist/cjs/provider.js:89:35)
    at async MethodsBuilder.rpc [as _rpcFn] (/Users/smallyu/work/github/hello_sol/node_modules/@coral-xyz/anchor/dist/cjs/program/namespace/rpc.js:15:24)
    at async /Users/smallyu/work/github/hello_sol/app/app.js:40:17
```

You might wonder why we didn’t specify the program address—how does the script know which program you deployed? Notice the `idlPath` variable. Open `target/idl/hello_sol.json` and you’ll find metadata about the compiled program, including the program ID. That’s right—the address is generated offline; the program has a unique address even before deployment.

If the script runs without errors, the terminal will print the transaction signature and a browser URL you can open. For example:
https://explorer.solana.com/tx/2fnPgKkv3tGKKq72hhRxmW6WFSXuofMzXfY2UYoFZXTdJi37btdESy9NzS2gjpWzXX4CL5F7QfxugpctBVaMcBFY?cluster=devnet

At the bottom of that transaction page you’ll see `Program logged: "Hello, world!"`, which is the `msg!` we wrote in the program.

### 10. Troubleshooting

If you hit errors, first suspect version mismatches. The blockchain space iterates quickly and incompatibilities are common. Here are my local versions:

```
rustup: rustup 1.28.2 (e4f3ad6f8 2025-04-28)
rustc: rustc 1.90.0-nightly (706f244db 2025-06-23)
solana: solana-cli 2.2.18 (src:8392f753; feat:3073396398, client:Agave)
archor: anchor-cli 0.31.1
node: v24.2.0
@coral-xyz/anchor(nodejs): ^0.31.1
```


<br><br>

## Development-Tutorial-2

We’ve learned how to create, deploy, and call a program. Now let’s go deeper into program structure and logic by implementing a simple USDT-like token program, reading through the code while learning Solana’s style of smart contracts.

### 1. Create a project

Use the command you already know:

```
anchor init usdt_clone
```

### 2. Config file

Check `programs/usdt_clone/Cargo.toml`. Cargo is Rust’s package manager, and `Cargo.toml` declares dependencies and versions. The autogenerated config contains:

```
[dependencies]
anchor-lang = "0.31.1"
```

Anchor’s macros are key to Solana programs—things like `#[program]` and `#[account]` tell Solana’s SVM where the entrypoints and data structures are. Without the Anchor dependency, this would be a normal Rust project and Solana wouldn’t know how to interpret it. That’s how Solana leverages Rust to implement smart contracts.

### 3. Program address

Open `usdt_clone/programs/usdt_clone/src/lib.rs`. The first line imports common Anchor types, which is fine as-is:

```
use anchor_lang::prelude::*;
```

The second line calls `declare_id` to set the Program ID (the address). As mentioned earlier, Solana program addresses can be generated offline.

```
declare_id!("CFmGdHuqDymqJYBX44fyNjrFoJx6wRkZPkYgZqfkAQvT");
```

This is a random-looking value, but not arbitrary—it’s an Ed25529 public key. If you change the last character `T` to `t`, the string is no longer a valid public key. So while it can be randomly generated, you can’t just tweak it freely. Where’s the private key, then? During initialization, a keypair is created at `target/deploy/usdt_clone-keypair.json`. It’s an array of bytes; the `declare_id` public key is derived from that private key.

### 4. Stored data structures

Add your logic below `declare_id`:

```
#[account]
pub struct Mint {
    pub decimals: u8,
    pub mint_authority: Pubkey,
}
```

You can think of `#[account]` as defining on-chain data structures. Anchor’s “magic” wires things so you can read/write these structures on-chain. Here we define a `Mint` with two fields: `decimals` for token precision, and `mint_authority` for who can mint.

Define another struct to store per-user balances. `owner` is the user address, `balance` is their balance:

```
#[account]
pub struct TokenAccount {
    pub owner: Pubkey,
    pub balance: u64,
}
```

### 5. Account constraint structs

At the bottom of the template you’ll see `#[derive(Accounts)]`—this macro lets you specify constraints for accounts. You can define functions within a `#[derive(Accounts)]` block and then use `#[account]` to define structures; those structures gain behavior akin to methods.

Remove the original `Initialize`:

```
#[derive(Accounts)]
pub struct Initialize {}    // delete
```

Add your own:

```
#[derive(Accounts)]
pub struct InitMint<'info> {
    #[account(
        init, 
        payer = authority,
        space = 8 + 1 + 32
    )]
    pub mint: Account<'info, Mint>,

    #[account(mut)]
    pub authority: Signer<'info>,

    pub system_program: Program<'info, System>,
}
```

A bit to unpack. In `#[account(...)]` we pass 3 parameters defined by Anchor:

- `init` is a flag: create the account if it doesn’t exist.
- `payer` specifies who pays for the account creation.
- `space` is how many bytes to allocate: 8 (discriminator) + 1 for `u8` + 32 for `Pubkey`.

This macro decorates the `mint` field. `Account<'info, Mint>` is an Anchor account type for reading/writing our `Mint` (as opposed to `TokenAccount`, etc.).

Next, `#[account(mut)]` indicates lamports may change. `authority` is a `Signer<'info>`, meaning a signature from the owner is required. The `’info` lifetime is a Rust feature—think of it like a borrowed reference. Finally, `system_program` is boilerplate whenever SOL transfers may occur. Altogether, `InitMint` is a wrapper around `Mint` with account-related constraints.

### 6. Initialize the token program

Focus on the functions under `#[program]`, which marks program entrypoints. The template contains:

```
#[program]
pub mod usdt_clone {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {   // delete
        msg!("Greetings from: {:?}", ctx.program_id);             // delete
        Ok(())                                                    // delete
    }                                                             // delete
}
```

Remove `initialize` and add:

```
pub fn init_mint(ctx: Context<InitMint>, decimals: u8) -> Result<()> {
    let mint = &mut ctx.accounts.mint;
    mint.decimals = decimals;
    mint.mint_authority = ctx.accounts.authority.key();
    Ok(())
}
```

Place `init_mint` where `initialize` was. Ignoring the macros, it’s a normal Rust function. `Context` is provided by Anchor (even though we didn’t define it), and `InitMint` is the type we created earlier.

The first parameter specifies the authority; the second (`u8`) is the token precision. It returns `()`: success returns nothing; errors bubble up.

Inside, we assign to `mint`, which is a deserialized, mutable account from `ctx.accounts`. Because it’s a mutable reference, updating it persists the data on-chain.

### 7. Unit tests

Compile first to ensure you didn’t miss anything—Rust is strict and may produce warnings:

```
anchor build  
```

Then edit `usdt_clone/tests/usdt_clone.ts` and paste:

```
import anchor from "@coral-xyz/anchor";
import { Program } from "@coral-xyz/anchor";
import { SystemProgram, Keypair } from "@solana/web3.js";
import { assert } from "chai";

const { AnchorProvider, BN } = anchor;

describe("usdt_clone / init_mint", () => {
  const provider = AnchorProvider.env();
  anchor.setProvider(provider);
  const program = anchor.workspace.UsdtClone as Program;

  const mintKey = Keypair.generate();

  it("creates a Mint with correct metadata", async () => {
    const txSig = await program.methods
      .initMint(new BN(6))
      .accounts({
        mint: mintKey.publicKey,
        authority: provider.wallet.publicKey,
        systemProgram: SystemProgram.programId,
      })
      .signers([mintKey])
      .rpc();

    console.log("tx:", txSig);

    const mintAccount = await program.account.mint.fetch(mintKey.publicKey);

    assert.equal(mintAccount.decimals, 6);
    assert.equal(
      mintAccount.mintAuthority.toBase58(),
      provider.wallet.publicKey.toBase58()
    );
  });
});
```

This uses the local test framework to call `initMint`, e.g., setting 6 decimals and supplying the 3 accounts that `InitMint` requires. It logs `txSig`, then reads `program.account.mint.fetch` and asserts the values.

Run the tests:

```
anchor test
```

If all goes well you’ll see `1 passing (460ms)`.

### 8. Open account & transfer

Building on what we’ve learned, add two more account structs for opening an account and transferring. The new macro `#[error_code]` defines an enum for program errors:

```
#[derive(Accounts)]
pub struct InitTokenAccount<'info> {
    #[account(init, payer = owner, space = 8 + 32 + 8)]
    pub token: Account<'info, TokenAccount>,
    #[account(mut, signer)]
    pub owner: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct Transfer<'info> {
    #[account(mut, has_one = owner)]
    pub from: Account<'info, TokenAccount>,
    #[account(mut)]
    pub to: Account<'info, TokenAccount>,
    #[account(signer)]
    pub owner: Signer<'info>,
}

#[error_code]
pub enum ErrorCode {
    InsufficientFunds,
    ArithmeticOverflow,
}
```

Add two methods for opening and transferring. Note `token.balance = 1000` grants a default balance of 1000 upon opening—this simplifies tests and can be changed as you like:

```
pub fn init_token_account(ctx: Context<InitTokenAccount>) -> Result<()> {
  let token = &mut ctx.accounts.token;
  token.owner = ctx.accounts.owner.key();
  token.balance = 1000;
  Ok(())
}

pub fn transfer(ctx: Context<Transfer>, amount: u64) -> Result<()> {
  let from = &mut ctx.accounts.from;
  let to   = &mut ctx.accounts.to;

  require!(from.balance >= amount, ErrorCode::InsufficientFunds);

  from.balance -= amount;
  to.balance = to
      .balance
      .checked_add(amount)
      .ok_or(ErrorCode::ArithmeticOverflow)?;

  Ok(())
}
```

Unit tests:

```
const tokenA = Keypair.generate();
const tokenB = Keypair.generate();

it("initializes tokenA & tokenB, each with balance 1000", async () => {
  for (const tok of [tokenA, tokenB]) {
    await program.methods
      .initTokenAccount()
      .accounts({
        token: tok.publicKey,
        owner: provider.wallet.publicKey,
        systemProgram: SystemProgram.programId,
      })
      .signers([tok])
      .rpc();

    const acc = await program.account.tokenAccount.fetch(tok.publicKey);
    assert.equal(
      acc.owner.toBase58(),
      provider.wallet.publicKey.toBase58()
    );
    assert.equal(acc.balance.toNumber(), 1000);
  }
});

it("transfers 250 from A to B (balances 750 / 1250)", async () => {
  await program.methods
    .transfer(new BN(250))
    .accounts({
      from:  tokenA.publicKey,
      to:    tokenB.publicKey,
      owner: provider.wallet.publicKey,
    })
    .rpc();

  const a = await program.account.tokenAccount.fetch(tokenA.publicKey);
  const b = await program.account.tokenAccount.fetch(tokenB.publicKey);

  assert.equal(a.balance.toNumber(), 750);
  assert.equal(b.balance.toNumber(), 1250);
});
```

If you’re curious, try deploying this program to devnet as well and calling it via the SDK.



<br><br>

## Development-Tutorial-3


You may have noticed that describing business logic is relatively lightweight, while `#[account]` types and their parameters (auto-creating accounts, how many bytes to rent, etc.) are the tricky bits. Because all account data must fit into node memory—and memory is expensive—Solana requires careful sizing. The account model is also a bit complex.

### 1. Issue a token via CLI

For USDT-like scenarios, Solana provides library functions and even CLI tools. You can issue tokens without writing a program. Tokens are called SPL Tokens. To create a 6-decimal SPL token (no token name needed):

```
spl-token create-token --decimals 6
```

The output includes an `Address`, the token mint address. For example, mine was `E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV` (view it in the [explorer](https://explorer.solana.com/address/E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV?cluster=devnet)).

Next, create an Associated Token Account (ATA) for your local wallet for this token. This is like instantiating a record in the token program that tracks your balance. Without it, the token program doesn’t “know” you.

“Account” here can be confusing: you already have a wallet address visible via `solana address`, so why create an ATA in the token program? Think of the program maintaining a `map{}`; creating an ATA inserts a key/value where the key is your wallet address and the value is your token balance. If there’s no entry, you can’t even receive transfers.

Why require pre-creation? Because on Solana, on-chain space is scarce and must be paid for. Creating an ATA reserves 165 bytes. You can estimate rent with `solana rent 165`, e.g., `0.00203928 SOL`. Beyond the transaction fee, you pay this rent when creating the ATA. Hence the explicit creation step.

Create the ATA:

```
spl-token create-account E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV
```

You’ll see `Creating account` followed by the ATA address, e.g., `E5XmcEJhhGUri8itThLGk8QfPzY1acFid8JmVyo5DWUo` (also visible in the [explorer](https://explorer.solana.com/address/E5XmcEJhhGUri8itThLGk8QfPzY1acFid8JmVyo5DWUo?cluster=devnet)).

Note that ATAs have their own addresses. If your wallet is `a`, your USDT ATA might be `b`. Receiving and sending USDT uses the ATA, not your base wallet. SPL Token CLI can show the mapping:

```
spl-token address --verbose --token E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV

// output looks like
Wallet address: 75sFifxBt7zw1YrDfCdPjDCGDyKEqLWrBarPCLg6PHwb
Associated token address: E5XmcEJhhGUri8itThLGk8QfPzY1acFid8JmVyo5DWUo
```

Now check your USDT balance. The `balance` parameter takes the mint (token) address, not the ATA:

```
spl-token balance E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV 
```

It’ll be 0 by default. Mint some tokens. The command has 3 parameters: mint address, amount, and ATA (who to mint to):

```
spl-token mint E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV 5 E5XmcEJhhGUri8itThLGk8QfPzY1acFid8JmVyo5DWUo
```

After success, you can query the balance or view it in the explorer. To transfer:

```
spl-token transfer <MINT> 1 <ATA>
```

For convenience, the last parameter can be a wallet address instead of an ATA (the CLI derives the ATA), which is why wallets feel like “there is no ATA”:

```
spl-token transfer <MINT> 1 <RECIPIENT_WALLET>
```

### 2. Write a program using the SPL library

Let’s call SPL library functions from a program. These system-level libraries are audited and safer than rolling your own. With them, you can focus on business logic rather than low-level details like precision math. Create a new project:

```
anchor init usdt_spl
```

Add the `anchor-spl` dependency. This pulls the latest version. After running it, `programs/usdt_spl/Cargo.toml` should include `anchor-spl = "0.31.1"` under `[dependencies]`:

```
cargo add anchor-spl
```

Start coding. First import SPL types. Previously we used Anchor types like `Account` and `Signer`. SPL also provides types—for example, `TokenAccount` represents an ATA:

```
use anchor_spl::token::{self, MintTo, Token, TokenAccount, Mint};
```

Define the accounts for a mint-to operation:

```
#[derive(Accounts)]
pub struct MintToCtx<'info> {
    #[account(mut)]
    pub mint: Account<'info, Mint>, 

    #[account(mut)]
    pub to:   Account<'info, TokenAccount>,

    #[account(mut)]
    pub authority: Signer<'info>,

    pub token_program: Program<'info, Token>,
}
```

`mut` means data is writable. `Account` is the Anchor wrapper we’ve used. `Mint` here is from SPL—we no longer define our own `Mint` struct. Similarly, `TokenAccount` and `Token` are SPL types. Looks simpler, right? Not so fast—one more piece:

```
impl<'info> From<&MintToCtx<'info>> for CpiContext<'_, '_, '_, 'info, MintTo<'info>>
{
    fn from(accts: &MintToCtx<'info>) -> Self {
        let cpi_accounts = MintTo {
            mint:      accts.mint.to_account_info(),
            to:        accts.to.to_account_info(),
            authority: accts.authority.to_account_info(),
        };
        CpiContext::new(accts.token_program.to_account_info(), cpi_accounts)
    }
}
```

This Rust `From` impl converts `&MintToCtx<'info>` into `CpiContext<..., MintTo<'info>>`. CPI stands for Cross-Program Invocation—packaging the target program and accounts into a single structure. The last generic, `MintTo`, is the SPL type we’ll pass.

Why an “external program”? Because SPL Token isn’t just types—it’s an already-deployed on-chain program. Using SPL crates essentially calls those deployed programs. When your program runs and you invoke SPL, it finds the SPL program and executes logic there, returning results. In other words, programs across the network share the same SPL implementation.

Finally, the `#[program]` method:

```
pub fn mint_to(ctx: Context<MintToCtx>, amount: u64) -> Result<()> {
    token::mint_to((&*ctx.accounts).into(), amount)
}
```

### 3. Build the program

You’ll hit a compile error unless you enable features in `programs/usdt_spl/Cargo.toml`:

```
[features]
idl-build = ["anchor-lang/idl-build", "anchor-spl/idl-build"]

[dependencies]
anchor-spl  = { version = "0.31.1", features = ["token", "idl-build"] }
```

Static builds won’t include SPL by default; the config above enables it. Now build:

```
anchor build
```

### 4. Write tests

Install SPL-related Node.js deps. Tests are in TypeScript:

```
npm i @coral-xyz/anchor@^0.31 @solana/spl-token chai
```

Paste into `tests/usdt_spl.ts`:

```
import anchor from "@coral-xyz/anchor";
import { Program } from "@coral-xyz/anchor";
import {
  createMint,
  createAssociatedTokenAccount,
  getAccount,
  TOKEN_PROGRAM_ID,
} from "@solana/spl-token";
import { assert } from "chai";

const { AnchorProvider, BN } = anchor;

describe("usdt_spl / mint_to", () => {
  const provider = AnchorProvider.env();
  anchor.setProvider(provider);
  const program = anchor.workspace.UsdtSpl as Program;

  let mintPubkey: anchor.web3.PublicKey;
  let ata: anchor.web3.PublicKey;

  it("creates mint, mints 1 USDT into ATA", async () => {
    mintPubkey = await createMint(
      provider.connection,
      provider.wallet.payer,          // fee-payer
      provider.wallet.publicKey,      // mint authority
      null,                           // freeze authority
      6                               // decimals
    );

    ata = await createAssociatedTokenAccount(
      provider.connection,
      provider.wallet.payer,          // fee-payer
      mintPubkey,
      provider.wallet.publicKey       // owner
    );

    await program.methods
      .mintTo(new BN(1_000_000))      // 1 USDT
      .accounts({
        mint: mintPubkey,
        to: ata,
        authority: provider.wallet.publicKey,
        tokenProgram: TOKEN_PROGRAM_ID,
      })
      .rpc();

    const accInfo = await getAccount(provider.connection, ata);
    assert.equal(accInfo.amount.toString(), "1000000");
  });
});
```

Run tests:

```
anchor test
```

### 5. Deploy to devnet

Ensure you have enough SOL, then:

```
anchor deploy --provider.cluster devnet 
```

Sometimes you’ll hit `Operation timed out`. You can pass your own RPC URL (wrap long URLs in quotes):

```
anchor deploy --provider.cluster "<your-rpc-url>"
```

Network issues can also leave partial buffers locally or on-chain. To bypass such states, try:

```
solana program deploy \
  target/deploy/usdt_spl.so \
  --program-id target/deploy/usdt_spl-keypair.json \
  --url "<your-rpc-url>"
```

This is often more reliable. Without `--program-id`, it generates a new keypair and deploys to a new address—choose based on your needs. After success, check the [explorer](https://explorer.solana.com/address/CFXzAhGKEz7tSFdNcVeCX8HosFGYczD7rZyD4vwoWozY?cluster=devnet).

### 6. Call the on-chain program via SDK

Let’s use the SDK again. Edit `app/app.js` and paste:

```
/* scripts/mint_to.js   (CommonJS) */
const anchor = require("@coral-xyz/anchor");
const {
  createMint,
  createAssociatedTokenAccount,
  getAccount,
  TOKEN_PROGRAM_ID,
} = require("@solana/spl-token");
const fs   = require("fs");
const os   = require("os");
const path = require("path");
const { Keypair, Connection, PublicKey } = anchor.web3;

const RPC_URL = process.env.RPC_URL || "https://api.devnet.solana.com";
const connection = new Connection(RPC_URL, { commitment: "confirmed" });

const secret = Uint8Array.from(
  JSON.parse(fs.readFileSync(path.join(os.homedir(), ".config/solana/id.json")))
);
const wallet = new anchor.Wallet(Keypair.fromSecretKey(secret));
const provider = new anchor.AnchorProvider(connection, wallet, {
  preflightCommitment: 'confirmed',
});
anchor.setProvider(provider);

const idl  = JSON.parse(fs.readFileSync(path.resolve("target/idl/usdt_spl.json")));
const prog = new anchor.Program(idl, provider);

(async () => {
  const mint = await createMint(connection, wallet.payer, wallet.publicKey, null, 6);
  const ata  = await createAssociatedTokenAccount(connection, wallet.payer, mint, wallet.publicKey);

  const sig = await prog.methods
    .mintTo(new anchor.BN(1_000_000))
    .accounts({ mint, to: ata, authority: wallet.publicKey, tokenProgram: TOKEN_PROGRAM_ID })
    .rpc();

  console.log("tx:", sig);
  console.log(`explorer: https://explorer.solana.com/tx/${sig}?cluster=devnet`);

  const bal = await getAccount(connection, ata);
  console.log("balance:", bal.amount.toString());
})();
```

If everything works, you’ll see:

```
~/work/github/sol_contract/usdt_spl main ❯ node app/app.js
tx: 3MgHxsfnJp68mrrABvCh9iwNm6MSXp1SEvk7vDYHoW7KhTEHfVNyMWsbfbEAXTC9gLzcmWu5xbkzia8hgZrcZ18i
explorer: https://explorer.solana.com/tx/3MgHxsfnJp68mrrABvCh9iwNm6MSXp1SEvk7vDYHoW7KhTEHfVNyMWsbfbEAXTC9gLzcmWu5xbkzia8hgZrcZ18i?cluster=devnet
balance: 1000000
```
