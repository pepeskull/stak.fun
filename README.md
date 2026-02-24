# stak.fun

I’m bringing bundling back. Not as a hype product. Not as a paid “alpha tool.” Just as a simple execution tool.

Bundling is one of the most important tools during a token launch, but today it is either too expensive, too complicated or built in a way that forces you to trust someone else with your keys.
So I built stak.fun, a client-side Solana multi-wallet bundle executor. It lets you execute swaps from multiple wallets using Jupiter Ultra routing.
It is made for traders, devs and launch teams who want control over execution.


## why bundling matters

When a token launches everything happens very fast. Snipers enter immediately, liquidity changes in seconds and distribution becomes messy. The chart can look broken before the project even starts. Many teams lose control in this moment.

Bundling helps reduce that chaos. Instead of doing one large buy from one wallet, you can split buys across many wallets and execute them one by one in order. This creates better distribution and avoids extreme imbalance in early trading.

It does not remove risk or stop snipers, but it gives you more structure and more control.


## what this tool does

stak.fun allows you to use up to 16 wallets at the same time. Execution is sequential on purpose with a small delay between each wallet (~300ms).
Parallel execution may look faster, but it causes problems:

* liquidity can move between swaps
* blockhash can expire
* RPC can rate limit you
* some wallets can fail while others succeed

Sequential execution is more stable and more predictable.

All transactions are built and signed inside your browser. The backend only receives signed transactions and sends them to the RPC.

The backend never has your private keys.


## how execution works

For each wallet:

1. The system checks if the wallet has valid access (stored in Redis).
2. A Jupiter Ultra quote is requested.
3. An unsigned transaction is created.
4. The transaction is loaded in your browser.
5. The wallet signs it locally.
6. The signed transaction is sent to the backend.
7. The backend sends it to the RPC.
8. The transaction signature is returned.
9. The UI updates and shows a Solscan link.

If a wallet does not have enough balance, it is skipped automatically.
If the RPC gives unclear simulation errors, the transaction is marked as pending instead of failed, because Solana RPC can sometimes be inconsistent during high load.

The goal is stable execution, not maximum speed.


## private keys and security

Your private keys:

* are never stored
* are never sent to the backend
* are never logged

They only exist in your browser memory during the session.

The backend cannot:

* read your keys
* change your transaction
* sign on your behalf
* move your funds

It only forwards signed transactions.

You trust:

* your browser
* the RPC provider
* Jupiter Ultra routing
* the backend relay to forward your transaction honestly

You do not give custody to this tool.


## jupiter ultra

This tool uses Jupiter Ultra (not public routing). Ultra gives better routing and better reliability during high activity. The Ultra API key is protected on the backend. It is never exposed to users.
Routing happens server-side. Signing happens client-side.


## access control

This tool includes time-based access control.

Access is stored in Redis like this:

wallet_address → expiry_timestamp

Before swaps run, access is checked.

If access is expired, swaps will not execute.

The system supports:

* time-based access
* top-ups
* payment verification via service wallet
* optional SPL token validation

Access must be valid before execution starts.


## architecture

### frontend

* Vanilla JavaScript
* @solana/web3.js
* tweetnacl
* SortableJS

All transaction building and signing happens in the browser.

No heavy framework.


### backend (vercel serverless)

The backend handles:

* Jupiter Ultra proxy
* transaction relay
* access validation
* payment verification
* metadata fetching
* Redis session storage

The backend does not store or manage private keys.


## project structure

```
/
├── index.html
├── package.json
├── style.css
├── app.js
└── api/
    ├── new-address.js
    ├── sol-balance.js
    ├── token-balance.js
    ├── token-supply.js
    ├── ultra-order.js
    ├── ultra-execute.js
    └── send-tx.js
```

Simple and direct.


## environment variables

```
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
```

If you deploy yourself:

* Use a private RPC
* Do not expose server keys
* Restrict origins
* Consider rate limiting


## self-hosting

Requirements:

* Node.js 20+
* Vercel account
* Solana RPC endpoint
* Jupiter Ultra API key
* Upstash Redis

Setup:

```
git clone https://github.com/pepeskull/stak.fun.git
cd project
npm install
```

Then deploy:

```
vercel deploy
```


## limitations

This tool is focused.

It does not support:

* wallet adapters
* hardware wallets
* parallel execution
* offline signing

It depends on:

* Jupiter Ultra uptime
* your RPC quality
* valid access window

This is not a trading terminal. It is an execution tool.


## license

MIT
