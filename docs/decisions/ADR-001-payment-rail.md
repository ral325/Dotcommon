# ADR-001: Payment rail — DAI on Optimism

**Status:** Accepted  
**Date:** 2026-03-14

## Context

Dotcommon requires a payment rail for all worker payments, credit draws, and repayments. The choice of rail determines transaction costs, censorship resistance, commercial dependency risk, and accessibility to non-crypto users.

## Decision

Use DAI on Optimism as the primary payment rail.

## Reasoning

DAI is a decentralised stablecoin issued by the MakerDAO protocol, backed by overcollateralised crypto assets rather than dollars held in a corporate bank account. Unlike USDC (issued by Circle), no single entity can freeze a DAI balance or blacklist an address. This censorship resistance is directly aligned with Dotcommon's mission to build infrastructure that cannot be captured or shut down by corporate or regulatory pressure.

Optimism is an Ethereum L2 with gas costs of ~$0.01–$0.06 per transaction, making per-payment costs negligible relative to traditional payment processing (2.9% + $0.30 via Stripe).

## Alternatives considered

**USDC on Base** — Lower gas, high liquidity, but Circle is a US-incorporated company that can freeze addresses, change terms, or be acquired. Commercial dependency risk is unacceptable for the cooperative's core payment infrastructure.

**USDC on Arbitrum** — Slightly higher fees than Base, more decentralised validator set than Base, but same Circle dependency problem.

**Fiat via ACH/Stripe** — Zero crypto friction but reintroduces 2.9% payment processing fee, eliminating the key cost advantage over the traditional stack. Full corporate dependency. Appropriate only as an on/off ramp at the edges, not as the core rail.

## Consequences

- Fiat on/off ramp infrastructure (Privy + Stripe) is required to maintain dollar-in dollar-out UX — see ADR-005
- Members who are comfortable holding DAI natively can bypass ramp costs entirely
- DAI's peg stability depends on MakerDAO governance and collateral health — a systemic DeFi event could affect the peg temporarily
- Optimism sequencer is currently centralised; decentralisation is in progress but is a residual risk
- The 3% Colony network fee applies on top of all expenditure payouts through Colony — see ADR-003
