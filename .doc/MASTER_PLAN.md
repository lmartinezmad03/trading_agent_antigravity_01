# Master Implementation Plan: Freqtrade Decentralized Trading Bot

## Overview

An autonomous, unattended, non-KYC cryptocurrency futures trading system built on Freqtrade, targeting Hyperliquid DEX, featuring strict backtesting validation, dual-node infrastructure deployment, host service co-existence protection, and exchange-native fail-safes.

---

## Phase 1: Dual-Node Environment & Infrastructure Baseline

- [ ] **1.1 Workspace Setup & Repository Structuring (Node A)**
  - Initialize local git repository with modular directory layout.
  - Create standard `.doc/` folder containing `SYSTEM_SPEC.md`, `MASTER_PLAN.md`, and `DECISIONS.md` for AI Agent context.
  - Set up local Python virtual environment and Freqtrade core on Node A (Windows 11).

- [ ] **1.2 Containerized Environment & Resource Bounding (Node B)**
  - Create `docker-compose.yml` for multi-platform deployment (Windows 11 dev / Xubuntu server).
  - Configure Docker `cgroups` / resource limits (`mem_limit`, `cpus`) inside `docker-compose.yml` to strictly bound Freqtrade container usage (~250–500MB RAM max).
  - Deploy and verify base Freqtrade container on Node B (Xubuntu execution server) without affecting host processes.

- [ ] **1.3 Host Service Audit & Security Verification (Node B)**
  - Audit running host services (`bitcoind`, `lnd`, `monero`, `electrs`, `specter`, `tor`) and establish baseline resource/uptime metrics.
  - Verify Tor proxy routing and enforce kill-switch safeguards to prevent DNS/IP leaks or browser/client fingerprinting.

- [ ] **1.4 System Backup & Rollback Engine (Node B)**
  - Implement automated pre-configuration system state snapshot routines (Timeshift / config tarball backups) on Node B.
  - Create a single-command `rollback.sh` script for rapid recovery in case of failed infrastructure updates.

- [ ] **1.5 Centralized Context & Logging Ledger**
  - Establish `DECISIONS.md` in the repository root to log all architectural choices, configuration updates, past mistakes, and human/AI edits.

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
  - Configure strict position sizing, leverage caps (`max_open_trades = 3`), and automated emergency drawdown triggers.

---

## Phase 4: Dry-Running, Health Staging & Dashboard Deployment

- [ ] **4.1 Node B Deployment & Monitoring Setup**
  - Deploy strategy to Node B running in Docker under `dry_run: true` mode.
  - Deploy lightweight monitoring dashboard (Uptime Kuma / Netdata) via Docker on Node B to monitor health of `bitcoind`, `lnd`, `monero`, `electrs`, `specter`, `tor`, and trading containers.
  - Configure Telegram / Matrix webhook alerts for service drops, high CPU/RAM pressure (>85%), or IP leak events.

- [ ] **4.2 Execution & Signal Validation**
  - Run live dry-trading to audit websocket feed stability, order execution latency, and signal consistency against local backtest benchmarks.

---

## Phase 5: Live Execution & Operational Lifecycle

- [ ] **5.1 Capital Allocation & Wallet Setup**
  - Fund self-custodial trading wallet with initial allocation (~0.03 BTC).
  - Perform mandatory human review checkpoint (Human Operator approval required before live deployment).

- [ ] **5.2 Live Trading Activation & Persistent Audit Logging**
  - Toggle live trading mode (`dry_run: false`) on Node B.
  - Enable persistent SQLite/JSON transaction logging for end-to-end trade auditability.

---

## Phase 6: Scalability, Resource Auditing & Profit Optimization

- [ ] **6.1 System Resource Evaluation & Upgrade Scaling**
  - Monitor resource headroom on Node B during combined execution of crypto services and trading containers.
  - Submit formal hardware/architecture expansion proposals if resource bottlenecks threaten host system stability.

- [ ] **6.2 Complementary Monetization Opportunities**
  - Explore LND routing liquidity management (using tools like `bos` / Balance of Satoshis) to generate passive routing fees on Node B.
  - Research low-risk Hyperliquid funding rate arbitrage or liquidity vault strategies for auxiliary yield generation.