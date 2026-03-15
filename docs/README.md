# Dotcommon — Project Documentation

**Dotcommon** (Community Owned Money, Markets, and Open Networks) is a worker-owned cooperative financial platform providing mutual credit access and eventually cooperative marketplace infrastructure to independent workers and small businesses.

---

## Documentation structure

| Folder | Contents |
|---|---|
| `decisions/` | Architectural Decision Records (ADRs) — significant choices with reasoning and alternatives |
| `design/` | Product and governance design decisions |
| `open-questions/` | Unresolved questions requiring future investigation or decision |
| `caveats/` | Known limitations, risks, and important qualifications |

---

## ADR format

Each decision record follows this structure:

```
# ADR-NNN: Title

**Status:** proposed / accepted / deprecated / superseded  
**Date:** YYYY-MM-DD  

## Context
Why this decision needed to be made.

## Decision
What was decided.

## Reasoning
Why this option was chosen over alternatives.

## Alternatives considered
What else was evaluated and why it was not chosen.

## Consequences
What this decision implies — costs, constraints, follow-on decisions required.
```

---

## Key artifacts

- `../Dotcommon_Business_Plan_v0.docx` — full business plan
- `../Colony_whitepaper.pdf` — Colony protocol technical reference
- Cost model: [cooperative_stack_cost_model.html] — interactive stack cost model (maintained separately)

---

## Project summary

Dotcommon addresses the structural cash flow problem faced by independent workers — the gap between completing work and receiving payment, currently bridged by predatory short-term credit at 22–40% APR. The platform provides:

1. **Mutual credit pool** — member-owned lending at rates calibrated to actual cost and risk (~3–7% effective APR), governed by reputation-weighted voting
2. **Cooperative governance** — Colony protocol with reputation-weighted decision-making that accrues power to contributors, not capital holders
3. **Consumer-grade UX** — dollar-in dollar-out via Privy embedded wallets and Stripe fiat ramps; no crypto knowledge required
4. **Long-term marketplace ambition** — eventual cooperative-owned job marketplace to displace platform intermediaries extracting 15–30% of worker revenue

The default recommended stack: DAI on Optimism (payment rail) + TLSNotary (income attestation) + Colony (governance) + Custom on-chain pool (credit) + Privy + Stripe (wallet abstraction).
