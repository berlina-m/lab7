# SplitSmart Requirement Coverage Matrix — Consolidated Review

**Branch reviewed:** `draft/architecture-design-prompts`  
**Specification status:** Draft  
**Purpose:** Cross-check proposed architecture coverage; not implementation verification.

**Status meanings**
- **Covered:** proposal identifies an architecture/design element for the requirement.
- **Covered — decision open:** design intent exists, but a product/engineering policy remains unresolved.
- **Covered — verification pending:** proposal identifies a verification approach, but no test evidence has been supplied.

| ID | Requirement summary | Spec Kit design coverage | Codex design coverage | Verification expected | Review status |
|---|---|---|---|---|---|
| FR-001 | Individual account, email/password sign-in, recovery, own data | Account/access boundary | Account use cases and identity adapter | Register/login/recovery integration; cross-account authorization tests | Covered — decision open |
| FR-002 | Create/manage groups and membership | Group/Membership components | Group aggregate, membership history and roles | Group lifecycle, role, active/inactive membership tests | Covered — decision open |
| FR-003 | Expense details, separate payer, active participants | Expense and allocation service | Expense aggregate and membership validator | Valid/invalid payer and participant tests; payer-participant permutations | Covered — verification pending |
| FR-004 | Optional receipt image | Receipt port | Receipt port with authorized retrieval | With/without/invalid receipt tests; access and retention tests | Covered — decision open |
| FR-005 | Equal split with remainder cents in participant order | Ordered allocation rule | Minor-unit allocation in stored participant order | Property tests for amounts, participant counts and order permutations | Covered — decision open |
| FR-006 | Percentage split, total 100%, shares reconcile | Percentage validation and currency reconciliation | Percentage validator and deterministic residual proposal | 100%, invalid totals, precision and residual tests | Covered — decision open |
| FR-007 | Group-configured approval before balance impact | Approval policy/state transition | Review policy/state machine and transactional effect | Pending/rejected no effect; approved exactly once; policy matrix | Covered — decision open |
| FR-008 | Review status and decision history | Decision history | Review Decision entity, version and history | Status visibility, history authorization, concurrent review tests | Covered — verification pending |
| FR-009 | Notify group members of new expenses | Notification port | Domain event and notification adapter | Other members notified; non-members receive no details; delivery failure | Covered — decision open |
| FR-010 | Pairwise balances from approved expenses, payer, payments | Balance service/projection | Immutable effect ledger and balance projection | Independent ledger reconciliation; payer-is-participant and payer-not-participant examples | Covered — decision open |
| FR-011 | Record payments, reject above outstanding | Payment use case and outstanding check | Transactional payment use case and idempotency | Partial, duplicate, concurrent, over-balance, correction tests | Covered — decision open |
| FR-012 | Expense, balance and payment summaries | Reporting use case | Authorized report/query service | Reconcile totals to source entries and defined snapshot | Covered — verification pending |
| FR-013 | Date range and other report parameters | Report parameter boundary | Report query; inclusive-start/exclusive-end proposed | Empty/reversed/boundary ranges, timezone and parameter tests | Covered — decision open |
| FR-014 | Primary web plus Android and iOS apps | Web/Android/iOS adapters identified | Web/Android/iOS client adapters identified | End-to-end acceptance for required features on all three channels | Covered — architecture incomplete |
| FR-015 | Preserve historical expense/payment records after membership change | Historical references preserved | Immutable transaction history | Membership changes do not rewrite records; access policy tests | Covered — decision open |
| NFR-001 | Prevent unauthorized account/group access | Authorization policy | Application authorization on scoped reads | Own/member/non-member matrix including receipts/payments/reports | Covered — decision open |
| NFR-002 | Monetary accuracy | BigDecimal and explicit currency boundary | BigDecimal, declared scale/rounding, reconciliation | Currency boundary, allocation and ledger invariants | Covered — decision open |
| NFR-003 | Understandable status and balance effects | Status/effect presentation intent | Result DTOs and explicit status/error intent | Usability tests identify pending/approved/rejected and balance impact | Covered — verification pending |
| NFR-004 | Consistent results for authorized members | Versioned transaction/projection concept | Versioned transactions and consistent query snapshot | Concurrent reads and projection consistency tests | Covered — decision open |
| NFR-005 | Confirmation or actionable error after actions | Use-case result/error intent | Typed outcomes and actionable errors | Success/failure tests for each state-changing and report action | Covered — verification pending |
| NFR-006 | Complete capability set across web, Android, iOS | Shared use-case contract and three adapters | Shared application contract and three clients | Cross-channel feature parity acceptance matrix | Covered — architecture incomplete |

## Constitution traceability

| Principle | Architecture evidence | Required verification / review |
|---|---|---|
| I. Customer requirements and traceability | FR/NFR matrix; customer scope separated from implementation choices | Keep one row per requirement; map each to design and test IDs |
| II. Java 21 and Maven | Java 21/Maven MVP context in both proposals | Maven build and Java 21 toolchain verification |
| III. OOP and Swing separation | Presentation → application → domain; infrastructure adapters | Dependency checks; confirm Swing event handlers contain no business rules |
| IV. Exact monetary arithmetic | BigDecimal and explicit allocation boundaries | No float/double monetary arithmetic; approved currency policy tests |
| V. JUnit 5, PlantUML, human review | JUnit 5 test plans, editable diagrams, unapproved status | Run tests/build; review diagram diffs; obtain human approval before acceptance |

## Important limitation

A “Covered” status means the proposal mentions a suitable design element. It does not mean the feature is implemented, tested, secure, or accepted. No implementation source code or executed test results were part of this review.
