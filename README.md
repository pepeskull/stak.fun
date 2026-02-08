# Stak.fun wallet bundler

A web-based tool that lets you **execute multiple Solana swaps in a controlled bundle**, using multiple wallets, with **drag‑and‑drop execution order**, live quotes via **Jupiter**, and a **clear execution modal**. It also includes a **time‑limited access gate** with optional top‑ups and a **buy/sell mode** for bundle trades.

This project is designed for power users who want to:

* Buy the same token from **multiple wallets**
* Control **execution order** (stack order)
* Avoid RPC overload with **staggered execution**
* See **real transaction status** instead of silent failures

---

## Features

* **Client‑side key handling** (keys are never stored or sent elsewhere)
* **Active wallet + stack wallets**
* **Drag‑and‑drop stack ordering** (handle-only dragging)
* **Live SOL balance + Jupiter quotes**
* **Buy totals as % of token supply, sell totals in SOL**
* **Staggered execution** (RPC‑safe)
* **Execution modal with per‑wallet status**
* **Solscan links on success**

---

## Architecture Overview

### Frontend

* Vanilla JS
* No framework dependencies
* Uses:

  * `@solana/web3.js` (browser build)
  * `tweetnacl` for key handling
  * `SortableJS` for drag ordering

### Backend (API routes)

* Deployed on **Vercel**
* Acts as a **transaction relay only**
* Never receives private keys

---

## Project Structure

```text
/
├── app.js              # Core logic
│
└── api/
    ├── new-address.js  # Token metadata (SolanaTracker)
    ├── sol-balance.js  # SOL balance lookup
    ├── token-balance.js # SPL token balance lookup
    ├── token-supply.js  # SPL token supply lookup
    ├── ultra-order.js  # Jupiter Ultra order proxy
    ├── ultra-execute.js # Jupiter Ultra execute proxy
    └── send-tx.js      # RPC transaction relay
```

---

## Environment Variables

Set these in Vercel (or your local environment):

```bash
SOLANA_RPC  =https://your.rpc.endpoint
SOLANATRACKER_API_KEY = your_api_key
```

---

## How It Works

### 1. Add wallets

* One wallet is active by default
* Add up to **16 wallets total**
* Stack wallets appear on the right

### 2. Drag to reorder

* Drag using the ☰ handle only
* Empty slots stay fixed at the bottom
* **Execution order = stack order + active wallet last**

### 3. Enter token mint

* Token metadata fetched automatically
* Logo + symbol displayed
* Token decimals cached for quotes

### 4. Enter amount

* Balance shown per wallet
* Quote fetched from Jupiter
* Total SOL calculated live

### 5. Buy or Sell Bundle

* Execution modal always opens
* Wallets execute **sequentially** (300ms spacing)
* Status updates in real time

---

## Execution Logic

* Wallets without a valid key or SOL amount are skipped
* Transactions are **staggered** to prevent RPC failures
* Some RPCs may report `simulation failed` even if the transaction lands
* UI treats ambiguous RPC errors as **Pending**, not Failed

---

## Security Notes

* ❗ Private keys are **never stored**
* ❗ Private keys are **never sent to the backend**
* Signing happens **locally in the browser**
* Backend only receives a **signed, serialized transaction**

---

## Known RPC Behavior

You may see logs like:

```
Simulation failed: insufficient lamports
```

Even when:

* The transaction succeeds
* Tokens arrive in the wallet

This is a known Solana RPC edge case. The UI is designed to handle this safely.

---

## Limitations

* No Phantom / wallet adapter support (by design)
* No transaction batching (sequential only)
* Requires sufficient SOL for ATA creation + fees

---

## Development

```bash
npm install
```

---

## License

MIT

---

## Notes

This project intentionally avoids heavy frameworks to keep:

* Key handling transparent
* Execution predictable
* Debugging straightforward

If you know what you’re doing on Solana — this tool is built for you.
