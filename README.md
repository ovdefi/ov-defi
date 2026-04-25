
# 🏦 Ov Defi - Featured App
**Canton Network Tokenomics Committee**

---

## 📋 Agenda

1. Company Overview
2. Platform Overview
3. Technical Architecture
4. Use Cases & Transactions
5. Security & Compliance
6. Featured App Impact
7. Roadmap

---

## 🏢 Company Overview

### OneNov

| Detail | Value |
|--------|-------|
| **Name** | OneNov |
| **Website** | https://onenov.xyz |
| **Contact** | ov@onenov.xyz |
| **Validator** | `onenov-validator-1::122004c8b00540481384634910608b21774b9cc36030cd9df981579d8c5501a25c25` |

**Background:**
- Active Canton MainNet Validator since **March 2026**
- Running infrastructure across **22+ networks**
- **99.9% uptime** since 2022
- Built and deployed multi-user smart contracts on MainNet

---

## 🌐 Platform Overview

### Ov Defi - Premier DeFi on Canton Network

| Feature | Status | Description |
|---------|--------|-------------|
| 🔐 **Self-Custodial Wallet** | ✅ Live | Email/password with encrypted private key |
| 🥩 **Staking** | ✅ Ready | Delegate CC to validators, earn real-time rewards |
| 🏦 **Lending** | ✅ Ready | Supply CC to earn interest, borrow with collateral |
| 🪙 **Token Factory** | ✅ Ready | Deploy custom tokens with logo (CIP-0056) |
| 💰 **Transfer CC** | ⏳ Pending FA | Peer-to-peer CC transfers |
| 📊 **Validator Explorer** | ✅ Live | 1,000+ validators indexed |
| 🎁 **Airdrop Program** | ✅ Ready | 1,000+ waitlist users, leaderboard system |

**Product URL:** https://ovdefi.onenov.xyz

---

## 🛠️ Technical Architecture

```

┌─────────────────────────────────────────────────────────────┐
│                    Ov Defi Frontend (Vercel)                │
│                   https://ovdefi.onenov.xyz                 │
└─────────────────────────┬───────────────────────────────────┘
│ HTTPS
▼
┌─────────────────────────────────────────────────────────────┐
│              Backend API (Node.js + Express)                │
│              https://canton-api.onenov.xyz                  │
│  • Auth (JWT)  • Balance  • Staking  • Lending  • Token     │
│  • Rate Limiting  • Transaction Limits  • Captcha           │
└─────────────┬───────────────────────────────┬───────────────┘
│                                │
│ gRPC                           │ SQL
▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────────┐
│   Canton Network Node   │     │      PostgreSQL Database    │
│   (Splice Validator)    │     │    (144.91.107.167:5432)    │
│   • Ledger API          │     │   • Users  • UTXOs  • TXs   │
│   • Party Management    │     │   • Staking  • Lending      │
│   • gRPC Port 5001      │     │   • tx_logs (limits)        │
└─────────────────────────┘     └────────────────────────────

```

---

## 📦 Smart Contracts (On-Chain)

| Contract | Package ID | Contract ID | Status |
|----------|-----------|-------------|--------|
| **StakingPool** | `b4a78bdb74cc03d604ba233b142dfac5c7387f7aa9af90f1de43d234500a2fc2` | `001f37d008eefb15c4b5dbc70f8b50990cf1b3338f89355946f0223bfb8b4ad2c8ca1212205eccaee45bdb3df9847308dddba10fa7f58dffd9b97d066aa7736ec986095367` | ✅ Deployed |
| **LendingPool** | `b4a78bdb74cc03d604ba233b142dfac5c7387f7aa9af90f1de43d234500a2fc2` | `0046e4b2594b9ddbd0645cde4d1d3af598fe2ed781458e1a7ae26c0d5c05a6623cca121220776dbd7c98d65e636677bd19a0a172c190e8775355cfdd1bed66a42c08f36580` | ✅ Deployed |
| **TokenFactory** | `c78a00ab7486ffe8986a245f1fdb94b7bc418961d73aefccf091219a05269dd8` | `008cb54f727bffef305496cf6a7fc960fb8367a3a60b911f3bcd0bfbce75cfbedeca121220146a9a406ce357b9beb01bfb4e6fb0ae71c385325a225d743eb266aeb64a8f4c` | ✅ Deployed (v2 with logo) |

**Design:** Multi-user using **observer pattern**
- `signatory`: operator (`onenov-validator-1::1220...`)
- `observer`: dynamic list of users
- Users can be added/removed via `Staking_AddObserver` / `Lending_AddObserver` choice

**Choices:**

| Contract | Choice | Description |
|----------|--------|-------------|
| **StakingPool** | `Stake` | User stakes CC to a validator |
| | `Unstake` | User unstakes CC after lock period (10,080 rounds) |
| | `ClaimRewards` | User claims accumulated rewards |
| | `Staking_SetPaused` | Admin: pause/unpause pool |
| | `Staking_AddObserver` | Admin: grant user access |
| **LendingPool** | `Supply` | User supplies CC to earn interest |
| | `Withdraw` | User withdraws supplied CC + interest |
| | `Borrow` | User borrows CC with 150% collateral |
| | `Repay` | User repays borrowed CC + interest |
| | `Liquidate` | Admin: liquidate unhealthy position |
| | `Lending_SetPaused` | Admin: pause/unpause pool |
| | `Lending_AddObserver` | Admin: grant user access |
| **TokenFactory** | `CreateToken` | User deploys custom token with name, symbol, logo URL |
| | `Factory_SetPaused` | Admin: pause/unpause factory |

**Parameters:**

| Parameter | StakingPool | LendingPool |
|-----------|-------------|-------------|
| APY | 5% | - |
| Lock Period | 10,080 rounds (~7 days) | - |
| Min Stake | 0.000001 CC | - |
| Max Stake | 100,000 CC | - |
| Base Interest Rate | - | 3% |
| Collateral Ratio | - | 150% |
| Liquidation Threshold | - | 120% |
| Optimal Utilization | - | 80% |
| Max Interest Rate | - | 30% |
---

## 📊 Use Cases & Transaction Estimates

### 1. 🥩 Staking

| Metric | Value |
|--------|-------|
| Daily TX per user | 1-3 (stake, claim, unstake) |
| Multi-TX per round | Auto-compound triggers claim hourly |
| Scaling | **Linear** - independent positions |
| Controls | Min 0.000001 CC, max 100,000 CC, 20 tx/day |

**Contract:** `Contracts:StakingPool`  
**Choice:** `Stake`, `Unstake`, `ClaimRewards`

### 2. 🏦 Lending (Supply & Borrow)

| Metric | Value |
|--------|-------|
| Daily TX per user | 2-5 (supply, withdraw, borrow, repay) |
| Multi-TX per round | Position adjustments during volatility |
| Scaling | **Linear** - independent positions |
| Controls | Min 5-10 CC, max 50-100 CC, 150% collateral |

**Contract:** `Contracts:LendingPool`  
**Choice:** `Supply`, `Withdraw`, `Borrow`, `Repay`

### 3. 🪙 Token Factory

| Metric | Value |
|--------|-------|
| Daily TX per user | 0.1 (occasional deployment) |
| Multi-TX per round | Rare - 1 deployment per 24h limit |
| Scaling | **Linear** - independent deployments |
| Controls | 1 deployment/day, 3-5 char symbol |

**Contract:** `Contracts:TokenFactory`  
**Choice:** `CreateToken`

### 4. 💰 Transfer CC

| Metric | Value |
|--------|-------|
| Daily TX per user | 1-5 (peer-to-peer) |
| Multi-TX per round | Multiple transfers allowed |
| Scaling | **Linear** - independent transfers |
| Controls | Min 1 CC, max 500 CC, 50 tx/day |

**Contract:** `Splice.Amulet:Amulet`  
**Choice:** `Transfer`

---

## 🔒 Security & Compliance

| Layer | Implementation |
|-------|---------------|
| **Authentication** | JWT tokens (7-day expiry) |
| **Rate Limiting** | 3x register/day/IP, 5 login failures = 15min lock |
| **Transaction Limits** | Per-tx min/max, daily caps, cooldowns |
| **UTXO Protection** | `SELECT ... FOR UPDATE` + transaction wrapper |
| **Anti-Sybil** | Disposable email block, waitlist invite system |
| **Captcha** | Cloudflare Turnstile on registration |
| **HTTPS** | TLS 1.2+ with valid certificate |
| **CORS** | Restricted to trusted origins |
| **CIP Compliance** | CIP-0056 (Token Standard), CIP-0104 (Traffic Rewards) |

---

## 🔴 Critical: Why FA Status Is Essential

### Current State (WITHOUT FA):

| Feature | Status | Issue |
|---------|--------|-------|
| Transfer CC | ❌ Blocked | `AmuletTransferInstruction` stuck in pending |
| Staking | ❌ Blocked | Contract not accessible by users |
| Lending | ❌ Blocked | Contract not accessible by users |
| Token Factory | ❌ Blocked | Contract not accessible by users |

### Evidence:

```
Transfer from CantonLoop to user onenovfi-mainnet-2:

· Amount: 2 CC
· Status: PENDING (over 1 hour)
· Reason: DSO cannot settle without FA status
```
· Lighthouse: [https://lighthouse.cantonloop.com/transactions/12203a4d...](https://lighthouse.cantonloop.com/transactions/12203a4d4d3dd859a0cfe4fa027619df54bdb3934d5cd5778a4398385c4011d7796b)

### Expected State (WITH FA):

| Feature | Status |
|---------|--------|
| Transfer CC | ✅ Instant settlement |
| Staking | ✅ Fully functional |
| Lending | ✅ Fully functional |
| Token Factory | ✅ Fully functional |

---

## 📈 Traffic & Scaling

| Phase | Users | Daily TX | Notes |
|-------|-------|----------|-------|
| **Launch** | 100-500 | 500-2,500 | Waitlist users |
| **Month 1** | 500-1,000 | 2,500-5,000 | Organic growth |
| **Month 3** | 1,000-5,000 | 5,000-25,000 | Marketing push |
| **Month 6** | 5,000-10,000 | 25,000-50,000 | Ecosystem adoption |

**Scaling Model:** Linear - each user generates independent transactions

---

## 🗓️ Roadmap

| Timeline | Milestone |
|----------|-----------|
| **May 2026** | ✅ FA Approval - Full platform launch |
| **May 2026** | 1,000+ waitlist users onboarded |
| **Jun 2026** | Complete security audit |
| **Jul 2026** | Open-source smart contracts |
| **Q3 2026** | Cross-chain bridge (EVM ↔ Canton) |
| **Q4 2026** | Mobile app (iOS/Android) |

---

## 👥 First Customers

- **1,000+ waitlist users** already collected
- Existing Canton ecosystem participants
- Validators wanting to attract delegators

---

## 📊 Reporting Commitment

- ✅ **Monthly report** on transaction activity
- ✅ **Quarterly review** with Tokenomics Committee
- ✅ **Per use-case breakdown** of transactions
- ✅ **Customer growth metrics**

---

## 🙏 Request

**Ov Defi is ready for production.** All smart contracts are deployed. The frontend and backend are live. 1,000+ users are waiting.

**What we need:** Featured App status to enable DSO settlement and unlock the full platform.

**Party ID:** `onenov-validator-1::122004c8b00540481384634910608b21774b9cc36030cd9df981579d8c5501a25c25`

---

## 📞 Contact

| Channel | Detail |
|---------|--------|
| **Email** | ov@onenov.xyz |
| **Website** | https://onenov.xyz |
| **Product** | https://ovdefi.onenov.xyz |
| **GitHub** | https://github.com/OneNov0209/canton-stake |

---

> **"Ov Defi brings institutional-grade DeFi to Canton Network. With FA status, we can unlock real on-chain transactions for 1,000+ users and drive significant traffic through our validator."**

---

*Prepared for Canton Foundation Tokenomics Committee - April 2026*
