# DECISIONS & CHANGE LOG

This document tracks all human operator and AI agent decisions, infrastructure updates, configuration changes, and operational incidents.

---

## [2026-10-04] - System Baseline Adoption v1.0

### Author
Human Operator & AI Code Agent

### Changes Applied
* Initialized baseline specification documents in `.doc/`:
  * `SYSTEM_SPEC.md` (v1.0) - System Architecture & Requirements
  * `MASTER_PLAN.md` (v1.0) - Implementation Roadmap
* Established project directory structure and `.doc/` tracking workflow.

### Rationale & Premises
* Formalized dual-node architecture (Node A for backtesting/dev, Node B for execution server).
* Established strict protection rules for Node B host services (`bitcoind`, `lnd`, `monero`, `electrs`, `specter`, `tor`).
* Enforced exchange-native fail-safes (`stoploss_on_exchange: true`) and non-KYC DEX execution via Hyperliquid.

### Verification & Testing Status
* Documentation verified. Infrastructure implementation pending Phase 1 execution.