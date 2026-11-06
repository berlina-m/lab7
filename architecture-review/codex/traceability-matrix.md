# Codex Architecture Traceability Matrix

Status: proposed mapping for human review. The source specification is Draft.

| ID | Intent | Architecture/design coverage | Verification |
|---|---|---|---|
| FR-001 | Account, email/password access and recovery | Identity adapter; account use cases; authorization boundary | Account/recovery integration and unauthorized-read tests |
| FR-002 | Groups and memberships | Group aggregate, membership history, organizer policy | Group lifecycle and access-control tests |
| FR-003 | Expense fields, payer, active participants | SubmitExpense; Expense aggregate; membership validator | Valid/invalid participant and payer tests |
| FR-004 | Optional receipt image | Receipt port and authorized retrieval | Accepted/invalid/absent receipt adapter tests |
| FR-005 | Equal split and participant-order remainder | Money allocation domain service | Currency-boundary and order-sensitive JUnit 5 tests |
| FR-006 | Percentage split and reconciliation | Percentage validator/allocation service | 100%, invalid totals, rounding reconciliation tests |
| FR-007 | Configured approval before balances | Review policy/state machine and approval effect transaction | Pending/rejected/approved balance tests |
| FR-008 | Review status/history | Review Decision history and authorized views | Decision history and concurrent-review tests |
| FR-009 | Notify other group members | New-expense event and notification adapter | Member/non-member and delivery-failure tests |
| FR-010 | Pairwise balances from approved shares/payments | Immutable effects plus balance projection/query | Independent ledger reconciliation tests |
| FR-011 | Record bounded payments | Payment use case, obligation check, idempotency | Partial, duplicate, over-balance, history tests |
| FR-012 | Expense/balance/payment summaries | Authorized report/query service | Summary-to-source reconciliation tests |
| FR-013 | Date-range/parameter reports | Report query contract with explicit boundaries | Date boundary and parameter filtering tests |
| FR-014 | Web, Android, iOS product channels | Shared capability boundary plus three client adapters | Cross-channel acceptance suite |
| FR-015 | Preserve history through membership changes | Immutable transaction records and membership history | Membership-change historical-record tests |
| NFR-001 | Prevent unauthorized data access | Application authorization on all group/account/receipt reads | Own/member/non-member access matrix |
| NFR-002 | Monetary accuracy | BigDecimal, declared scale/rounding, exact effect ledger | Currency and reconciliation suite |
| NFR-003 | Understandable status/effects | Result DTOs, status views, explicit pending/approved/rejected state | Usability and UI state acceptance tests |
| NFR-004 | Consistent views | Versioned transactions/projections and consistent query snapshot | Concurrent unchanged-view tests |
| NFR-005 | Explicit action outcome | Typed success/error results and client feedback adapters | Failure/success action tests |
| NFR-006 | Capability parity across channels | Same application contracts consumed by web/mobile | FR-001–FR-013 parity matrix on all channels |

Constitution traceability: Java 21 and Maven constrain the build/runtime; Swing is confined to presentation; JUnit 5 verifies behavior; PlantUML files in this proposal document architecture; `BigDecimal` governs money; human review is required before acceptance. These are engineering constraints, not customer requirements.
