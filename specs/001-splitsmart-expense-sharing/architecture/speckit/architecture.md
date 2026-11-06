# SplitSmart Architecture Proposal — Spec Kit

Status: proposed; the source specification is Draft and this document is not human-approved.

## Scope and goals

The full product includes a primary web experience plus Android and iOS applications covering FR-001–FR-015. The Java 21/Maven/Swing implementation is a labelled MVP/prototype subset covering the core flows; it cannot satisfy FR-014 or NFR-006 alone.

The proposed architecture centers on a shared application/domain contract. Presentation adapters invoke use cases; domain rules do not depend on Swing or a client framework. Infrastructure owns persistence, credential handling, receipt storage, and notification delivery behind ports.

## Components and dependency direction

Swing presentation, and future web/mobile adapters, depend on application use cases. Application services depend on domain objects and ports. Domain entities/services depend on neither presentation nor infrastructure. Infrastructure implements ports and is selected by composition. No external integration is assumed.

Core components are Account/Access, Group/Membership, Expense/Allocation, Approval, Notification, Balance, Payment, and Reporting. An authorization policy protects account, group, receipt, expense, balance, and payment reads. An approval transition records decision history and creates one idempotent balance-effect record only on approval.

## Critical rules

The payer is separate from ordered participants. Participants must be active members at submission. Equal split remainder cents are assigned in participant order. Percentage shares are non-negative, total 100%, and reconcile after declared currency rounding. Pending/rejected expenses do not affect balances. Payments greater than the current payer-to-recipient outstanding amount are rejected. Historical expense/payment records remain immutable when membership changes; historical access policy remains open.

## Runtime boundary

The MVP may run as a Java Swing process with approved local or remote adapters. The full product requires a service boundary and web/Android/iOS clients, but service framework, deployment, storage, receipt delivery, and notification channels are unselected proposals. The architecture must not claim those channels exist until implemented and verified.

## Security and reliability

Email/password authentication and recovery are required; recovery verification details are open. Enforce group authorization server-side or at the trusted application boundary, protect receipts by the same policy, and audit review/payment history. Use transaction boundaries around state transitions and balance/payment effects, optimistic versioning or equivalent concurrency control, idempotency keys/unique effect constraints, and explicit failure reporting. Notification failure must not silently change expense status or balance outcome.

## Verification

JUnit 5 covers allocation, percentage reconciliation, authorization, state transitions, exactly-once effects, payments, reports, and ports/adapters. PlantUML sources in `diagrams/` are editable and must be reviewed with architecture changes.
