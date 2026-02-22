# Stak.fun Wallet Bundler

A **client-side Solana multi-wallet bundle executor** powered by
**Jupiter Ultra routing**, designed for advanced users who need precise
execution control, distributed wallet swaps, and predictable transaction
orchestration.

------------------------------------------------------------------------

# 🚀 Overview

Stak.fun is not just a swap interface.

It is a:

> Client-side Solana bundle executor with Ultra routing, sequential
> orchestration, and server-verified time-gated access control.

Designed for:

-   Multi-wallet coordinated buys\
-   Controlled execution stacking\
-   Liquidity-sensitive entries\
-   RPC-safe sequential swaps\
-   Advanced Solana users

------------------------------------------------------------------------

# 🔑 Core Features

## Client-Side Key Handling

-   Private keys are never stored
-   Private keys are never sent to backend
-   Signing occurs fully in-browser
-   Backend only receives signed serialized transactions

------------------------------------------------------------------------

## 👛 Multi-Wallet Stack System

-   1 Active wallet
-   Up to 15 Stack wallets (16 total)
-   Drag-handle only reordering (SortableJS)

Execution order:

Stack wallets (top → bottom)\
Active wallet executes last

------------------------------------------------------------------------

## 🔄 Jupiter Ultra Integration

Uses **Jupiter Ultra API** (not public routing):

-   Advanced routing engine
-   Higher reliability under load
-   Better liquidity pathing
-   Reduced failed swaps

Ultra endpoints are proxied via backend to protect the API key.

------------------------------------------------------------------------

## 📊 Real-Time Execution Feedback

-   Per-wallet status tracking
-   Solscan links on success
-   Ambiguous RPC simulation errors treated as "Pending"
-   Wallets without sufficient funds are skipped automatically

------------------------------------------------------------------------

## 🕒 Time-Limited Access Gate

Includes a server-verified access control system:

-   Time-based usage windows
-   Optional top-ups
-   Access state stored in Redis
-   Payment verification via service wallet
-   Optional token-based validation (SPL mint support)

Access must be valid before swap execution.

------------------------------------------------------------------------

# 🏗 Architecture

## Frontend

-   Vanilla JavaScript
-   `@solana/web3.js`
-   `tweetnacl`
-   `SortableJS`

All transaction construction and signing happens locally.

## Backend (Vercel Serverless Functions)

-   Jupiter Ultra proxy
-   Transaction relay
-   Access validator
-   Payment verifier
-   Metadata fetcher
-   Redis session manager

Backend never receives private keys.

------------------------------------------------------------------------

# 📂 Project Structure

/ ├── app.js\
└── api/\
    ├── new-address.js\
    ├── sol-balance.js\
    ├── token-balance.js\
    ├── token-supply.js\
    ├── ultra-order.js\
    ├── ultra-execute.js\
    └── send-tx.js

------------------------------------------------------------------------

# ⚙ Environment Variables

SOLANA_RPC=
SOLANATRACKER_RPC=
SOLANATRACKER_API_KEY=
HELIUS_API_KEY=

JUPITER_ULTRA_API_KEY=

UPSTASH_REDIS_REST_URL=
UPSTASH_REDIS_REST_TOKEN=

MAIN_WALLET_ADDRESS=
MAIN_WALLET_PRIVATE=

SPL_MINT=

------------------------------------------------------------------------

# 🔁 Execution Flow

For each wallet:

1.  Validate access (Redis check)
2.  Fetch Jupiter Ultra quote
3.  Build transaction via Ultra API
4.  Deserialize transaction in browser
5.  Sign locally
6.  Send signed transaction to backend
7.  Backend relays via RPC
8.  Signature returned
9.  UI updates status
10. Solscan link displayed

Each wallet executes with \~300ms stagger.

------------------------------------------------------------------------

# 🖥 Self-Hosting Guide

## Requirements

-   Node.js 20+
-   Vercel account
-   Solana RPC endpoint
-   Jupiter Ultra API key
-   Upstash Redis

## Setup

git clone `<https://github.com/pepeskull/stak.fun.git>`{=html}\
cd project\
npm install

Configure environment variables and deploy:

vercel deploy

### Security Notes

-   Never expose server-side keys
-   Use private RPC endpoint
-   Restrict origins
-   Consider rate limiting

------------------------------------------------------------------------

# 🧠 Developer Deep-Dive

## Swap Lifecycle

1.  Fetch metadata
2.  Fetch balances
3.  Fetch Ultra quote
4.  Build unsigned tx
5.  Sign locally
6.  Relay to backend
7.  Receive signature

Sequential execution prevents:

-   Liquidity race conditions
-   Blockhash conflicts
-   RPC overload

------------------------------------------------------------------------

# 🛡 Security Overview

## Key Management

-   Keys exist only in browser memory
-   Never transmitted
-   Never logged

## Backend Limitations

Backend cannot:

-   Modify transactions
-   Re-sign transactions
-   Access user keys

## Redis Model

wallet_address → expiry_timestamp

## Trust Model

User trusts:

-   Backend relay integrity
-   Jupiter Ultra routing
-   RPC provider

User does NOT trust backend with private keys or funds.

------------------------------------------------------------------------

# ⚠ Limitations

-   No wallet adapter support
-   No hardware wallet support
-   Sequential execution only
-   Dependent on Jupiter Ultra availability
-   Requires active access window

------------------------------------------------------------------------

# 🛠 Development

npm install

Deploy via Vercel.

------------------------------------------------------------------------

# 📄 License

MIT
