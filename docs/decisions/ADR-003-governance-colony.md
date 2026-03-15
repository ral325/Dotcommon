# ADR-003: Governance — Colony protocol

**Status:** Accepted  
**Date:** 2026-03-14

## Context

Dotcommon requires a governance system that is resistant to plutocratic capture (governance power should not be proportional to capital held), supports domain-specific decision-making (credit pool governance, marketplace governance, and infrastructure governance have different stakeholder sets), and provides dispute resolution for job outcomes and credit defaults.

## Decision

Use the Colony protocol for cooperative governance, treasury management, and reputation-weighted voting.

## Reasoning

Colony provides reputation-weighted governance where voting power accrues to members based on contribution history rather than token holdings. This is the critical structural property — it prevents wealthy members from buying governance influence. Colony's domain hierarchy allows credit pool parameters to be governed by members with credit domain reputation (those who have used and repaid credit) separately from marketplace governance (those with high job completion records) and infrastructure governance.

Colony also provides the treasury management infrastructure (funding pots, funding queues, expenditure primitives) that Dotcommon needs to manage the cooperative's operating budget and surplus distribution.

**What Colony provides out of the box:**
- Reputation-weighted governance with 90-day half-life decay (preventing reputation aristocracy)
- Domain-structured organisation with permission inheritance
- Motions and dispute resolution with stake-based challenge-response
- Treasury management and funding allocation
- Task and expenditure primitives for job completion and payment
- Upgradability via EtherRouter pattern
- Recovery mode for emergency intervention

**What Colony does NOT provide (requires custom build):**
- Mutual credit pool lending logic
- Income attestation and ZK proof integration
- Wallet abstraction and fiat ramps
- Job marketplace matching
- Credit underwriting model

## Alternatives considered

**Gnosis Safe + Snapshot** — Industry standard multisig treasury with off-chain gasless voting. Low cost but Snapshot voting is token-weighted by default, requiring careful configuration to achieve one-member-one-vote. Does not provide the reputation-weighted meritocracy that Dotcommon requires structurally.

**Aragon OSx** — Fully on-chain governance, more transparent than Gnosis + Snapshot, but same token-weighting problem by default and higher cost.

**Off-chain (Loomio/custom)** — Cleanest one-member-one-vote implementation, zero gas cost, but no treasury integration, no trustless execution, and not crypto-native. Suitable for v1 governance before on-chain infrastructure matures.

## Consequences

- Colony charges a **~3% network fee on all expenditure payouts**. This is a real income-dependent cost included in the cost model.
- Colony's reputation decay (half every 90 days) is aggressive — founding members' early advantage erodes within ~18 months of consistent activity, which is desirable for long-term governance health but requires careful bootstrap design
- The bootstrapping problem: new colonies have no reputation, so no one can vote. Colony's solution is founder-assigned initial reputation equal to token allocation, which decays over time. Founding documents must cap this initial advantage explicitly.
- Colony's governance model is permissive by default (things happen unless challenged), with voting reserved for disputes — this is operationally appropriate for a cooperative
- Colony's internal token (DOT) must be configured as the reputation-bearing instrument — see Design-001
- Colony covers ~60–70% of governance and treasury needs; the financial services layer and consumer UX are entirely custom work
