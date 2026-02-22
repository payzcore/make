# PayzCore - Make (Integromat) Custom App

**Version:** 1.0.0

Custom app integration for [Make](https://www.make.com/) (formerly Integromat) that connects to the PayzCore blockchain transaction monitoring API.

## Overview

PayzCore is a non-custodial stablecoin monitoring API. This Make custom app allows you to:

- **Watch New Payments** (polling trigger) - Monitor for new payment events
- **Create Payment** - Create a new payment monitoring request (supports static wallet mode)
- **Confirm Payment** - Submit a transaction hash to confirm a static wallet payment
- **Get Payment** - Retrieve a single payment by ID
- **List Payments** - List payments with optional status filter
- **PayzCore Webhook** (instant trigger) - Receive real-time webhook notifications

Supported chains: TRC20, BEP20, ERC20, Polygon, Arbitrum
Supported tokens: USDT, USDC (USDC not available on TRC20)

## Important

**PayzCore is a blockchain monitoring service, not a payment processor.** All payments are sent directly to your own wallet addresses. PayzCore never holds, transfers, or has access to your funds.

- **Your wallets, your funds** — You provide your own wallet (HD xPub or static addresses). Customers pay directly to your addresses.
- **Read-only monitoring** — PayzCore watches the blockchain for incoming transactions and sends webhook notifications. That's it.
- **Protection Key security** — Sensitive operations like wallet management, address changes, and API key regeneration require a Protection Key that only you set. PayzCore cannot perform these actions without your authorization.
- **Your responsibility** — You are responsible for securing your own wallets and private keys. PayzCore provides monitoring and notification only.

## Setup

### 1. Import into Make Developer Hub

1. Go to [Make Developer Hub](https://www.make.com/en/developer-hub)
2. Create a new custom app
3. Import the JSON configuration files from this package following the directory structure

### 2. Configure Connection

When adding the PayzCore connection in Make:

- **API Key**: Your PayzCore project API key (`pk_live_...`). Find it in your PayzCore dashboard under Projects.
- **API URL**: Leave as default (`https://api.payzcore.com`) unless you run a self-hosted instance.

### 3. Use Modules

#### Watch New Payments (Polling Trigger)

Polls for new payments created after the last check. Optionally filter by status.

#### Create Payment (Action)

Creates a new payment monitoring request. Required fields:
- **Amount** - Payment amount in stablecoin
- **Blockchain** - Target chain (TRC20, BEP20, ERC20, Polygon, Arbitrum)
- **External Reference** - Your user/customer identifier

Optional fields:
- **Address** - Provide a static wallet address to skip HD derivation. When used, the response includes `requires_txid: true` and a `confirm_endpoint` for submitting the transaction hash after payment.

#### Confirm Payment (Action)

Submits a transaction hash to confirm a static wallet payment. Required fields:
- **Payment ID** - UUID of the payment to confirm
- **Transaction Hash** - Blockchain transaction hash as proof of payment

Use this after creating a payment with a static wallet address. The API verifies the transaction on-chain and updates the payment status.

#### Get Payment (Action)

Retrieves full payment details by payment UUID.

#### List Payments (Action)

Lists payments with optional status filter and limit.

#### PayzCore Webhook (Instant Trigger)

Receives real-time webhook events from PayzCore. To set up:

1. Add the "PayzCore Webhook" trigger to your scenario
2. Copy the webhook URL provided by Make
3. In your PayzCore dashboard, go to your project settings
4. Paste the Make webhook URL as your project's Webhook URL
5. Events (`payment.completed`, `payment.overpaid`, `payment.expired`, `payment.partial`, `payment.cancelled`) will be delivered instantly

## File Structure

```
make/
  base.json              - Base URL and auth headers
  common.json            - Shared constants (chains, tokens, statuses)
  groups.json            - Module grouping
  connection/
    parameters.json      - Connection input fields
    communication.json   - Connection test request
  modules/
    watchNewPayments/    - Polling trigger
    createPayment/       - Create action (supports static wallet address)
    confirmPayment/      - Confirm static wallet payment with tx hash
    getPayment/          - Read action
    listPayments/        - Search action
  webhooks/
    payzcoreWebhook/     - Instant webhook trigger
```

## Links

- [PayzCore Dashboard](https://app.payzcore.com)
- [API Documentation](https://docs.payzcore.com)
- [PayzCore Website](https://payzcore.com)

## Before Going Live

**Always test your setup before accepting real payments:**

1. **Verify your wallet** — In the PayzCore dashboard, verify that your wallet addresses are correct. For HD wallets, click "Verify Key" and compare address #0 with your wallet app.
2. **Run a test order** — Place a test order for a small amount ($1–5) and complete the payment. Verify the funds arrive in your wallet.
3. **Test sweeping** — Send the test funds back out to confirm you control the addresses with your private keys.

> **Warning:** Wrong wallet configuration means payments go to addresses you don't control. Funds sent to incorrect addresses are permanently lost. PayzCore is watch-only and cannot recover funds. Please test before going live.

## License

MIT - PayzCore 2026
