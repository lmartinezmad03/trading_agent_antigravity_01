# Master Implementation Plan: Freqtrade Decentralized Trading Bot

## Overview
An autonomous, unattended, non-KYC cryptocurrency futures trading system built on Freqtrade, targeting Hyperliquid DEX, featuring strict backtesting validation, dual-node infrastructure deployment, and exchange-native fail-safes.

---

## Phase 1: Dual-Node Environment & Infrastructure Baseline
- [ ] **1.1 Workspace Setup & Repository Structuring (Node A)**
  - Initialize local git repository with modular directory layout.
  - Create standard `.doc/` folder containing `SYSTEM_SPEC.md` and `MASTER_PLAN.md` for AI Agent context.
  - Set up local Python virtual environment and Freqtrade core on Node A (Windows 11).
- [ ] **1.2 Containerized Environment & Hardware Split (Node B)**
  - Create `docker-compose.yml` for multi-platform deployment (Windows 11 dev / Xubuntu server).
  - Deploy and verify base Freqtrade container on Node B (Xubuntu execution server).
- [ ] **1.3 Non-KYC Connectivity & Tor/VPN Privacy Verification**
  - Configure global VPN routing on Node A and Tor proxy routing on Node B.
  - Configure Hyperliquid exchange connector with EIP-712 Agent Wallet API signing (no personal identity disclosure).

---

## Phase 2: Historical Data Pipeline & Realistic Backtesting
- [ ] **2.1 Local Historical Candle Data Pipeline (Node A)**
  - Build automated scripts to download, validate, and cache local OHLCV datasets for BTC perpetual futures.
- [ ] **2.2 High-Realism Backtesting Engine Configuration**
  - Configure backtest parameters with next-bar open fills, realistic DEX maker/taker fees, and explicit slippage (0.05% - 0.1%).
  - Implement an automated Out-Of-Sample (OOS) data splitter (In-Sample train vs. Out-of-Sample validate) to detect overfitting.
- [ ] **2.3 Strategy 1 Development: Decoupled BTC Trend-Following**
  - Develop baseline strategy in Python adhering strictly to Freqtrade's decoupled structure (`populate_indicators`, `populate_entry_trend`, `populate_exit_trend`).
  - Run local backtests on Node A; verify zero look-ahead bias and audit performance metrics (Sharpe ratio, max drawdown).

---

## Phase 3: Mandatory Risk Management & Exchange-Native Fail-Safes
- [ ] **3.1 Exchange-Native Stop-Loss Configuration**
  - Enforce `stoploss_on_exchange: true` and order types (`entry: limit`, `stoploss: market`) inside `config.json` to prevent orphan positions during system outages.
- [ ] **3.2 Multi-Account & Portfolio Exposure Limits**
  - Configure strict position sizing, leverage caps (max open trades = 3), and automated emergency drawdown triggers.

---

## Phase 4: Dry-Running & Paper Trading Staging
- [ ] **4.1 Node B Deployment (Xubuntu Server)**
  - Deploy strategy to Node B running in Docker under `dry_run: true` mode.
  - Integrate Telegram bot notifications and local web UI for real-time monitoring behind Tor.
- [ ] **4.2 Execution & Signal Validation**
  - Run live dry-trading to audit websocket feed stability, order execution latency, and signal consistency against local backtest benchmarks.

---

## Phase 5: Live Execution & Human Review Protocol
- [ ] **5.1 Capital Allocation & Wallet Setup**
  - Fund self-custodial trading wallet with initial allocation (~0.03 BTC).
  - Perform mandatory human review checkpoint (Human Operator approval required before live deployment).
- [ ] **5.2 Live Trading Activation & Persistent Audit Logging**
  - Toggle live trading mode (`dry_run: false`) on Node B.
  - Enable persistent SQLite/JSON transaction logging for end-to-end trade auditability.