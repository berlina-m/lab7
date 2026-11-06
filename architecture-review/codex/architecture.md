# SplitSmart Independent Codex Architecture Proposal

**Status:** Proposed, unapproved
**Independence:** Prepared from the customer request, constitution, Draft specification, and requirements checklist. No Spec Kit proposal was used.

## 1. Goals and scope

SplitSmart needs one coherent business model for accounts, groups, shared expenses, review, notifications, pairwise balances, payments, and reports. The target product is a primary web experience plus Android and iOS applications (FR-014, NFR-006). The Java 21/Maven/Swing implementation is a separately labelled prototype subset required by the constitution; it cannot satisfy the complete channel requirement.

This proposal optimizes for a small, cohesive, testable domain core. It separates presentation from use cases, keeps monetary rules in domain services, preserves immutable transaction history, and exposes the same application capabilities to different clients. It does not select a database, cloud provider, external notification vendor, receipt policy, supported currencies, or production deployment technology as if those were requirements.

## 2. Scope boundary

### Complete product target

The recommended target is a shared application/service boundary consumed by web, Android, and iOS clients. All clients use the same authorization, validation, split, approval, balance, payment, notification, and reporting rules. A web client is the primary delivery channel; mobile clients are additional channel adapters (FR-014, NFR-006).

### Java Swing MVP/prototype

The Swing client uses the same domain and application interfaces but may use local or replaceable infrastructure adapters. It covers the explicitly listed subset: account access, basic groups, expense entry and splits, optional receipt handling, one configured review step, in-application notifications, balances, payments, summaries, and date-range reports (FR-001–FR-013 as applicable to the subset). It is not evidence that web, Android, or iOS delivery is complete.

## 3. Proposed architecture style

**Proposal:** ports-and-adapters architecture with a modular monolith/service boundary and a framework-independent domain core. This gives Swing and future web/mobile clients the same use-case contracts while avoiding premature distributed services. The domain module depends on no UI, database, or transport framework.

Alternatives considered:

| Option | Benefit | Cost / reason not selected now |
|---|---|---|
| Layered monolith without explicit ports | Simple initial implementation | Adapters can leak into business rules and make Swing-to-web evolution harder. |
| Microservices | Independent scaling and deployment | Adds operational, consistency, and testing complexity not required by the supplied scope. |
| Separate business logic per client | Fast channel-specific prototyping | Duplicates monetary and approval rules and risks cross-channel inconsistency (NFR-004, NFR-006). |

The selection remains a proposed engineering decision and requires human approval.

## 4. System context and actors

Actors are account holders, group organizers, group members, expense submitters, and expense reviewers. Web, Android, iOS, and Swing are clients. The application boundary contains identity/account access, groups, expenses, review, notifications, balances, payments, and reports. Persistence and receipt storage are abstract ports because their technology and limits are unspecified. No external integration is assumed.

See [diagrams/context.puml](diagrams/context.puml).

## 5. Components and dependency direction

| Component | Responsibility | Depends on |
|---|---|---|
| Client adapters | Render state, collect input, show success/errors; Swing event handlers contain no business rules | Application ports |
| Application/use-case layer | Authorize intent, coordinate transactions, map commands/results, publish domain events | Domain, ports |
| Domain model/services | Invariants, split allocation, approval state, balance effects, payment limits | Java standard types; `BigDecimal` |
| Persistence adapters | Store/retrieve aggregate and immutable history records | Domain/application ports |
| Identity adapter | Credential verification and recovery workflow integration | Application ports; selected implementation is open |
| Receipt adapter | Validate and store/retrieve receipt references | Application ports; selected implementation is open |
| Notification adapter | Deliver in-app notification and report delivery failure | Application event port |
| Report/query adapter | Read authorized records and calculate summaries | Application query ports, persistence |

Allowed dependency direction is clients → application → domain; application/domain ports point inward, and infrastructure implements outward-facing ports. Domain code must not import Swing, HTTP, persistence classes, or notification SDKs.

See [diagrams/components.puml](diagrams/components.puml).

## 6. Suggested module/package structure

```text
splitsmart-domain/
  account, group, expense, review, balance, payment, notification, report
  money, shared kernel, domain events
splitsmart-application/
  commands, queries, usecases, authorization, transaction ports
splitsmart-adapters/
  persistence, identity, receipts, notifications, reporting
splitsmart-swing/
  screens, presenters, controllers, client configuration
splitsmart-web/       (target product adapter; proposal)
splitsmart-android/   (target product adapter; proposal)
splitsmart-ios/       (target product adapter; proposal)
```

The exact Maven module split is a proposal. A single Maven project with packages is acceptable for the prototype if the same dependency boundaries are enforced.

## 7. Domain model

- **Account:** stable identifier, unique email, credential reference, status; never exposes password material.
- **Group:** identifier, name, organizer/management policy, approval configuration, lifecycle.
- **Membership:** account-group relation, active/inactive state, role/permissions, effective dates. Deactivation does not rewrite transactions.
- **Expense:** amount and declared currency, date, description, submitter, separate payer, participant order, split method, calculated shares, receipt reference, status, version, and review history.
- **Expense Share:** expense, participant, percentage or allocated amount. Shares are persisted or reproducibly derived so the approved allocation is historically stable.
- **Review Decision:** reviewer, decision, timestamp, reason, and decision sequence/version.
- **Notification:** recipient, group/expense reference, event type, created/read state; it must not be delivered to non-members.
- **Balance:** a derived or materialized pairwise obligation with clear currency and source version. The authoritative inputs are approved expense effects and valid payments.
- **Payment:** payer, recipient, amount, currency, date, status, idempotency key, and immutable history; corrections are open.
- **Report:** authorized query parameters, time boundary semantics, source snapshot/version, and expense/balance/payment summaries.

See [diagrams/domain.puml](diagrams/domain.puml).

## 8. Core rules and workflows

1. **Account access:** registration validates email/password input; authentication returns a session through an identity port; authorization is checked for every account/group query. Password recovery uses a replaceable verification workflow; its exact safeguards are open (FR-001, NFR-001).
2. **Groups:** an authorized organizer creates a group and changes memberships. Membership changes are recorded. Historical records remain unchanged; whether former members retain access is a product policy open question (FR-002, FR-015).
3. **Expense submission:** authorize an active group member; verify payer policy, participant order, active membership at submission, positive amount, currency precision, split validity, and receipt policy. Save as pending, then publish a new-expense event. Notification failure must not falsely change expense status (FR-003–FR-009).
4. **Equal split:** calculate the base minor-unit allocation using `BigDecimal`; distribute remaining minor units one at a time in recorded participant order. The sum must equal the expense amount (FR-005, NFR-002).
5. **Percentage split:** require non-negative percentages totaling exactly 100% under an explicitly approved percentage precision. Calculate shares with `BigDecimal`, round at the declared currency boundary, reconcile residual minor units deterministically, and reject any unreconciled result (FR-006, NFR-002). The residual policy needs product approval if the specification does not define it.
6. **Review:** an authorized reviewer changes pending to approved or rejected under an optimistic version check. Repeated or concurrent decisions are rejected or become no-ops according to the approved state machine. Approval creates exactly one balance-effect record; rejection creates none. Self-approval and quorum rules remain open (FR-007, FR-008).
7. **Balance:** approved expense effects credit the recorded payer and debit each participant share, including the case where payer is not a participant. Payments reduce the payer-to-recipient obligation without rewriting expense history. Pairwise netting presentation must not erase auditable source effects (FR-010).
8. **Payment:** validate payer, recipient, same currency, positive amount, current outstanding obligation, and idempotency key inside one transaction. Reject amounts above the current outstanding balance with no history or balance change (FR-011).
9. **Reports:** authorize the requester, validate non-empty/non-reversed date range and supported parameters, query a consistent snapshot, and return parameters with results. Export formats are not selected (FR-012, FR-013).

See [diagrams/sequences.puml](diagrams/sequences.puml).

## 9. Persistence and data ownership

The application owns accounts, memberships, expenses, shares, review history, notifications, payments, and report query semantics. The balance is either a transactionally maintained projection rebuilt from approved effects or a query projection with a verifiable source ledger; the selected approach must preserve exact-once effects.

Persistence options are relational storage, document storage, or an in-memory adapter for the Swing prototype. A relational store is a reasonable proposal for the full product because approval/payment transactions, uniqueness, ordering, and audit history need atomic constraints, but no technology is selected by the requirements. Receipt bytes should be behind a receipt port rather than embedded in the domain; format, size, retention, and access policy are open.

## 10. Security and privacy

Use server-side authorization at every use-case boundary, group membership checks on every group-scoped read, and least-privilege role checks for organizers/reviewers. Passwords must be handled through a suitable credential implementation and never stored as plaintext; recovery verification and session design remain proposals pending approval. Receipt references require the same group/history authorization as the associated expense. Audit review decisions, membership changes, payment events, and status transitions. No compliance certification is claimed.

## 11. Consistency, concurrency, and failures

Use a transaction boundary around submission plus durable status, review decision plus one-time approved-effect creation, and payment validation plus recording. Enforce unique constraints for account email, review transition/effect identity, and payment idempotency key. Use aggregate version checks for concurrent reviews and membership changes. Notification delivery is asynchronous or adapter-specific; a failure is visible/retryable and cannot roll back or fabricate the financial outcome. A client receives an explicit success or actionable failure (NFR-004, NFR-005).

## 12. Test architecture

JUnit 5 tests should include:

- domain unit tests for `BigDecimal` precision, equal remainder order, percentage validation/reconciliation, status transitions, and payment limits;
- application tests with fake ports for authorization, notification failure, idempotency, and exact-once approval effects;
- adapter/integration tests for the selected persistence, identity, receipt, and notification adapters;
- client/component tests for Swing presenters and input/error rendering without putting rules in event handlers;
- cross-channel acceptance tests for FR-001–FR-013 through web, Android, and iOS before claiming FR-014/NFR-006;
- traceability tags or test naming that identifies covered FR/NFR IDs.

## 13. Runtime/deployment views

For the prototype, a Java 21 Maven-built Swing process can use an in-memory or selected local persistence adapter. For the full product, the proposed shape is web/mobile clients calling a shared application service, with separately selected identity, persistence, receipt, notification, and reporting adapters. Hosting, API protocol, database, mobile framework, and distribution targets are intentionally unselected proposals. The Swing process cannot be the production substitute for all three requested channels.

## 14. Risks and assumptions

Major risks are an undefined currency policy, percentage residual policy, ambiguous reviewer permissions, historical access after membership removal, unbounded receipt requirements, unclear payment corrections, and channel parity effort. Assumptions are limited to those already in the Draft specification: pending/rejected expenses do not affect balances, approved effects occur once, participants must be active at submission, and MVP notifications may be in-app. These are not approval of the Draft specification.

## 15. Recommendation

Approve the architectural direction only after human review of the open questions and the Draft specification. Proceeding to implementation would be unsafe until at least currency/rounding boundaries, reviewer/self-approval policy, historical access, password recovery verification, receipt policy, report parameters/exports, payment corrections, and the MVP/full-product release boundary are resolved or explicitly accepted as engineering/product decisions.
