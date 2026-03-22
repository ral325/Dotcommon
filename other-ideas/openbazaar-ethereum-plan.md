# Decentralized Cooperative Marketplace on Ethereum
### A Dotcommon Architecture Plan — OpenBazaar Pattern, Cooperative Structure

---

## What OpenBazaar Got Right (and Wrong)

OpenBazaar's core insight was correct: eliminate the platform as a rent-extracting intermediary by making the protocol the platform. Its failure modes are instructive:

- **No persistent storefront** — listings disappeared when the seller went offline. IPFS pinning was optional and unreliable.
- **No network-level quality signal** — reputation was node-local, not portable. A new buyer had no way to assess a seller they'd never transacted with.
- **Crypto friction killed adoption** — Bitcoin-only initially, then multi-coin, but always requiring the buyer to already hold crypto.
- **No cooperative structure** — it was peer-to-peer but not worker-owned. There was no shared surplus, no governance, no mutual credit.

A Dotcommon implementation fixes all four.

---

## Layer 1 — Content and Listing Persistence

**Problem:** Storefront data (listings, images, descriptions, pricing) cannot live on-chain — gas cost is prohibitive and the data is mutable by design.

**Solution stack:**

- **IPFS** for content-addressed storage of listing data, images, and seller profiles. Each listing is a JSON object pinned to IPFS and referenced by its CID.
- **Filecoin or a pinning service** (Pinata, web3.storage) for persistence guarantees. Sellers or the network pays pinning fees — this is a real cost line not currently in the cooperative stack model.
- **On-chain registry** — a lightweight smart contract maps seller addresses to their current profile CID and active listing CIDs. Updating a listing = one cheap L2 transaction to update the CID pointer.

The on-chain registry is the canonical source of truth for *what exists*. IPFS is the source of truth for *what it contains*.

---

## Layer 2 — Payment and Escrow

This is where Ethereum earns its place. The payment flow for a marketplace transaction:

1. Buyer initiates purchase → funds held in **escrow smart contract**
2. Seller fulfills → buyer confirms receipt → funds released
3. Dispute → funds held pending arbitration outcome
4. Timeout (no confirmation, no dispute) → auto-release after N days

**Escrow contract design considerations:**

- Single contract per transaction (factory pattern) vs. shared escrow pool. Factory is simpler to reason about; pool is cheaper per transaction.
- Escrow holds DAI (or whichever stablecoin is the network rail). No price volatility risk during fulfillment window.
- ERC-4337 paymaster sponsors gas for buyers — they never touch ETH directly. This is already in the Dotcommon stack.
- For physical goods, the escrow timeout window needs to be long enough for shipping (7–14 days minimum). For digital goods, near-instant release is fine.

**Fiat ramp integration:** Buyer pays fiat → Stripe converts to DAI → DAI enters escrow. From the buyer's perspective this is indistinguishable from a normal card checkout. The Privy + Stripe stack handles this.

---

## Layer 3 — Dispute Resolution

OpenBazaar used a moderator system — a pre-selected third party held a key in a 2-of-3 multisig. This worked technically but had no accountability structure.

**Better approach: tiered resolution**

**Tier 1 — Negotiated settlement.** 48-hour window for buyer and seller to resolve directly. No on-chain action needed.

**Tier 2 — Peer arbitration.** If Tier 1 fails, dispute goes to a randomly selected panel of staked network members. Arbitrators earn a fee (from escrow) for resolving. They vote on fund allocation — partial releases are possible (e.g. 70% to seller, 30% refund to buyer).

**Tier 3 — Kleros escalation.** Unresolved or contested peer arbitration escalates to Kleros, which has a large existing juror pool and is already battle-tested for marketplace disputes. This is an external dependency but well-established.

**Arbitrator staking:** Peer arbitrators must hold staked DOT tokens to participate. Bad rulings (appealed and overturned) slash their stake. This aligns incentives without requiring a centralized trust authority.

---

## Layer 4 — Reputation and Identity

This is the hardest unsolved problem in decentralized marketplaces. OpenBazaar had no good answer.

**Reputation as on-chain state:**

- After each completed transaction, buyer can submit a signed rating (1–5, optional text hash on IPFS).
- Ratings are stored on-chain against the seller's address. Immutable, portable, not platform-controlled.
- Seller reputation score = weighted average of ratings, decaying toward neutral over time for inactive sellers.

**Sybil resistance:**

- Sellers must pass ZK attestation (TLSNotary path) to list. This anchors identity to verifiable off-chain data without revealing PII.
- Buyers who want to rate must also have completed ZK attestation or hold minimum stake. Anonymous ratings are weighted lower.
- New seller bootstrap problem: first N transactions get escrow protection extended (longer dispute window, lower buyer risk), acknowledging that reputation is thin.

**Reputation portability:** Because reputation lives on-chain against an address, it's portable across any frontend that reads the same contract. This is a structural advantage over platform-owned reputation (Etsy stars, Upwork JSS).

---

## Layer 5 — Discovery and Search

On-chain search is not viable. Discovery requires an indexing layer:

- **The Graph protocol** — indexing the on-chain registry contract events (new listings, CID updates, completed transactions) into a queryable subgraph. Frontends query The Graph rather than the chain directly.
- **Off-chain search index** — a network-operated (cooperative-owned) search service that indexes listing content from IPFS. This is a centralization point but doesn't affect settlement or custody — it's a convenience layer only.
- **Category/tag taxonomy** — stored in listing JSON, indexed by the search layer. Governance can vote on canonical category structures.

The key principle: **discovery can be centralized** (it's already fast and cheap), **settlement cannot be**.

---

## Layer 6 — Governance Integration

This is what makes it a cooperative rather than just a decentralized marketplace:

- **Fee structure:** The network charges a small transaction fee (1–2% of GMV) that flows into the cooperative treasury. This replaces the 20%+ platform rake. The fee rate is governance-controlled with constitutional bounds.
- **Surplus distribution:** Treasury surplus above operating reserves is distributed to worker-members proportional to their stake accrual (GMV-weighted, as per the existing DOT model).
- **Protocol upgrades:** Aragon OSx governance controls contract upgrade keys. No single party can change the protocol rules unilaterally.
- **Category and curation policy:** Governance votes on prohibited categories, dispute resolution parameters, fee rates, and arbitrator selection criteria.

---

## Build Sequence

Given Dotcommon's current state, the sensible phased approach:

### Phase 1 — Thin Protocol, Manual Backstop

Deploy escrow contract + on-chain listing registry on Optimism testnet. Off-chain dispute resolution (manual admin). No ZK attestation yet — social vouching only. Privy + Stripe for buyer onramp. Single product category (the digital flea market vertical).

**Goal:** Prove the payment and escrow mechanics work end to end.

### Phase 2 — Reputation and Attestation

Wire in TLSNotary attestation for sellers. Deploy on-chain reputation contract. Implement The Graph subgraph for listing discovery. Launch peer arbitration with staked arbitrators drawn from early network members.

### Phase 3 — Governance and Surplus

Deploy Aragon OSx DAO with custom reputation-weighted voting plugin. Connect treasury to transaction fee collection. Implement stake accrual and first dividend distribution. Kleros integration for Tier 3 escalation.

### Phase 4 — Credit Integration

Wire in mutual credit pool for sellers. Credit underwriting uses on-chain transaction history (GMV, dispute rate, reputation score) as the income signal. This is the full Dotcommon stack operating together.

---

## Key Open Questions

1. **Physical goods vs. digital goods first?** Physical goods need longer escrow windows and create more dispute surface area. Digital goods are simpler but the flea market vertical probably mixes both.

2. **Who pins IPFS content?** The cooperative could run pinning infrastructure, but this is an operational dependency. Decentralizing pinning to members (with token incentives for uptime) is possible but adds complexity.

3. **Buyer identity model:** Anonymous guest checkout is important for accessibility, but anonymous buyers can't rate sellers or access credit. Where does the cooperative draw the line on requiring registration?

4. **Cross-chain?** Starting on Optimism is sensible, but Arbitrum and Base have larger existing user bases. The on-chain registry and escrow contracts would need bridge logic if the network eventually spans multiple L2s.
