---
title: Solana Smart Contract Development Tutorial (3)
date: 2025-06-28 00:01:01
tags:
  - Smart Contract
  - Tutorial
---

> This is a beginner-friendly tutorial series that teaches Solana smart contract development from the very basics.
> 
> - [Part 1](/2025/06/24/Solana%20Smart%20Contract%20Development%20Tutorial%20(1)/): Basic environment setup, deploying HelloWorld contract, on-chain contract interaction
> - [Part 2](/2025/06/26/Solana%20Smart%20Contract%20Development%20Tutorial%20(2)/): Creating a minimal USDT contract model, custom data structures and methods
> - [Part 3](/2025/06/28/Solana%20Smart%20Contract%20Development%20Tutorial%20(3)/): Using the official SPL library to reuse contract functionality and issue a standard token

You may notice that writing smart contract logic is relatively lightweight. The complexity lies mostly in different `#[account]` macro usages and understanding parameters like whether accounts can be auto-created, how many bytes to reserve, etc. Because all account data must be loaded into Solana validator memory—which is costly—developers need to be precise with space usage. Solana’s account model also requires some understanding.

### 1. Issue Tokens via CLI

For standard tokens like USDT, Solana provides built-in libraries and command-line tools—no need to write contracts. These are called SPL Tokens. To create a token with 6 decimal places:

```bash
spl-token create-token --decimals 6
```

After executing, you'll see an `Address`, which is your token’s address, e.g., `E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV`. You can look it up in the [block explorer](https://explorer.solana.com/address/E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV?cluster=devnet).

Next, create an Associated Token Account (ATA) for your wallet:

```bash
spl-token create-account E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV
```

This creates a record in the token contract’s internal map. Without it, your address can’t hold USDT. To view your ATA:

```bash
spl-token address --verbose --token E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV
```

Example output:

```
Wallet address: 75sFifxBt7zw1YrDfCdPjDCGDyKEqLWrBarPCLg6PHwb  
Associated token address: E5XmcEJhhGUri8itThLGk8QfPzY1acFid8JmVyo5DWUo
```

Check token balance:

```bash
spl-token balance E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV 
```

To mint tokens:

```bash
spl-token mint E75GMXAfJ91XuRboSpjwkDmta45Etgt3F3Gf5WLZvLbV 5 E5XmcEJhhGUri8itThLGk8QfPzY1acFid8JmVyo5DWUo
```

To transfer tokens:

```bash
spl-token transfer <MINT> 1 <ATA>
# or
spl-token transfer <MINT> 1 <RECIPIENT_WALLET>
```

### 2. Writing Contracts with SPL Standard Libraries

Let’s create a project that uses the official `anchor-spl` crate for minting:

```bash
anchor init usdt_spl
cargo add anchor-spl
```

Add SPL imports in your contract:

```rust
use anchor_spl::token::{self, MintTo, Token, TokenAccount, Mint};
```

Define the mint context:

```rust
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

Implement conversion to CPI context:

```rust
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

Add program logic:

```rust
pub fn mint_to(ctx: Context<MintToCtx>, amount: u64) -> Result<()> {
    token::mint_to((&*ctx.accounts).into(), amount)
}
```

Update `Cargo.toml` features:

```toml
[features]
idl-build = ["anchor-lang/idl-build", "anchor-spl/idl-build"]

[dependencies]
anchor-spl = { version = "0.31.1", features = ["token", "idl-build"] }
```

Compile:

```bash
anchor build
```

### 3. Unit Testing

Install required dependencies:

```bash
npm i @coral-xyz/anchor@^0.31 @solana/spl-token chai
```

Test file `tests/usdt_spl.ts`:

```ts
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
      provider.wallet.payer,
      provider.wallet.publicKey,
      null,
      6
    );

    ata = await createAssociatedTokenAccount(
      provider.connection,
      provider.wallet.payer,
      mintPubkey,
      provider.wallet.publicKey
    );

    await program.methods
      .mintTo(new BN(1_000_000))
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

Run the tests:

```bash
anchor test
```

### 4. Deploy to Devnet

Deploy using anchor:

```bash
anchor deploy --provider.cluster devnet
```

If network issues occur, try:

```bash
anchor deploy --provider.cluster "<your-rpc-url>"
# Or
solana program deploy \
  target/deploy/usdt_spl.so \
  --program-id target/deploy/usdt_spl-keypair.json \
  --url "<your-rpc-url>"
```

### 5. Use SDK to Interact with Deployed Contract

File: `app/app.js`:

```js
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
const { Keypair, Connection } = anchor.web3;

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

Expected output:

```
tx: 3MgHxsfnJp68mrrABvCh9iwNm6MSXp1SEvk7vDYHoW7KhTEHfVNyMWsbfbEAXTC9gLzcmWu5xbkzia8hgZrcZ18i
explorer: https://explorer.solana.com/tx/3MgHxsfnJp68mrrABvCh9iwNm6MSXp1SEvk7vDYHoW7KhTEHfVNyMWsbfbEAXTC9gLzcmWu5xbkzia8hgZrcZ18i?cluster=devnet
balance: 1000000
```
