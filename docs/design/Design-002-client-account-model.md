# Design-002: Client account model — guest and registered tiers

**Status:** Accepted  
**Date:** 2026-03-14

## Overview

Dotcommon supports two client tiers: guest payment (no account required) and registered client accounts that build reputation and earn limited governance rights.

## Guest clients

- Pay via card through Stripe; Stripe converts to DAI and funds Colony escrow
- No account, no persistent identity, no data retained beyond the transaction
- Generate zero data for the cooperative's underwriting model
- Appropriate for one-off jobs or clients unwilling to create an account

## Registered clients

Registered clients create an account and build a transaction history on the platform. Benefits to the cooperative:

**Underwriting value**: A worker with a long-standing relationship with a reliable registered client has more predictable income than one who finds new clients each job. Registered client history feeds into the worker's income attestation and credit underwriting model — a proven client relationship is a positive signal even before ZK attestation matures.

**Governance participation**: Registered clients earn DOT in the client domain (see Design-001) and participate in governance decisions that directly affect them — marketplace fee structures, dispute resolution policies, worker category onboarding. They do not vote on credit pool parameters or worker compensation structures.

**Network effects**: Registered clients who repeatedly hire from the platform and build a reputation as reliable, prompt-paying employers strengthen the cooperative's data advantage and reduce underwriting risk for workers they hire.

## Governance domain separation

Client governance is scoped to a separate Colony domain from worker governance. This prevents clients from influencing decisions they are not stakeholders in (credit pool parameters, internal compensation structures) while giving them voice in decisions where their perspective is legitimate (platform policies that affect their experience as buyers of work).

## Path to full membership

The criteria by which a registered client may become a full cooperative member — with full governance rights across all domains — is itself a governance decision. Initial suggested criteria: a registered client who has also completed at least X jobs as a worker through the platform qualifies for full membership. Specific thresholds are governance-adjustable.

## Implementation note

Guest payment is technically straightforward — Stripe handles anonymous card payments without requiring an account. The design decision is primarily about what the registered client experience offers that justifies account creation friction. The answer is: better dispute resolution support, payment history for their own records, ability to re-hire known workers, and governance participation.
