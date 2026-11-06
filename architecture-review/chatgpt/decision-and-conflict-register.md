# SplitSmart Decision and Conflict Register

**Purpose:** Record decisions needed to consolidate Spec Kit and Codex proposals.  
**Status:** All entries are proposed/open pending human approval.  
**Authority:** Customer scope comes from `UserRequirement.md`; clarified behavior comes from the Draft `spec.md`; architecture choices are proposals, not requirements.

## A. Decisions requiring approval

| ID | Topic | Current position in proposals | Decision needed | Suggested direction (not approved) | Owner | Priority / gate |
|---|---|---|---|---|---|---|
| DEC-001 | Payer/participant accounting | Payer is separate; may or may not be participant; balance effect described at high level | Define exact ledger postings and self-obligation treatment | Model each expense as payer credit and participant debits; net/omit self-obligation; retain source entries | Product owner + architect | Critical; before financial implementation |
| DEC-002 | Currency support | Currency-aware BigDecimal; currency list/scale open | Supported currencies, minor units, accepted input scale, rounding mode | Approve a finite initial currency policy and enforce at domain boundary | Product owner | Critical |
| DEC-003 | Percentage residual allocation | Percentages total 100%; shares reconcile after currency rounding | Define precision and deterministic residual-cent allocation | Define a deterministic policy and document tie-breaking; test exact reconciliation | Product owner + architect | Critical |
| DEC-004 | Full product service/API boundary | Shared application/domain boundary and web/mobile clients; concrete service/API details open | Define trusted service, client contracts, identity boundary, and runtime responsibilities | Shared service boundary with clients as adapters; defer frameworks/vendors | Architect | Critical |
| DEC-005 | Approval permissions | Group-configured process; one or multiple reviewers possible | Roles, self-approval, quorum, multi-level review, concurrent decisions | Explicit policy object/state machine; no implicit self-approval rule | Product owner | High |
| DEC-006 | Transactional approval effects | Both require exactly-once approved balance effects | Define atomic transaction, unique effect identity, retry behavior | Commit review decision and unique effect set atomically | Architect | High |
| DEC-007 | Payment consistency | Reject above outstanding balance; Codex adds idempotency | Define transaction isolation, concurrency, duplicate request response | Check obligation and record payment in one transaction with idempotency key | Architect | High |
| DEC-008 | Payment corrections/refunds | Open | Define reversal/correction workflow and audit history | Use compensating entries; avoid destructive financial-history edits | Product owner + architect | High |
| DEC-009 | Historical access | Preserve historical records; access policy open | Decide what former members can view, including receipts and payments | Separate immutable retention from explicit authorization policy | Product owner | High |
| DEC-010 | Identity/recovery security | Email/password and recovery required; safeguards open | Recovery verification, session/token policy, rate limits, account enumeration behavior | Use trusted identity boundary; never store plaintext passwords | Security reviewer + product owner | High |
| DEC-011 | Receipt lifecycle | Optional receipt image behind adapter | Formats, size, validation, retention/deletion, access | Define allowed types/limits and authorized retrieval; defer storage vendor | Product owner + security reviewer | Medium |
| DEC-012 | Notifications | In-app MVP proposed; future channels open | Channels, preferences, retries, deduplication and delivery expectations | Keep notifications separate from expense financial state; define MVP delivery guarantee | Product owner | Medium |
| DEC-013 | Reporting semantics | Date range/parameters; Codex suggests inclusive start/exclusive end | Timezone, date boundaries, currencies, snapshot and export formats | Adopt explicit half-open interval only after product approval | Product owner + architect | Medium |
| DEC-014 | Persistence | Repository ports; relational store considered but not selected | Select persistence model and transaction guarantees | Evaluate relational storage for transactional financial history; no vendor preselection | Architect | High before implementation |
| DEC-015 | Balance projection | Projection or recomputation discussed | Define source of truth and rebuild/reconciliation mechanism | Immutable approved-effect ledger is authoritative; projection is rebuildable | Architect | High |
| DEC-016 | MVP/full-product release boundary | Swing prototype is subset; web/mobile required full target | Define which capabilities are in Swing MVP and how full-product acceptance is measured | Maintain separate MVP acceptance and full-product channel-parity gates | Product owner | Critical for scope governance |

## B. Proposal conflicts and alignment issues

| ID | Area | Spec Kit | Codex | Resolution |
|---|---|---|---|---|
| CON-001 | Architecture depth | Concise dependency-inverted core | More detailed ports/adapters and modular monolith | Compatible; use Codex as baseline and retain Spec Kit's concise framing |
| CON-002 | Traceability granularity | Groups FR ranges and NFRs | One row per FR/NFR | Prefer Codex granularity; retain individual IDs and test links |
| CON-003 | Domain model detail | Core entities named, limited relationship semantics | More detailed entities, status and relationship descriptions | Use Codex detail, but add ordered participant relation and financial postings |
| CON-004 | Test strategy | JUnit 5 categories | More comprehensive domain/application/adapter/cross-channel strategy | Adopt Codex structure and add explicit requirement-linked test IDs |
| CON-005 | Approval/payment diagrams | Sequential calls; prose says atomic | More transaction detail, but transaction scope can be clearer | Revise both diagrams to show atomic commit, rollback and uniqueness |
| CON-006 | Dependency diagram | Generic infrastructure implements ports | Some arrows point from application services to concrete adapters despite prose | Correct Codex diagram: application defines ports; infrastructure implements them |
| CON-007 | MVP/full product | Clearly labels Swing subset | Clearly labels Swing subset | Keep both proposals' distinction; do not imply Swing satisfies FR-014/NFR-006 |
| CON-008 | Percentage rounding | Explicit approved policy required | Deterministic reconciliation proposed, policy open | No actual conflict; require product decision before implementation |

## C. Open risks

| Risk ID | Risk | Consequence | Mitigation |
|---|---|---|---|
| RSK-001 | Ambiguous accounting model | Incorrect or unreconcilable balances | Approve worked ledger examples and invariant tests |
| RSK-002 | Currency policy unresolved | Different layers calculate different amounts | Centralize currency/rounding policy in domain value objects |
| RSK-003 | Concurrent approvals/payments | Duplicate or over-applied financial effects | Transactions, versions, unique constraints, idempotency |
| RSK-004 | Former-member access unclear | Privacy exposure or inaccessible records | Explicit historical access policy and tests |
| RSK-005 | Swing mistaken for complete product | Customer scope not delivered | Separate release gates and cross-channel acceptance |
| RSK-006 | Notification failure coupled to transaction | Misleading expense status or lost alerts | Durable event/delivery state separate from financial outcome |
| RSK-007 | Receipt/report details decided late | Rework and security gaps | Approve policies before acceptance; retain adapter boundaries |
| RSK-008 | Plan retains template scaffolding | Implementation tasks based on incomplete plan | Finish technical context and remove template comments before task generation |

## D. Consolidation choices recommended

1. Use Codex's ports-and-adapters / modular-monolith explanation as the baseline, with an explicit logical service/API boundary.
2. Use Codex's one-row-per-requirement matrix.
3. Preserve Spec Kit's concise diagrams as references, but update all diagrams to the approved model and transaction semantics.
4. Make the ordered participant list and payer relationship explicit in the domain model.
5. Make the approved-effect ledger authoritative and any balance projection rebuildable, subject to architecture approval.
6. Keep all unresolved customer decisions marked open; do not silently turn proposals into requirements.
7. Keep `UserRequirement.md`, constitution, and Draft `spec.md` unchanged during architecture consolidation unless an approved requirements change is separately authorized.

## E. Human approval record

| Decision ID | Approved / rejected / deferred | Approver | Date | Notes |
|---|---|---|---|---|
| DEC-001 through DEC-016 | Pending |  |  |  |

No decision in this register is approved by virtue of appearing here.
