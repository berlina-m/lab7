# SplitSmart consolidated architecture proposal — Spec Kit

Status: proposal for human review; the feature specification remains Draft. This document reconciles the authoritative customer request, constitution, Draft specification, existing proposals, and ChatGPT review guidance. Requirements are not approvals.

## Classification and scope

Customer requirements are the capabilities in `UserRequirement.md`: individual accounts/authentication; groups and membership; expenses (amount, date, description, payer, participants, optional receipt); equal and percentage allocation; member notifications; configurable review/approval; pairwise balances; payments; reports with date ranges; web, Android, and iOS. The Draft specification clarifies behavior but is not approved. Constitution constraints are Java 21/Maven, OO design, Swing MVP/prototype, strict presentation/business separation, `BigDecimal`, JUnit 5, PlantUML, traceability, and human review. All technology, storage, API, deployment, and policy choices below are proposed decisions or open decisions.

The target is a multi-channel product. A Java Swing MVP/prototype demonstrates core flows against the same application/domain ports but does not satisfy the complete web/Android/iOS product or FR-014/NFR-006 by itself.

## Logical architecture

Web, Android, iOS, and Swing clients call an application/service boundary. Controllers authenticate and authorize, validate commands, and invoke inward-facing use-case ports. The domain owns expense allocation, approval state, ledger effects, pairwise balances, payments, and invariants. Infrastructure adapters implement ports for persistence, identity, receipt storage, notifications, reporting, and clocks; the core never depends on concrete adapters. See [diagrams/context.puml](./diagrams/context.puml), [components.puml](./diagrams/components.puml), and [deployment.puml](./diagrams/deployment.puml).

Server-side rules are authoritative for membership, permissions, allocation, money arithmetic, approval, ledger effects, payment validation, receipt authorization, and reports. Clients are untrusted presentation layers. The API exposes commands/queries for accounts, groups, expenses, approvals, balances, payments, receipts, notifications/preferences, and reports; it must return idempotency and version-conflict results without exposing storage details.

## Accounting model (proposed, unapproved)

An approved expense has one payer, an ordered participant list, a currency, and immutable allocation rows. Convert input to integer minor units using the selected currency policy before allocation. Equal split is floor(total/n), with the remainder minor units assigned one each in recorded participant order. Percentage split allocates each percentage share deterministically, then requires the unresolved residual policy decision (recommended: largest-remainder allocation with participant-order tie break, subject to explicit approval). The sum of allocations equals the expense total.

For each allocation, create an obligation from participant to payer for that participant's share, except self-obligations are eliminated. This works whether payer participates or not: if A pays $90 for A/B/C equally, allocations are A=$30, B=$30, C=$30 and only B→A $30 and C→A $30 remain; if A pays $90 for B/C, B=$45 and C=$45, producing B→A $45 and C→A $45. A payer's own share is not debt to self.

Pairwise balance is the signed net of approved expense obligations and recorded payment effects, grouped by currency and unordered member pair. Netting is a presentation/query projection, not destruction of source entries. Payments reduce an outstanding obligation only after a transaction validates payer/payee, currency, amount, and available outstanding amount. The authoritative source is append-only financial effects; a balance projection may be rebuilt and reconciled.

Invariants: allocations conserve total; no self-obligation; no pending/rejected balance effect; one approved expense effect set exactly once; payment effects never exceed outstanding obligations; every projection can reconcile to source effects; all money uses `BigDecimal` at explicit scale/rounding boundaries, never float/double.

## Approval and consistency

Proposed states are `PENDING`, `APPROVED`, `REJECTED`; only approved expenses create effects. Review eligibility, self-approval, one-versus-many reviewers, quorum, multi-level approval, and resubmission are open product decisions. A decision command carries expense version and idempotency key. One transaction locks/version-checks the expense, records a unique decision `(expense, reviewer, decision sequence/idempotency key)`, transitions state, and inserts a unique effect batch keyed by expense/version when approval completes. Concurrent decisions yield a conflict or deterministic already-decided response; retries return the original result. Rejection records audit history and may create a new submission revision, never mutate the old decision.

Payment validation and recording are one transaction: lock/version-check the relevant obligation or aggregate, re-read authoritative effects, enforce remaining amount, insert a unique payment/effect by idempotency key, and commit. Rollback leaves no partial financial effect. Failed notifications do not roll back financial commits.

## Security, history, and auxiliary services

Identity owns password recovery, session/token lifecycle, revocation, rate limits, and audit events. Every read/write checks authenticated identity, group role, membership state, resource ownership, and receipt authorization server-side. Former-member access to historical expenses, receipts, balances, and payments is an open authorization decision; retention is separate from access. Sensitive data is minimized, encrypted in transit/at rest by the eventual deployment, and security events are auditable; implementation is not claimed.

Corrections use append-only compensating entries: payment refund/correction, expense amendment as a new revision, or approval reversal as a compensating reversal. Destructive edits are prohibited by architecture recommendation pending approval. Receipt format, size, content validation, retention/deletion, and access policy are open. Notifications need channel, preference, retry/deduplication, and delivery expectation decisions; delivery failure is not an approval failure. Reports need decisions on date/time boundaries, timezone, currency aggregation, snapshot consistency, and export formats.

## Release boundary and verification

The Java 21/Maven Swing MVP uses presentation adapters over application ports and demonstrates account/group/expense/allocation/approval/balance/payment/report flows with `BigDecimal` and JUnit 5. It is explicitly a limited prototype. Full product requires web, Android, iOS, service-side enforcement, production identity, receipt, notification, reporting, and operational controls. PlantUML sources are editable; syntax validation was not available in this environment. No code, build, deployment, security, or implementation verification is claimed.

