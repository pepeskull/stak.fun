# 🚀 Stak.fun Wallet Bundler

```{=html}
<p align="center">
```
`<b>`{=html}Client-Side Multi-Wallet Solana Bundle
Executor`</b>`{=html}`<br>`{=html} Powered by Jupiter Ultra Routing •
Sequential Execution • Access-Gated Infrastructure
```{=html}
</p>
```

------------------------------------------------------------------------

## 📌 Overview

Stak.fun is a **client-side Solana multi-wallet bundle executor**
designed for advanced users who require:

-   Deterministic execution ordering
-   Distributed wallet entries
-   Liquidity-sensitive swaps
-   RPC-safe transaction orchestration
-   Time-gated access control

This is not a typical swap UI --- it is a structured execution engine.

------------------------------------------------------------------------

## ✨ Key Features

### 🔑 Client-Side Key Management

-   Private keys never leave the browser
-   No key storage
-   No backend transmission
-   Signing happens locally via `@solana/web3.js` + `tweetnacl`

### 👛 Multi-Wallet Stack System

-   1 Active wallet
-   Up to 15 Stack wallets (16 total)
-   Drag-handle-only ordering (SortableJS)
-   Deterministic execution:

```{=html}
<!-- -->
```
    Stack wallets (top → bottom)
    Active wallet executes last

### 🔄 Jupiter Ultra Integration

-   Uses **Jupiter Ultra API**
-   Backend proxy protects API keys
-   Higher routing reliability
-   Improved liquidity handling

### 📊 Real-Time Execution Modal

-   Per-wallet status updates
-   Solscan links on success
-   Ambiguous RPC simulation errors marked as "Pending"
-   Automatic skip for insufficient balances

### 🕒 Time-Limited Access Gate

-   Redis-based access control
-   Expiry timestamps per wallet
-   Optional top-ups
-   Optional SPL token validation
-   Service wallet payment verification

------------------------------------------------------------------------

## 🏗 Architecture

### Frontend

-   Vanilla JavaScript
-   `@solana/web3.js`
-   `tweetnacl`
-   `SortableJS`

All transaction construction and signing occur locally.

### Backend (Vercel Serverless Functions)

-   Jupiter Ultra proxy
-   Signed transaction relay
-   Access validator (Redis)
-   Payment verification (service wallet)
-   Metadata + balance lookup endpoints

Backend never accesses private keys.

------------------------------------------------------------------------

## 📂 Project Structure

    /
    ├── app.js
    └── api/
        ├── new-address.js
        ├── sol-balance.js
        ├── token-balance.js
        ├── token-supply.js
        ├── ultra-order.js
        ├── ultra-execute.js
        └── send-tx.js

------------------------------------------------------------------------

## ⚙ Environment Variables

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

## 🔁 Execution Flow

For each wallet:

1.  Validate Redis access window
2.  Fetch Jupiter Ultra quote
3.  Request unsigned transaction
4.  Deserialize in browser
5.  Sign locally
6.  Send signed tx to `/api/send-tx`
7.  Backend relays via `sendRawTransaction`
8.  Return signature
9.  Update UI state

Each wallet executes with \~300ms stagger to reduce RPC collision and
liquidity race conditions.

------------------------------------------------------------------------

## 🖥 Self-Hosting

### Requirements

-   Node.js 18+
-   Vercel account (or serverless platform)
-   Solana RPC endpoint
-   Jupiter Ultra API key
-   Upstash Redis instance

### Setup

``` bash
git clone <repo-url>
cd project
npm install
vercel deploy
```

### Production Hardening

-   Use private RPC
-   Add rate limiting
-   Restrict CORS origins
-   Protect environment variables
-   Monitor RPC reliability

------------------------------------------------------------------------

## 🧠 Developer Deep Dive

### Why Sequential Execution?

Parallel swaps can cause: - Liquidity invalidation - Route recalculation
failure - RPC rate limiting - Blockhash conflicts

Sequential staggered execution ensures deterministic behavior.

### Ultra Proxy Model

Frontend never contacts Jupiter Ultra directly:

    Frontend → /api/ultra-order → Jupiter Ultra
    Frontend → /api/ultra-execute → Jupiter Ultra

API keys remain server-side only.

------------------------------------------------------------------------

## 🛡 Security Overview

### Key Isolation

-   Keys exist only in browser memory
-   Never transmitted
-   Never logged

### Transaction Integrity

-   Backend receives fully signed transactions
-   Cannot modify instructions
-   Cannot re-sign or alter swaps

### Redis Access Model

    wallet_address → expiry_timestamp

Access enforced server-side.

### Trust Model

User must trust: - Backend relay integrity - Jupiter Ultra routing
correctness - RPC provider reliability

User does NOT need to trust: - Backend with private keys - Backend with
swap logic - Backend with custody of funds

------------------------------------------------------------------------

## ⚠ Known Limitations

-   No hardware wallet support
-   No wallet adapter integration (intentional)
-   Sequential execution only
-   Requires sufficient SOL for ATA + fees
-   Dependent on Jupiter Ultra uptime

------------------------------------------------------------------------

## 🛠 Development

    npm install
    vercel dev

------------------------------------------------------------------------

## 📄 License

MIT
