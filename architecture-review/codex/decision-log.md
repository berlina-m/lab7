# Codex Architecture Decision Log

All entries are **proposed/open**. None is accepted without human approval.

| ID | Status | Context | Options | Proposed decision and rationale | Consequences / approver |
|---|---|---|---|---|---|
| ADR-001 | Proposed | Multiple client channels need one rule set | Per-client logic; shared service; microservices | Use shared application/domain contracts with ports and adapters; minimizes rule drift and operational complexity | Requires channel adapters; architect + product owner |
| ADR-002 | Proposed | Swing is constitutionally required but customer asks web/mobile | Swing as product; Swing subset; parallel systems | Treat Swing as labelled prototype sharing the domain boundary; it cannot satisfy FR-014/NFR-006 | Full product still needs three clients; product owner |
| ADR-003 | Open | Persistence is unspecified | Relational; document; in-memory | Use in-memory for isolated prototype tests and evaluate relational persistence for full product because transactional history/effects matter | Database, hosting, migration remain open; architect |
| ADR-004 | Open | Currency and rounding policy absent | One currency; configured list; broad currency support | Require an approved currency policy before implementation; use `BigDecimal` and explicit minor-unit allocation | Product decision blocks final money contract; customer/product owner |
| ADR-005 | Proposed | Approval must affect balances exactly once | Direct recalculation; immutable effect ledger; event-only | Create unique immutable approved-effect entries transactionally with approval | Needs transaction/constraint support; architect |
| ADR-006 | Open | Reviewer permissions/self-approval unclear | Submitter may self-approve; separate reviewer; quorum | Keep reviewer policy configurable and prohibit/allow self-approval only after product decision | State machine and UX depend on answer; product owner |
| ADR-007 | Proposed | Payment overage must not mutate data | Allow overage; reject; cap silently | Reject above current payer-recipient outstanding balance, preserving the clarification and auditability | Corrections and race handling still need policy; product owner |
| ADR-008 | Open | Notification delivery channels unspecified | In-app only; email; push; combinations | In-app is the MVP proposal; isolate delivery adapters and defer full channels | Delivery reliability/preferences remain open; product owner |
| ADR-009 | Open | Historical access after membership changes unclear | Retain access; revoke access; role-based archive access | Preserve records and require an explicit disclosed access policy | Privacy/product policy required; customer/product owner |
| ADR-010 | Open | Receipt and report formats/limits absent | Define defaults; defer; configurable policy | Use ports and validation without inventing limits or exports | Product decisions required before acceptance; customer/product owner |
