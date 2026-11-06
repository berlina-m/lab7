# Requirement traceability matrix — Spec Kit

Status describes proposal coverage, not implementation or verification.

| ID | Summary | Source/anchor | Components/design | Diagram | Verification proposal | Status | Notes |
|---|---|---|---|---|---|---|---|
| FR-001 | accounts/authentication | UserRequirement; spec FR-001 | identity; design modules | context, deployment | T-001 auth/recovery | covered | policy open |
| FR-002 | group creation/membership | UserRequirement; spec FR-002 | groups | domain, components | T-002 membership auth | covered | former access open |
| FR-003 | expense fields/receipt | UserRequirement; spec FR-003 | expenses, receipts | domain | T-003 validation | covered | limits open |
| FR-004 | equal allocation | UserRequirement; spec FR-004 | expenses/ledger | domain, submission | T-004 conservation | covered | order defined |
| FR-005 | percentage allocation | UserRequirement; spec FR-005 | expenses/ledger | domain | T-005 residual cases | partially covered | residual policy open |
| FR-006 | balances | UserRequirement; spec FR-006 | ledger | domain, components | T-006 reconciliation | covered | effects authoritative |
| FR-007 | payment tracking | UserRequirement; spec FR-007 | payments/ledger | payment | T-007 concurrency | covered | correction policy proposed |
| FR-008 | approval/review | UserRequirement; spec FR-008 | expenses/approval | approval | T-008 role/quorum | partially covered | product policy open |
| FR-009 | notifications | UserRequirement; spec FR-009 | outbox/notifications | components | T-009 retry/dedup | partially covered | channels open |
| FR-010 | expense reports | UserRequirement; spec FR-010 | reports | components | T-010 snapshot | partially covered | semantics open |
| FR-011 | balance reports | UserRequirement; spec FR-011 | reports/ledger | components | T-011 totals | covered | currency policy open |
| FR-012 | payment reports | UserRequirement; spec FR-012 | reports/payments | components | T-012 history | covered | timezone open |
| FR-013 | date ranges | UserRequirement; spec FR-013 | report query | components | T-013 boundaries | partially covered | timezone open |
| FR-014 | web/Android/iOS | UserRequirement; spec FR-014 | clients/service | context/deployment | T-014 channel contract | partially covered | proposal, not implementation |
| FR-015 | receipt handling | UserRequirement; spec FR-015 | receipts | components | T-015 auth/lifecycle | partially covered | policy open |
| NFR-001 | usability | spec NFR-001 | clients/presentation | context | T-016 UX review | partially covered | human usability review |
| NFR-002 | performance | spec NFR-002 | service/projection | components | T-017 load test | open decision | targets not selected |
| NFR-003 | security | spec NFR-003 | identity/authz/audit | deployment | T-018 threat test | partially covered | implementation unverified |
| NFR-004 | reliability | spec NFR-004 | transactions/outbox | sequences | T-019 failure tests | covered | operational targets open |
| NFR-005 | maintainability | spec NFR-005 | OO ports/layers | components | T-020 architecture test | covered | Java/Swing constraints |
| NFR-006 | cross-platform | spec NFR-006 | web/mobile/service | context/deployment | T-021 matrix test | partially covered | Swing alone insufficient |
| C-001 | Java21/Maven/Swing MVP | constitution | presentation/application | context | T-022 build/architecture | covered | prototype boundary |
| C-002 | BigDecimal/policy | constitution | ledger | domain | T-023 monetary properties | covered | policy decision remains |
| C-003 | JUnit5/PlantUML/traceability | constitution | verification/docs | all | T-024 review | covered | syntax validator unavailable |
| C-004 | human AI review | constitution | governance | — | T-025 approval record | covered | proposal only |

