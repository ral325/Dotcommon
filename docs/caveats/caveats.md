# Caveats Register

Known limitations, model simplifications, risks, and important qualifications that anyone working on this project should understand.

---

## CAV-001: Cost model structural bias toward cooperative

**Context:** The interactive cost model compares cooperative stack costs against a traditional platform stack. The comparison is structurally biased in the cooperative's favour in several ways:
- The traditional baseline is undercosted: it does not include platform algorithm risk, account suspension risk, race-to-bottom pricing pressure, or the cost of managing relationships with multiple platforms
- Default costs are not fully captured in the cooperative pool — the risk premium implicitly prices default risk but the model does not simulate actual pool drawdowns
- The credit cost comparison is asymmetric: traditional credit (22% APR on credit card) vs cooperative credit (3–7% effective rate) is a fair comparison in dollar terms, but the traditional option doesn't require the member to trust an on-chain pool

**Implication:** The model is useful for directional intuition and parameter sensitivity, not for precise cost claims in fundraising materials without appropriate caveats.

---

## CAV-002: Colony network fee is a significant income-dependent cost

**Context:** Colony charges ~3% on all expenditure payouts. At default parameters ($80k income, full platform routing), this is ~$2,400/member/year — larger than all other overhead costs combined. This fee was not in the original cost model and was added after reviewing the Colony whitepaper.

**Implication:** The cooperative's total cost advantage over traditional platforms is real but smaller than initial estimates suggested. At 3% Colony fee + 0.5–1.5% Stripe ramp, the pure overhead cost of routing income through the platform is 3.5–4.5% before credit costs. This remains far below traditional platform fees (15–30%) but must be communicated accurately.

---

## CAV-003: Cost model assumes homogeneous membership

**Context:** The model represents a single archetypal member with fixed income, job frequency, and credit behaviour. Real membership will have a distribution — some members earn $30k/year, others $200k; some take credit frequently, others never. Overhead costs (infra amortised, governance gas) favour lower-income members disproportionately as a fixed dollar amount.

**Implication:** The per-member cost figures should be understood as averages across an assumed membership distribution, not as precise individual costs. A proper model would simulate a membership distribution.

---

## CAV-004: ZK attestation costs are estimates, not quotes

**Context:** TLSNotary has no fixed pricing — compute costs depend on self-hosted infrastructure. Reclaim Protocol pricing may change as a commercial entity. zkPass pricing is not publicly fixed. All ZK attestation cost figures in the model are estimates from comparable ZK compute benchmarks.

**Implication:** Treat pessimistic ZK cost estimates as planning floors, not upper bounds. Actual costs could exceed the pessimistic range if commercial providers reprice or compute costs increase.

---

## CAV-005: Smart contract audit costs are amortised linearly

**Context:** Audit costs are amortised over 5 years regardless of network growth rate. A faster-growing network amortises sooner; a stagnant one more slowly. Additionally, any significant modification to the credit pool contract requires a new audit — the amortisation model assumes one audit per 5-year period.

**Implication:** If the credit pool contract requires significant changes in year 2 (e.g. to add TLSNotary integration or new credit products), a new $20k–$80k audit cost will be incurred. Budget accordingly.

---

## CAV-006: Legal and compliance costs are highly jurisdiction-dependent

**Context:** The cost model uses a simple linear formula for legal/compliance costs ($2,000 + $1.50×members optimistic, $5,000 + $4.00×members pessimistic). Real legal costs are highly jurisdiction-dependent and may step up nonlinearly at regulatory thresholds — particularly if money transmission licensing is required.

**Implication:** Legal cost estimates are the most uncertain line in the model. Actual costs could be an order of magnitude higher if significant regulatory compliance work is required.

---

## CAV-007: Colony reputation decay is more aggressive than intuition suggests

**Context:** Colony's 90-day half-life means reputation earned today is worth ~25% of its original value after 6 months, and ~6% after a year. This is by design — it prevents reputation aristocracy and ensures governance power reflects recent contribution. However, it also means:
- Founding members who reduce activity lose governance influence faster than expected
- Members who take parental leave or extended illness absence will see significant reputation decay
- The cooperative needs explicit mechanisms for protecting members during legitimate absence

**Implication:** Founding documents must address reputation decay during legitimate absence. Colony's whitepaper acknowledges this and suggests mechanisms for awarding compensatory reputation by consensus — these must be specified in Dotcommon's governance rules.

---

## CAV-008: What is not in the cost model

The following real costs are not modelled and should be added as the project matures:
- Pool liquidity reserve opportunity costs
- Member onboarding costs (KYC-equivalent, initial attestation, support)
- Platform development and maintenance costs (significant, especially early stage)
- Member support and dispute resolution operational costs
- Token volatility risk (if governance token develops financial value)
- Regulatory compliance costs specific to financial services licensing
