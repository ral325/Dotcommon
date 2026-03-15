# Design-001: DOT token and reputation formula

**Status:** Accepted (default parameters; weights are governance-adjustable)  
**Date:** 2026-03-14

## Overview

Colony requires an internal reputation-bearing token. Dotcommon uses a non-transferable, non-tradeable governance token (DOT) issued alongside DAI payments. DOT generates Colony reputation, which determines governance weight and share of cooperative surplus. DOT itself has no financial value — it is purely a reputation and governance instrument.

## Design principle

DOT issuance is tied to contribution to the Dotcommon economy, not personal income level alone. A member who earns $200k/year through Dotcommon and a member who earns $40k/year should not have a 5:1 governance ratio simply because of income. The formula rewards cooperative behaviour — routing economic activity through the platform, supporting other members' credit access, participating in governance — not just personal earnings.

## DOT issuance formula

DOT earned per year = sum of the following components:

| Component | Default rate | Rationale |
|---|---|---|
| Income transacted through platform | 1 DOT per $ | Base commitment signal |
| Credit drawn and repaid on time | 2 DOT per $ | Double weight — direct pool contribution |
| Credit deposited into pool as lender | 1 DOT per $ | Rewards pool capitalisation |
| Governance votes cast | Variable — weighted by decision scope | Rewards participation in cooperative governance |
| Successful vouching | Vesting DOT over 12 months of vouched member good standing | Rewards network growth |
| Client jobs posted (registered clients) | 0.5 DOT per $ | Rewards client ecosystem contribution |

## Governance of the formula

All weighting values are stored in a `DotParameters` smart contract governed by Colony's motions system. Any member can propose a change; it passes unless opposed within 3 days; if opposed it goes to reputation-weighted vote in the root domain.

**Constitutional bounds** (require supermajority + 14-day deliberation to change):
- Base income rate: minimum 0.5 DOT/$, maximum 2 DOT/$
- Credit draw multiplier: minimum 1x, maximum 5x
- Lender multiplier: minimum 0.5x, maximum 3x
- Client participation rate: minimum 0 DOT/$, maximum 1 DOT/$

Working values within constitutional bounds are adjustable by standard motions process.

## Why not dollar-per-DOT (simple ratio)?

Simple dollar-for-DOT reproduces income inequality in the governance layer. High-earning members accumulate governance power faster regardless of cooperative engagement. The formula above rewards what a worker cooperative values: routing economic activity through the platform, supporting peers' credit access, participating in collective decision-making.

## Token model

- **Non-transferable**: DOT cannot be sent between addresses, preventing governance power accumulation through purchase
- **Time-decaying**: Colony's built-in 90-day half-life decay ensures reputation reflects recent contribution, not historical accumulation
- **No financial value**: DOT is explicitly not a financial instrument; it generates Colony reputation but has no claim on assets beyond what the rewards formula provides to all members
- **Rewards formula**: Member's share of cooperative surplus = geometric mean of (DOT holdings / total DOT) and (reputation / total reputation) — per Colony's standard rewards formula (Section 2.4.3 of Colony whitepaper)

## Multi-stakeholder DOT domains

Workers, registered clients, and potentially other stakeholder classes each earn DOT in their respective Colony domains. Cross-domain governance rights are scoped:
- Workers vote on credit pool parameters, worker compensation structures, cooperative bylaws
- Registered clients vote on marketplace fee structures, dispute resolution policies, onboarding criteria for worker categories
- Full membership criteria (transitioning from client to full member) is itself a governance decision
