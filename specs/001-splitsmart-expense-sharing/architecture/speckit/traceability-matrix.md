# Requirement Traceability Matrix

| IDs | Architecture/design coverage | Verification |
|---|---|---|
| FR-001, NFR-001 | Account access and authorization policy | account/recovery and access-control tests |
| FR-002, FR-015 | Group/Membership with immutable historical references | membership and history tests |
| FR-003–FR-006 | Expense, ordered shares, AllocationService, receipt port | validation, equal, percentage, receipt tests |
| FR-007–FR-008 | Approval state machine, decision history, idempotent effect | concurrent approval and status tests |
| FR-009 | Notification port scoped to active group members | member/non-member delivery tests |
| FR-010–FR-011 | Balance projection and Payment use case | reconciliation and over-balance tests |
| FR-012–FR-013 | Reporting use case with authorization and parameters | summary/date-range tests |
| FR-014, NFR-006 | Shared use-case contract plus web/Android/iOS adapters; Swing explicitly insufficient | cross-channel acceptance tests |
| NFR-002 | BigDecimal money value and explicit currency policy | arithmetic/property tests |
| NFR-003 | Explicit status/effect presentation DTOs | usability/acceptance tests |
| NFR-004 | versioned transaction reads and consistent projections | concurrent-view tests |
| NFR-005 | result/error contract on every use case | application acceptance tests |

Constitution traceability: Principles I–V are addressed by this matrix, layered design, BigDecimal rules, JUnit 5, PlantUML, and human approval status.
