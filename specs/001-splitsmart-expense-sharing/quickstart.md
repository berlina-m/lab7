# SplitSmart Planning Validation Guide

This guide validates the planned behavior after implementation. It is not an implementation recipe.

Prerequisites: Java 21, Maven, and a human-approved persistence/adapter configuration. The Swing MVP is the first executable subset; web, Android, and iOS channel validation is required before claiming FR-014/NFR-006.

1. Run `mvn test` and confirm JUnit 5 domain and application tests pass.
2. Register two accounts, sign in, create a group, and verify non-member access is denied (FR-001, FR-002, NFR-001).
3. Submit a pending expense with payer separate from participants, then verify notifications, active-member validation, and no balance effect (FR-003, FR-009, FR-010).
4. Approve it once and verify ordered equal-split remainder allocation and exactly-once balance effect; retry approval and verify no duplicate effect (FR-005, FR-007, FR-010).
5. Reject a second expense and verify visible decision history and unchanged balances (FR-008).
6. Submit percentage totals other than 100% and verify actionable rejection; submit a valid split and reconcile shares (FR-006).
7. Record a valid payment and an over-balance payment; verify only the valid payment changes balance/history (FR-011).
8. Generate a summary and date-filtered report and reconcile it to authorized source records (FR-012, FR-013).
9. Repeat the core scenarios through the approved web, Android, and iOS clients before declaring the complete product compliant (FR-014, NFR-006).

The entity rules are in [data-model.md](./data-model.md); architecture and editable diagrams are in [architecture/speckit/](./architecture/speckit/).
