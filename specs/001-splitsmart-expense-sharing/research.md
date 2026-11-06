# Research: SplitSmart Planning Decisions

## Decision: Layered, dependency-inverted domain core

Rationale: It satisfies the constitution's Swing separation and keeps rules testable while allowing later web and mobile adapters. Alternatives considered: a Swing-centric design (rejected because it couples business rules to presentation) and a distributed microservice design (deferred because no scale or integration requirement justifies its operational cost).

## Decision: Repository and notification ports

Rationale: Persistence and delivery channels are unspecified. Ports make the MVP runnable with an approved storage adapter later and allow in-app MVP notifications without implying email, push, or a cloud provider. Alternatives considered: selecting a database or delivery vendor now (rejected as unsupported assumptions).

## Decision: Explicit money value object and allocation service

Rationale: BigDecimal, currency minor-unit rules, rounding, equal-split remainder order, and percentage reconciliation are central correctness boundaries. Alternatives considered: primitive numeric fields (rejected by the constitution) and implicit rounding (rejected because it makes reconciliation unverifiable).

## Decision: Full-product architecture remains a target boundary, not a Swing claim

Rationale: FR-014 and NFR-006 require web, Android, and iOS. The constitution explicitly says Swing is only an MVP/prototype. Client technology, deployment, and API style remain open. Alternatives considered: calling the Swing MVP the product (rejected as a scope violation).

## Decision: Approval effects are idempotent and transactionally guarded

Rationale: Pending/rejected expenses must not affect balances and an approved expense must affect them exactly once, including concurrent review attempts. The design therefore needs an approval state transition and an idempotent balance-effect record. Storage-specific locking/versioning is open.

## Resolved clarifications carried forward

The plan preserves the five specification clarifications: separate payer, active-member-only participants, participant-order remainder allocation, email/password plus recovery, and rejection of payments above current outstanding balance.

## Remaining research/open decisions

Supported currencies and precision, persistence technology, password recovery verification, receipt constraints/access, notification channels/preferences, approval roles/self-approval/quorum, report parameters/exports, payment corrections, deployment, and cross-channel release boundaries require human/product or engineering approval. They are not silently resolved here.
