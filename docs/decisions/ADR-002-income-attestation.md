# ADR-002: Income attestation — TLSNotary (with Reclaim Protocol bridge)

**Status:** Accepted  
**Date:** 2026-03-14

## Context

Dotcommon's mutual credit pool requires income verification to underwrite credit limits. Traditional underwriting requires a trusted intermediary with access to private financial data. The cooperative requires a privacy-preserving alternative that does not expose member financial data to any third party, including Dotcommon itself.

Zero-knowledge proofs over financial data allow a member to prove claims about their income (e.g. "income exceeds $X/month for 12 consecutive months") without revealing the underlying data.

## Decision

Use TLSNotary as the long-term income attestation protocol, with Reclaim Protocol as a bridge during TLSNotary's production maturation period.

## Reasoning

**TLSNotary** is an open-source protocol developed by the Privacy & Scaling Explorations group at the Ethereum Foundation. It generates ZK proofs over TLS sessions, allowing claims about data from any HTTPS source (bank portals, tax platforms, payroll systems) without requiring API cooperation from the data source. It has no token, no commercial layer, and no entity that can change pricing or restrict usage. This makes it the correct long-term choice for an infrastructure layer that the cooperative's credit function depends on — a commercial dependency at the trust layer is architecturally unacceptable.

**Reclaim Protocol** has working SDKs, production deployments, and the broadest data source coverage of the commercial ZK attestation providers. It is used as a v1 bridge while TLSNotary matures. The architecture is designed to make this migration straightforward — the attestation layer is modular.

## Alternatives considered

**zkPass** — ZK over TLS sessions, more institutional backing than Reclaim, but commercial layer with unconfirmed pricing. More complex cryptographic assumptions (3P-TLS). Same commercial dependency problem as Reclaim.

**Social vouching (no ZK)** — Viable for v1 bootstrap with small credit limits. Sybil risk is real; credit ceilings stay low permanently without external income anchoring; no defence against coordinated collusion. Used as a fallback for early-stage members with no external income history, not as the primary attestation mechanism.

**Plaid + ZK layer** — Would use structured, verified bank data from Plaid as the input to a ZK proof. Simpler than TLS-based approaches but reintroduces Plaid as a trusted intermediary, partially defeating the purpose.

## Consequences

- v1 will use Reclaim Protocol with acknowledged commercial dependency risk; treat this as a time-limited dependency
- Migration path to TLSNotary must be preserved in the attestation layer architecture — do not hardcode Reclaim assumptions
- ZK proof generation imposes compute costs: $0.00–$0.30/proof (TLSNotary range), $0.05–$0.50/proof (Reclaim range)
- ZK on-chain verification gas: $0.08–$0.60 per proof submission
- Proofs are point-in-time snapshots — income could change the day after attestation; the credit model must account for this
- Social vouching remains available as a fallback for new members with no verifiable external income history, with lower credit limits
