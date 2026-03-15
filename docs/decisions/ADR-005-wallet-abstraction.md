# ADR-005: Wallet abstraction & fiat ramps — Privy + Stripe

**Status:** Accepted  
**Date:** 2026-03-14

## Context

Dotcommon's target membership is independent workers, not crypto-native users. A cooperative that requires members to manage private keys, acquire stablecoins from exchanges, and pay gas fees manually will fail to reach mainstream adoption. The wallet and fiat layer must make the crypto infrastructure invisible.

## Decision

Use Privy for embedded wallet infrastructure (ERC-4337 smart contract wallets via email/social login) with Stripe as the primary fiat on/off ramp. Moonpay as international fallback.

## Reasoning

**Privy** creates a smart contract wallet tied to a member's email address or social login. The member never sees a private key. Wallet creation is silent during onboarding. Gas fees are sponsored by a Dotcommon-operated ERC-4337 paymaster contract, meaning members never need to hold ETH for gas.

**Stripe** provides fiat-to-DAI conversion on entry (card → DAI) and DAI-to-fiat on exit (DAI → ACH bank transfer). From the member's perspective this looks identical to receiving payment via any other platform. The 1–2 business day ACH settlement time is the only visible difference from instant bank transfer.

**The full payment flow:**
1. Client pays via card → Stripe converts to DAI → DAI into Colony escrow
2. Job completes → Colony releases DAI to member's Privy wallet
3. Member withdraws → Stripe converts DAI to USD → ACH to bank account

Members who hold DAI natively can bypass Stripe entirely, paying only L2 gas costs.

## Alternatives considered

**Privy + Moonpay** — Moonpay has broader international jurisdiction coverage but higher fees (1.0–2.5% vs 0.5–1.5% for Stripe). Retained as international fallback; not default due to cost.

**Web3Auth + Transak** — Open-source MPC wallet infrastructure, lower commercial dependency than Privy. Less mature developer experience. Transak ramp is mid-range on fees. Viable alternative if Privy's terms become unfavourable; architecture should not hardcode Privy assumptions.

**No abstraction (crypto-native only)** — Full member sovereignty, zero per-member wallet cost. Inaccessible to ~99% of target membership. Not viable for mainstream cooperative.

## Consequences

- Per-member wallet cost: $0.60–$1.20/year (Privy at $0.05–$0.10/active user/month)
- Fiat ramp conversion fee: 0.5–1.5% per conversion (Stripe)
- Paymaster gas sponsorship: ~$0.002–$0.01 per transaction
- Stripe and Privy are commercial dependencies at the fiat/UX layer — this is accepted as unavoidable (see design principle: fiat layer commercial dependency is structurally unavoidable; sovereignty ends where dollars begin)
- Stripe crypto ramp availability is jurisdiction-dependent — Moonpay fallback must be maintained
- Members in jurisdictions where neither Stripe nor Moonpay operate cannot use the fiat ramp layer; they can still participate crypto-natively
