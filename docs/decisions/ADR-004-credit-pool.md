# ADR-004: Mutual credit pool — Custom on-chain pool

**Status:** Accepted (v2 target; off-chain ledger for v1)  
**Date:** 2026-03-14

## Context

Dotcommon's core financial product is a mutual credit pool that extends low-cost bridge loans to members against their verified income history. The pool must be member-governed, trustless, and capable of implementing custom underwriting logic tied to the cooperative's income attestation data.

## Decision

Build a custom audited smart contract credit pool for v2, governed by Colony's reputation-weighted voting for parameter changes. Use an off-chain ledger for v1 to prove the lending model before incurring audit costs.

## Reasoning

A custom pool gives the cooperative full control over underwriting logic, interest rate components, default handling, and pool governance. This is essential because the pool's behaviour is the financial heart of Dotcommon — it must implement the three-component rate model (base rate + risk premium + health adjustment) and the income routing on default, neither of which exist in any off-the-shelf protocol.

The v1 off-chain ledger is explicitly a trust-dependent temporary measure, not an end state. It proves the model cheaply before committing $20k–$80k to a smart contract audit.

**The three-component credit rate model:**
- **Base rate** (~1.5%): Covers pool operating costs and liquidity overhead. Does not vary with membership.
- **Risk premium** (starts ~5% at 100 members, decays toward 0 as membership grows): Models the improving information advantage as the network's income dataset matures. Scale-down rate is a governance parameter.
- **Pool health adjustment** (0–3%): Governance-set buffer that activates when default rates exceed thresholds.
- **Effective rate** = base rate + (risk premium × scale factor) + health adjustment

## Alternatives considered

**Aave v3 fork** — Battle-tested codebase, lower initial audit scope than a fully custom pool. However: any modification to integrate Colony governance for parameter changes invalidates the existing audits; Aave v3 uses BSL licensing with commercial use restrictions; the lending logic is designed for collateralised DeFi lending, not unsecured income-backed mutual credit. The audit cost advantage is smaller than it appears once modification scope is accounted for.

**Maple Finance** — Institutional on-chain credit pools designed for real-world borrowers. Higher fees (0.5–1% origination + 15% of interest as protocol fee), corporate operator dependency, and limited governance customisation. Not appropriate for a member-owned cooperative.

**Off-chain ledger (v1)** — No smart contract risk or audit cost, but not trustless — requires member trust in the operator. Appropriate for v1 only. Must be replaced with on-chain infrastructure as the cooperative grows.

## Consequences

- Smart contract audit budget: $20k–$80k one-time, amortised over 5 years in the cost model
- Audit is required even for modified Aave forks — this eliminates the cost advantage of forking
- Pool solvency depends on: default rate, recovery rate, recovery lag, and effective interest rate. Model assumes 3% annual default rate, 55% recovery over 2 years as baseline
- Minimum rate for pool solvency at baseline assumptions: ~2.8–3.2% — well within target range of 3–7%
- Pool governance: all parameter changes (interest rates, credit limits, risk premium scale-down) require Colony reputation-weighted votes in the credit domain
- Default handling mechanism: automatic income routing (a smart contract claim on incoming payments that routes a repayment percentage before funds reach the member's wallet), peer workout committees for larger defaults, Kleros integration for disputed defaults
