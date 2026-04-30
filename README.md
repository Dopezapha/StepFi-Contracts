<div align="center">

![Stellar](https://img.shields.io/badge/Stellar-7D00FF?style=for-the-badge&logo=stellar&logoColor=white)
![Rust](https://img.shields.io/badge/Rust-000000?style=for-the-badge&logo=rust&logoColor=white)
![Soroban](https://img.shields.io/badge/Soroban-7D00FF?style=for-the-badge&logo=stellar&logoColor=white)

[![Open Source](https://img.shields.io/badge/Open%20Source-Yes-green?style=flat-square)](https://opensource.org/)
[![MIT License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](./LICENSE)

# StepFi Contracts

**Step into your future, pay small small.**

Soroban smart contracts powering the StepFi learner BNPL protocol on Stellar

[Contracts](#-contracts) • [Architecture](#-architecture) • [Quick Start](#-quick-start) • [Deployment](#-deployment) • [Contributing](#-contributing)

</div>

---

## 📖 About

StepFi Contracts is the on-chain layer of the StepFi protocol — a collection of Soroban smart contracts written in Rust that power decentralized learner financing on the Stellar network.

These contracts handle everything that must be trustless and transparent: loan creation, installment repayments, reputation scoring, liquidity pool management, and vendor verification.

---

## 📦 Contracts

| Contract | Description |
|---|---|
| `reputation-contract` | On-chain reputation score (0–100) per user. Drives credit limits and interest rates. |
| `creditline-contract` | Core BNPL engine — loan creation, per-installment repayment, defaults, late fees, grace periods. |
| `liquidity-pool-contract` | Share-based LP pool — deposits, withdrawals, loan funding, interest distribution (85/10/5 split). |
| `vendor-registry-contract` | Admin-managed whitelist of verified learning vendors (schools, bootcamps, electronics). |
| `parameters-contract` | Governance-tunable protocol parameters — interest BPS, grace periods, min reputation, etc. |

---

## 🏗 Architecture

```
StepFi-Contracts/
├── contracts/
│   ├── reputation-contract/       # On-chain reputation scoring
│   ├── creditline-contract/       # Loan lifecycle management
│   ├── liquidity-pool-contract/   # LP pool and interest distribution
│   ├── vendor-registry-contract/  # Learning vendor whitelist
│   └── parameters-contract/       # Protocol governance parameters
├── docs/
│   ├── architecture/              # Contract architecture docs
│   ├── development/               # Development standards
│   └── resources/                 # Stellar/Soroban references
├── scripts/
│   └── deploy-testnet.sh          # Testnet deployment script
├── CONTRIBUTING.md
├── ROADMAP.md
└── SECURITY.md
```

### Contract Interaction Flow

```
User Wallet
│
▼
creditline-contract  ──── calls ────▶  reputation-contract (validate score)
│                                          │
│                ◀─── score ───────────────┘
│
├── calls ──▶  vendor-registry-contract (validate vendor)
│
├── calls ──▶  liquidity-pool-contract (fund loan / receive repayment)
│
└── calls ──▶  parameters-contract (get protocol params)
```

### Reputation → Credit Tiers

| Score | Tier | Interest Rate | Credit Limit |
|---|---|---|---|
| 90+ | Gold | 4% | $10,000 |
| 75–89 | Silver | 6% | $5,000 |
| 60–74 | Bronze | 8% | $2,500 |
| < 60 | Starter | 10% | $1,000 |

---

## 🚀 Quick Start

### Prerequisites

- Rust (latest stable)
- Soroban CLI
- Stellar CLI
- A funded Stellar testnet account

### Install Rust & Soroban

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Add WASM target
rustup target add wasm32-unknown-unknown

# Install Stellar CLI
cargo install --locked stellar-cli --features opt
```

### Build All Contracts

```bash
# Clone the repository
git clone https://github.com/StepFi-app/StepFi-Contracts.git
cd StepFi-Contracts

# Build all contracts
cargo build --target wasm32-unknown-unknown --release
```

### Run Tests

```bash
# Run all tests
cargo test

# Run tests for a specific contract
cargo test -p reputation-contract
cargo test -p creditline-contract
cargo test -p liquidity-pool-contract
```

---

## 🚢 Deployment

### Testnet Deployment

```bash
# Run the deployment script
chmod +x scripts/deploy-testnet.sh
./scripts/deploy-testnet.sh
```

The script will output contract addresses — add them to StepFi-API `.env`:
```
REPUTATION_CONTRACT_ID=...
CREDIT_LINE_CONTRACT_ID=...
MERCHANT_REGISTRY_CONTRACT_ID=...
LIQUIDITY_POOL_CONTRACT_ID=...
```

### Initialization Order

Contracts must be initialized in this exact order:
1. `parameters-contract`
2. `reputation-contract`
3. `vendor-registry-contract`
4. `liquidity-pool-contract`
5. `creditline-contract`

Each contract needs the addresses of its dependencies passed to `initialize()`.

---

## 🔐 Security

- Reentrancy guards on all mutating functions
- Auth-first pattern — `require_auth()` before any state change
- Checked arithmetic — no overflow/underflow
- Role-based access — admin, updaters, creditline-only functions
- TTL management on all persistent storage entries

Report vulnerabilities via [SECURITY.md](./SECURITY.md).

---

## 🤝 Contributing

We welcome Rust and Soroban developers of all levels! See [CONTRIBUTING.md](./CONTRIBUTING.md) for setup, code style, and the PR process.

Check the [Roadmap](./ROADMAP.md) for open tasks and good first issues.

---

## 📄 License

MIT License — see [LICENSE](./LICENSE) for details.

---

<div align="center">

**Built with ❤️ for learners everywhere**

[![Stellar](https://img.shields.io/badge/Powered%20by-Stellar-7D00FF?style=flat-square)](https://www.stellar.org/)
[![Open Source](https://img.shields.io/badge/Open%20Source-Yes-green?style=flat-square)](https://opensource.org/)

</div>
