# SplitSmart consolidated design — Codex

## Components

Clients contain views/controllers and no authoritative business rules. Application use cases expose ports for identity, groups, expenses, approval, ledger, payments, receipts, notifications, persistence, and reporting. Domain entities/value objects enforce allocation, approval, ledger, and payment invariants. Adapters implement those ports for the eventual persistence, identity, object storage, delivery, and report mechanisms. This keeps dependencies inward and supports the Swing prototype without coupling core logic to Swing.

## Records and consistency

Use immutable IDs and revisions for Account, Group, Membership, ExpenseRevision, OrderedParticipant, Allocation, ApprovalDecision, FinancialEffect, Payment, AuditEvent, and NotificationOutbox. Add uniqueness for `(revision, effectType)`, `(idempotencyKey, actor)`, and payment identity; use aggregate version checks. Financial effects are the source of truth. Rebuild a balance projection from effects and compare conservation/reconciliation totals.

## API responsibilities

Commands: submit/amend expense, decide approval, record/correct payment, manage membership, attach receipt. Queries: expense history, pairwise balances, payments, and reports. The service authenticates, authorizes, validates versions and money, applies domain rules, and returns stable retry/conflict results. Clients render responses and may cache non-authoritative data only.

## Policy boundaries

Supported currencies, minor-unit scale, accepted input, rounding, percentage residual, approval policy, historical access, receipt lifecycle, notification delivery, report semantics, and persistence are decision-log items—not invented requirements. Proposed defaults are clearly marked there.

