---
title: Solana Smart Contract Development Tutorial (2)
date: 2025-06-26 13:56:54
tags:
  - Smart Contract
  - Tutorial
---

> This is a beginner-friendly tutorial series that teaches Solana smart contract development from the very basics.
> 
> - [Part 1](/2025/06/24/Solana%20Smart%20Contract%20Development%20Tutorial%20(1)/): Basic environment setup, deploying HelloWorld contract, on-chain contract interaction
> - [Part 2](/2025/06/26/Solana%20Smart%20Contract%20Development%20Tutorial%20(2)/): Creating a minimal USDT contract model, custom data structures and methods
> - [Part 3](/2025/06/28/Solana%20Smart%20Contract%20Development%20Tutorial%20(3)/): Using the official SPL library to reuse contract functionality and issue a standard token

Now that we’ve learned how to create, deploy, and call Solana smart contracts, let’s dive deeper into writing custom logic. We’ll walk through building a simplified USDT token contract to understand how smart contracts work on Solana.

### 1. Create a Project

Use the command we’ve already learned to create a new project:

```bash
anchor init usdt_clone
```

### 2. Configuration File

Take a look at the `programs/usdt_clone/Cargo.toml` file. Cargo is Rust’s package manager, and `Cargo.toml` defines dependencies and their versions. You’ll see this line:

```rust
[dependencies]
anchor-lang = "0.31.1"
```

The macros provided by Anchor—like `#[program]`, `#[account]`, etc.—are essential for Solana smart contracts. These macros instruct the Solana Virtual Machine (SVM) where the program logic and data structures are defined. Without this dependency, the project is just a regular Rust application.

### 3. Contract Address

Open the main contract file: `usdt_clone/programs/usdt_clone/src/lib.rs`. The first line imports the Anchor types:

```rust
use anchor_lang::prelude::*;
```

The second line defines the contract’s Program ID:

```rust
declare_id!("CFmGdHuqDymqJYBX44fyNjrFoJx6wRkZPkYgZqfkAQvT");
```

This address is a valid Ed25529 public key. It’s randomly generated during project initialization, with the corresponding private key stored in `target/deploy/usdt_clone-keypair.json`.

### 4. Data Structures for Storage

Right below `declare_id`, add this:

```rust
#[account]
pub struct Mint {
    pub decimals: u8,
    pub mint_authority: Pubkey,
}
```

This struct stores token metadata: `decimals` defines precision (e.g., 6 for USDT), and `mint_authority` defines who can mint new tokens.

Now define another struct for user token accounts:

```rust
#[account]
pub struct TokenAccount {
    pub owner: Pubkey,
    pub balance: u64,
}
```

### 5. Account Constraint Structures

Delete the default `Initialize` struct:

```rust
#[derive(Accounts)]
pub struct Initialize {}    // Delete this
```

And replace it with:

```rust
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

This structure defines the context for initializing a mint. The macro parameters indicate:
- `init`: Create the account if it doesn’t exist.
- `payer = authority`: The `authority` account pays the creation fee.
- `space = 8 + 1 + 32`: Reserve 8 bytes for the discriminator, 1 byte for `u8`, and 32 for `Pubkey`.

### 6. Initialize the Token Contract

Delete the default `initialize` function:

```rust
#[program]
pub mod usdt_clone {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {   // Delete
        msg!("Greetings from: {:?}", ctx.program_id);             // Delete
        Ok(())                                                    // Delete
    }                                                             // Delete
}
```

And replace it with:

```rust
pub fn init_mint(ctx: Context<InitMint>, decimals: u8) -> Result<()> {
    let mint = &mut ctx.accounts.mint;
    mint.decimals = decimals;
    mint.mint_authority = ctx.accounts.authority.key();
    Ok(())
}
```

This function receives the context (containing accounts) and token precision, then initializes the `mint` account accordingly.

### 7. Unit Test

Run a build to verify everything compiles:

```bash
anchor build
```

Then open `usdt_clone/tests/usdt_clone.ts` and paste this:

```ts
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

Run the test:

```bash
anchor test
```

If successful, you’ll see: `1 passing (460ms)`

### 8. Account Initialization & Token Transfer

Add these structs for account creation and token transfer logic:

```rust
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

Now add the functions:

```rust
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

And test them with:

```ts
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

If you’re interested, try deploying this contract to devnet and interact with it using the SDK!
