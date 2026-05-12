# PulsePay ⚡

PulsePay is a **Real-Time Streaming Payment Platform** that enables per-second billing for physical and digital services. Powered by **Superfluid**, PulsePay brings the concept of continuous money streams to everyday businesses like gyms, EV charging stations, parking lots, and co-working spaces. 

Instead of pre-paying or relying on discrete transaction checkpoints, users "stream" payments automatically for the precise duration they consume a service.

## 🚀 Key Features

- **Continuous Per-Second Billing**: Users pay exactly for what they use down to the second using Superfluid continuous streams.
- **Service Agnostic Architecture**: Built to handle multiple merchant/store types out of the box (e.g., `GYM`, `EV`, `WIFI`, `PARKING`).
- **Scan-to-Start Integration**: Users can simply scan a merchant's QR Code to initiate a service session and start streaming funds instantly.
- **Dual Wallet System**: Complete abstraction with in-app dual-wallet configurations for both User accounts (payers) and Store accounts (receivers).
- **Session Management**: Full lifecycle management of streaming parameters (start, bill, pause, end) tied directly to a continuous `StreamSession`.
- **User-Merchant History**: Transparent tracking of users and the stores they frequent, allowing for loyalty building and analytics.

## 🛠 Tech Stack

- **Backend**: Node.js & Express.js
- **Database**: MongoDB with Mongoose (Document-based relational mapping)
- **Blockchain integration**: Superfluid Protocol (via `@superfluid-finance/sdk-core` and `ethers`)
- **API Documentation**: Swagger/OpenAPI (`swagger-ui-express`)
- **Authentication/Security**: JWT (assumed via standard practices) & Express Validator

## 📁 System Architecture

The ecosystem relies on several core entities:
* **Users**: Can top up their wallets and start streaming payments for services.
* **Stores**: Independent merchants offering continuous services via unique `Service` profiles.
* **Wallets & Ledgers**: Internal tracking mapping physical/fiat balances alongside Web3 EVM addresses logic for genuine crypto streams.
* **Stream Sessions**: The active real-time connection bridging the User's wallet, the Store's wallet, and the given Service's rate-per-second requirement.

For a deep dive into our Database schema, consult the [Schema Relations](./Schema_Relation.md).

## ⚡ Quick Start

### Prerequisites
- Node.js (v18+)
- MongoDB running locally or via MongoDB Atlas
- Web3 Provider / RPC (if testing Web3 Superfluid streams natively)

### Setup

```bash
# 1. Clone & Install dependencies
npm install

# 2. Configure environment
cp ENV_TEMPLATE.txt .env
# Open .env and insert your MongoDB URI, RPC URL, and JWT Secrets

# 3. Start MongoDB (if running locally)
mongod

# 4. Start the development server
npm run dev
```

Server defaults to `http://localhost:5000`

## 📚 API Endpoints Overview

The complete Swagger endpoint documentation can be explored by running the app and visiting **`/api/docs`**.

### Core Services

| Method | Endpoint                      | Description                    |
|--------|-------------------------------|--------------------------------|
| POST   | `/api/users/login`            | Authenticate a user            |
| POST   | `/api/stores/login`           | Authenticate a store merchant  |
| POST   | `/api/sessions/start`         | Start a streaming session      |
| POST   | `/api/sessions/:id/end`       | Terminate a streaming session  |
| POST   | `/api/sessions/:id/bill`      | Resolve billing / Ledger sync  |
| GET    | `/api/wallets/:id/balance`    | Fetch wallet balance details   |
| POST   | `/api/wallets/:id/topup`      | Load funds into a wallet       |

### Example: Starting a Stream 

To begin streaming money for a service (e.g., parking your car):
```bash
curl -X POST http://localhost:5000/api/sessions/start \
  -H "Content-Type: application/json" \
  -d '{
    "userId": "user_id_here",
    "userWalletId": "wallet_id_here",
    "serviceId": "service_id_here",
    "storeId": "store_id_here"
  }'
```
This triggers the engine to:
1. Verify the User has sufficient funds.
2. Initialize the `StreamSession` entity and mark it `ACTIVE`.
3. Auto-link the Store to the User's `storeIds` for historical tracking.
4. Execute the blockchain transactions to open a Superfluid stream from the Payer to the Receiver.

## 📖 Further Documentation

- **Superfluid Context**: Review [`README_SUPERFLUID.md`](./README_SUPERFLUID.md) for how Web3 streams are managed.
- **Database Schema**: Review [`Schema_Relation.md`](./Schema_Relation.md) for data entity relations.
- **Health Check**: Run a quick system diagnostic at `GET /health`

| Variable           | Description                     | Default                              |
|--------------------|---------------------------------|--------------------------------------|
| `PORT`             | Server port                     | `5000`                               |
| `NODE_ENV`         | Environment mode                | `development`                        |
| `MONGO_URI`        | MongoDB connection string       | `mongodb://localhost:27017/pulsepay` |
| `CORS_ORIGIN`      | Allowed CORS origins            | `*`                                  |
| `TEST_PRIVATE_KEY` | Test private key for blockchain | -                                    |

## License

MIT License
