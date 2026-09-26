# **SYSTEM ARCHITECTURE & SPECIFICATION PROMPT: ANTIGRAVITY TRADING AGENT**

---

**Project Name:** Trading \- Antigravity \- Agent 01  
**Target Audience:** AI Code Agents (Antigravity / VSCode Agent Mode)

## **1\. Overall Project Objective**

To architect, develop, test, and deploy an autonomous, unattended, non-KYC crypto perpetual futures trading system built on **Freqtrade**, targeting decentralized exchanges (DEXs)—specifically **Hyperliquid**. The system must maximize operational anonymity, implement strict exchange-native fail-safes, run backtests with rigorous look-ahead bias protections, and support a distributed local deployment model across dual-hardware platforms.

## **2\. Core Architectural & System Stack**

* **Core Engine & Framework:** Freqtrade (Python-based). All strategy logic, indicators, signal generation, data pipelines, and backtesting must conform natively to Freqtrade interfaces.  
* **Primary DEX Target:** Hyperliquid (Layer-1 matching engine) via CCXT / Freqtrade exchange connector or native EIP-712 Agent Wallet signing.  
* **Trading Paradigm:** Non-HFT, candle-based systematic day trading for crypto perpetual futures. Both long and short positions allowed. Initial pairs focused on BTC, with future scaling to alts/indices.  
* **Initial Operational Capital:** \~0.03 BTC (\~3,000,000 sats) allocated to self-custodial trading wallet(s).  
* **Compliance & Identity:** Strictly 100% KYC-free, self-custody wallet infrastructure. No centralized exchanges (CEXs) requiring personal verification.

## **3\. Security, Anonymity & Infrastructure Distribution**

The system spans two local machines with distinct operational responsibilities to maintain high availability and security:

| Hardware Node | Specifications & OS | Operational Role | Network & Privacy Controls   |
| :---- | :---- | :---- | :---- |
| **Node A (Development Workstation)** | Intel i7-1185G7, 16GB RAM, 1TB SSD (Windows 11 Pro) | Strategy development, candle data acquisition/caching, heavy local backtesting, hyperopt, and manual review. | Global VPN masking via hide.me. |
| **Node B (Execution Server)** | Intel i3-6006U, 12GB RAM, 1TB HDD \+ 2TB External SSD (Xubuntu) | 24/7 unattended dry-running and live execution inside containerized environments (Docker Compose). | Tor hidden services & isolated proxy routing for outgoing trade execution calls. |

* **Portability:** All components must be dockerized via docker-compose.yml for rapid portability between Node A and Node B.  
* **Financial Expenditure Guardrail:** Zero-cost tier services must be preferred. Any decision incurring recurring financial cost must be flagged for explicit human authorization.

## **4\. Mandatory Fail-Safe & Safety Architecture**

### **4.1 Failure-Mode Threat Model**

In unattended operation, local network drops, API rate-limit lockouts, container crashes, or hardware power outages must never leave an unmonitored "orphan position" exposed to market risk on the DEX order book.

### **4.2 Exchange-Native Order Enforcement**

Software-only / polling-based stop-loss mechanisms are strictly prohibited for live trading. Every order execution confirmed by Freqtrade must trigger an exchange-native Stop-Loss order placed immediately on Hyperliquid's order book.

### **4.3 Mandatory Freqtrade Configuration Blueprint**

The strategy and config.json must enforce the following order parameters:

`{`  
  `"order_types": {`  
    `"entry": "limit",`  
    `"exit": "limit",`  
    `"emergency_exit": "market",`  
    `"force_exit": "market",`  
    `"force_entry": "limit",`  
    `"stoploss": "market",`  
    `"stoploss_on_exchange": true,`  
    `"stoploss_on_exchange_interval": 60,`  
    `"stoploss_price_type": "mark"`  
  `},`  
  `"position_adjustment_enable": false,`  
  `"max_open_trades": 3`  
`}`

## **5\. Backtesting, Data Integrity & Validation Standards**

To eliminate overfitting, look-ahead bias, and unrealistic expected returns, all proposed strategies must pass a multi-stage validation pipeline prior to deployment:

1. **Decoupled Logic Architecture:** populate\_indicators(), populate\_entry\_trend(), and populate\_exit\_trend() must be cleanly decoupled. Indicators cannot access future candle values or unclosed bar data.  
2. **Realistic Execution Emulation:** Backtest settings must enforce next-bar open fills, realistic DEX taker/maker fee structures, explicit slippage allowances (minimum 0.05% \- 0.1%), and funding rate deductions.  
3. **Out-Of-Sample (OOS) Split Testing:** Strategies must be trained/optimized on Period A (In-Sample) and evaluated on Period B (Out-of-Sample). Performance degrading \>25% on OOS invalidates the strategy.  
4. **Dual-Engine Cross Validation:** Promising local Freqtrade backtests must be verified against secondary charting/validation benchmarks (e.g., TradingView or custom Python bar-by-bar emulators) to catch candle intrabar assumptions.  
5. **Full Audit Traceability:** All trade signals, backtest runs, and execution logs must be persistently stored (SQLite/JSON logs) for end-to-end auditability.

## **6\. Human-In-The-Loop & Operational Lifecycle**

1. **Strategy Proposal Phase:** Code Agent generates or updates strategy logic in Python using Freqtrade paradigms.  
2. **Local Validation Phase:** Backtest and hyperopt execution on Node A (Windows 11\) using cached historical candle data.  
3. **Human Review Checkpoint:** Human operator (Laura) reviews performance metrics (Win Rate, Sharpe Ratio, Max Drawdown, Profit Factor) and explicitly authorizes deployment.  
4. **Dry-Run Staging:** Deploy strategy on Node B (Xubuntu Docker) in dry\_run: true mode to validate live websocket feed handling, latency, and signal consistency against live market prices.  
5. **Live Deployment:** Switch to live trading with allocated real capital (\~0.03 BTC) only after successful dry-run staging.