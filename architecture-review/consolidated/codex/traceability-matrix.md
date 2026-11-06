# Consolidated requirement traceability matrix

**Status:** Proposed coverage map; not implementation verification. The source specification remains Draft.

| ID | Requirement summary | Source / anchor | Architecture/design | Diagram | Verification | Status / notes |
|---|---|---|---|---|---|---|
| FR-001 | Individual account, email/password, recovery, permitted data | Draft spec §FR-001; UserRequirement accounts | Identity adapter; account use cases; authz | context, deployment | V-001 account/recovery/authz | partially covered; recovery policy open |
| FR-002 | Create/manage groups and membership | Draft §FR-002; UserRequirement groups | Group aggregate; membership history | context, domain | V-002 group/access tests | partially covered; role policy open |
| FR-003 | Expense fields, separate payer, active participants | Draft §FR-003 | Submission; membership validator; ordered participants | domain, approval | V-003 validation/payer permutations | covered design; approval pending |
| FR-004 | Optional receipt image | Draft §FR-004 | Receipt port; authorized retrieval | components, deployment | V-004 absent/valid/invalid/access | open formats/limits/retention |
| FR-005 | Equal split with participant-order remainder | Draft §FR-005; constitution IV | Minor-unit allocator | domain | V-005 allocation/order properties | partially covered; currency open |
| FR-006 | Percentage split totals 100%; shares reconcile | Draft §FR-006 | Percentage validator/residual proposal | domain | V-006 precision/residual tests | residual policy open |
| FR-007 | Configured approval before balance inclusion | Draft §FR-007 | State machine; atomic effects | approval-sequence | V-007 pending/rejected/approved-once | reviewer policy open |
| FR-008 | Review status and decision history | Draft §FR-008 | Append-only decisions; versions | domain, approval-sequence | V-008 history/authz/concurrency | design covered; verify pending |
| FR-009 | Notify group members of new expense | Draft §FR-009 | Event/outbox; notification adapter | approval-sequence, components | V-009 member/non-member/delivery | channels/retry open |
| FR-010 | Pairwise balances from shares, payer, payments | Draft §FR-010; review C-01 | Immutable effects; rebuildable projection | domain, payment-sequence | V-010 ledger examples/reconcile | accounting approval required |
| FR-011 | Record payments; reject above outstanding | Draft §FR-011 | Atomic payment/idempotency | payment-sequence | V-011 partial/duplicate/concurrent | correction policy open |
| FR-012 | Expense, balance, payment summaries | Draft §FR-012 | Authorized report service | components, deployment | V-012 source reconciliation | snapshot/currency open |
| FR-013 | Date-range and parameter reports | Draft §FR-013 | Report boundary contract | components | V-013 range/timezone tests | date/export open |
| FR-014 | Complete web, Android, iOS product | Draft §FR-014; UserRequirement OS | Shared service/API; three adapters | context, deployment | V-014 cross-channel acceptance | partial; Swing insufficient |
| FR-015 | Preserve history after membership change | Draft §FR-015 | Immutable records; separate access policy | domain, deployment | V-015 history/access tests | former-member policy open |
| NFR-001 | Prevent unauthorized account/group access | Draft §NFR-001; constitution I | Service authz every read/write | deployment | V-016 access matrix | design covered; unverified |
| NFR-002 | Monetary accuracy | Draft §NFR-002; constitution IV | BigDecimal; explicit currency policy | domain | V-005/V-006/V-010 | currency policy open |
| NFR-003 | Understandable status and balance effects | Draft §NFR-003 | Typed outcomes/status views | approval-sequence | V-017 usability/state | verification pending |
| NFR-004 | Consistent authorized views | Draft §NFR-004 | Versioned transactions/snapshots | components, payment-sequence | V-018 concurrent views | isolation open |
| NFR-005 | Confirmation/actionable error | Draft §NFR-005 | Shared result/error contract | approval/payment sequences | V-019 action outcomes | verification pending |
| NFR-006 | Capability parity across channels | Draft §NFR-006; constitution III | Shared contract and parity gate | context, deployment | V-014 all FRs on all channels | partial; Swing insufficient |
| CP-I | Requirements traceability and human authority | Constitution §I | This matrix; Draft governance | all | V-020 review audit | approval required |
| CP-II | Java 21 and Maven | Constitution §II | Prototype toolchain constraint | deployment | V-021 build/toolchain | not run |
| CP-III | OO design and Swing separation | Constitution §III | Inward core; Swing adapter | components | V-022 dependency checks | code unverified |
| CP-IV | BigDecimal exact money | Constitution §IV | Money value/allocation boundaries | domain | V-005/V-006/V-010 | policy open |
| CP-V | JUnit 5, PlantUML, traceability, human review | Constitution §V | Test plan, sources, review gate | README/all | V-023 checks | not run; review pending |

No row means implemented, tested, secure, or approved; status describes proposal coverage only.
