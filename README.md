

# sBTC-CryptoLoot - Advanced Fungible Token Smart Contract

This contract implements an **enhanced fungible token (FT)** on the [Stacks blockchain](https://docs.stacks.co/), named **Stellar Token (STLR)**.
It extends the [SIP-010 Fungible Token Standard](https://github.com/stacksgov/sips/blob/main/sips/sip-010/sip-010-fungible-token-standard.md) with **advanced features, security checks, and governance integrations**.

The goal is to provide a **production-ready, secure, and extensible token contract** suitable for **DeFi protocols, DAOs, and advanced game economies**.

---

## ✨ Features

### 🔐 Security & Controls

* **Admin-controlled initialization** (one-time, with error protection).
* **Pause/unpause mechanism** to halt all token operations in emergencies.
* **Blacklist/restriction system** to block malicious addresses.
* **Self-transfer, zero-address, and overflow protections**.

### 💰 Token Operations

* **Minting** (owner only, capped by `MAX-SUPPLY`).
* **Burning** (planned for extension).
* **Transfers with recipient validation** (no restricted or invalid addresses).
* **Allowances with expiry timestamps** (improves security over traditional unlimited approvals).

### ⚖️ Governance Extensions

* **Voting power tracking** (balances contribute to governance weight).
* **Last vote height tracking** for accountability.
* **Locked tokens** with unlock height (useful for vesting, DAO timelocks, staking).

### 🛡️ Safety Enhancements

* **Custom error codes** for granular debugging.
* **Safe math functions** (`safe-add`) to prevent overflows.
* **Event emission** for mint, transfer, burn (print-based, chain-readable).

---

## 📦 Constants

| Constant     | Value                                       | Purpose               |
| ------------ | ------------------------------------------- | --------------------- |
| `MAX-SUPPLY` | `1_000_000_000_000_000` (1 trillion tokens) | Hard cap              |
| `PRECISION`  | `1_000_000`                                 | 6 decimal places      |
| `ERR-*`      | Custom error codes (u100–u113)              | Security + validation |

---

## 🗂️ Data Structures

* **Data Vars**

  * `contract-owner`: Admin address.
  * `token-name`, `token-symbol`, `token-decimals`: Token metadata.
  * `total-supply`: Current minted supply.
  * `paused`: Global pause switch.
  * `initialized`: Ensures contract can’t be re-initialized.

* **Maps**

  * `allowances`: `{ owner, spender } → { amount, expiry }`.
  * `locked-tokens`: `{ address } → { amount, unlock-height }`.
  * `governance-tokens`: `{ holder } → { voting-power, last-vote-height }`.
  * `restricted-addresses`: `{ address } → { blocked-height }`.
  * `minter-roles`: `{ address } → { can-mint, mint-allowance }`.

---

## 🛠️ Functions

### 🔎 Read-Only Queries

* `get-name` → token name.
* `get-symbol` → token symbol.
* `get-decimals` → decimals precision.
* `get-total-supply` → current total supply.
* `get-balance (account)` → STLR balance.
* `get-allowance (owner, spender)` → remaining allowance (0 if expired).
* `is-locked (address)` → whether account has locked tokens.

### ⚙️ Public Functions

#### **Initialization & Ownership**

* `initialize (name, symbol, decimals)` → one-time setup.
* `set-contract-owner (new-owner)` → transfer contract ownership.

#### **Emergency Controls**

* `pause` / `unpause` → toggles global pause switch.

#### **Token Lifecycle**

* `mint (amount, recipient)`

  * Only contract owner can mint.
  * Capped by `MAX-SUPPLY`.
  * Updates `total-supply` and recipient governance power.
* `burn` *(to be extended in next iterations)*.
* `transfer`, `transfer-from`, `approve` *(to be extended for SIP-010 full compliance)*.

#### **Governance Extensions**

* Voting power automatically updated when tokens are minted.
* Token locking via `locked-tokens` supports staking/vesting logic.

---

## 🚨 Error Codes

| Code   | Error                      | Description                                 |
| ------ | -------------------------- | ------------------------------------------- |
| `u100` | `ERR-NOT-AUTHORIZED`       | Caller not authorized.                      |
| `u101` | `ERR-INSUFFICIENT-BALANCE` | Balance too low.                            |
| `u102` | `ERR-INVALID-AMOUNT`       | Amount is invalid (0 or negative).          |
| `u103` | `ERR-INVALID-RECIPIENT`    | Recipient invalid (self, zero, restricted). |
| `u104` | `ERR-INVALID-SPENDER`      | Spender not valid.                          |
| `u105` | `ERR-OVERFLOW`             | Arithmetic overflow detected.               |
| `u106` | `ERR-PAUSED`               | Contract paused.                            |
| `u107` | `ERR-ALREADY-INITIALIZED`  | Initialization already completed.           |
| `u108` | `ERR-NOT-INITIALIZED`      | Contract not yet initialized.               |
| `u109` | `ERR-BLACKLISTED`          | Address is restricted.                      |
| `u110` | `ERR-MAX-SUPPLY-REACHED`   | Minting cap exceeded.                       |
| `u111` | `ERR-ZERO-ADDRESS`         | Invalid zero address.                       |
| `u112` | `ERR-SELF-TRANSFER`        | Sender and recipient are same.              |
| `u113` | `ERR-EXPIRED-ALLOWANCE`    | Allowance expired.                          |

---

## 📖 Example Usage

### Deploy Contract

```bash
clarinet contract deploy contracts/stellar-token.clar
```

### Initialize Token

```clarity
(contract-call? .stellar-token initialize "Stellar Token" "STLR" u6)
```

### Mint Tokens

```clarity
(contract-call? .stellar-token mint u1000000 'ST1234...)
```

### Query Balance

```clarity
(contract-call? .stellar-token get-balance 'ST1234...)
```

### Pause Contract

```clarity
(contract-call? .stellar-token pause)
```

---

## 🧪 Testing with Clarinet

Run all tests:

```bash
clarinet test
```

Run a specific function in console:

```bash
clarinet console
```

```clarity
(contract-call? .stellar-token get-total-supply)
```

---
