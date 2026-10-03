# SYSTEM ARCHITECTURE & SPECIFICATION PROMPT: ANTIGRAVITY TRADING AGENT

**Project Name:** Trading - Antigravity - Agent 01  
**Target Audience:** AI Code Agents (Antigravity / VSCode Agent Mode)

---

## 1. Overall Project Objective

To architect, develop, test, and deploy an autonomous, unattended, non-KYC crypto perpetual futures trading system built on **Freqtrade**, targeting decentralized exchanges (DEXs)—specifically **Hyperliquid**. The system must maximize operational anonymity, implement strict exchange-native fail-safes, run backtests with rigorous look-ahead bias protections, guarantee host service co-existence safety on Node B, and support a distributed local deployment model across dual-hardware platforms.

---

## 2. Core Architectural & System Stack

* **Core Engine & Framework:** Freqtrade (Python-based). All strategy logic, indicators, signal generation, data pipelines, and backtesting must conform natively to Freqtrade interfaces.
* **Primary DEX Target:** Hyperliquid (Layer-1 matching engine) via CCXT / Freqtrade exchange connector or native EIP-712 Agent Wallet signing.
* **Trading Paradigm:** Non-HFT, candle-based systematic day trading for crypto perpetual futures. Both long and short positions allowed. Initial pairs focused on BTC, with future scaling to alts/indices.
* **Initial Operational Capital:** ~0.03 BTC (~3,000,000 sats) allocated to self-custodial trading wallet(s).
* **Compliance & Identity:** Strictly 100% KYC-free, self-custody wallet infrastructure. No centralized exchanges (CEXs) requiring personal verification.

---

## 3. Security, Anonymity & Infrastructure Distribution

The system spans two local machines with distinct operational responsibilities to maintain high availability and security:

| Hardware Node | Specifications & OS | Operational Role | Network, Privacy & Safety Controls |
| :--- | :--- | :--- | :--- |
| **Node A (Development Workstation)** | Intel i7-1185G7, 16GB RAM, 1TB SSD (Windows 11 Pro) | Strategy development, candle data acquisition/caching, heavy local backtesting, hyperopt, OOS validation, and manual review. | Global VPN masking via hide.me. DNS/IP leak protection. |
| **Node B (Execution Server)** | Intel i3-6006U, 12GB RAM, 1TB HDD + 2TB External SSD (Xubuntu) | 24/7 unattended dry-running, live execution inside containerized environments (Docker Compose), host process protection, monitoring dashboard. | Tor hidden services & isolated proxy routing. Strict leak protection, anti-fingerprinting, kill-switches. |

### 3.1 Node B Production Safety & Host Co-existence
Node B is a working, production-critical machine running sensitive core crypto services and privacy daemons that **must never be broken or interrupted**:
* **Protected Host Services:** `bitcoind`, `lnd`, `monero`, `electrs`, `specter`, and `tor`.
* **Container Isolation & Cgroup Limits:** All trading services on Node B must run isolated inside Docker containers. Resource limits (`cpus`, `memory` capped at ~250–500MB) must be explicitly enforced in `docker-compose.yml` to prevent CPU/RAM starvation of host crypto daemons.
* **Resource Separation:** All computationally heavy tasks (hyperopt, historical data downloads, heavy backtesting) are strictly prohibited on Node B and restricted to Node A.

### 3.2 Tor & Privacy Preservation
* Tor must remain up, running, and uncompromised at all times.
* Proxy routing must guarantee **zero DNS/IP leaks** and **prevent system fingerprinting**.
* Any new changes to security/privacy routing must be non-disruptive, validated, and pre-tested before applying.

---

## 4. Mandatory Fail-Safe & Safety Architecture

### 4.1 Failure-Mode Threat Model
In unattended operation, local network drops, API rate-limit lockouts, container crashes, or hardware power outages must never leave an unmonitored "orphan position" exposed to market risk on the DEX order book.

### 4.2 Exchange-Native Order Enforcement
Software-only / polling-based stop-loss mechanisms are strictly prohibited for live trading. Every order execution confirmed by Freqtrade must trigger an exchange-native Stop-Loss order placed immediately on Hyperliquid's order book.

### 4.3 Mandatory Freqtrade Configuration Blueprint
```json
{
  "order_types": {
    "entry": "limit",
    "exit": "limit",
    "emergency_exit": "market",
    "force_exit": "market",
    "force_entry": "limit",
    "stoploss": "market",
    "stoploss_on_exchange": true,
    "stoploss_on_exchange_interval": 60,
    "stoploss_price_type": "mark"
  },
  "position_adjustment_enable": false,
  "max_open_trades": 3
}