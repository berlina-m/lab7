# SplitSmart consolidated design — Spec Kit

## Bounded modules

`identity` exposes authenticate/recover/revoke and current-principal ports. `groups` owns membership roles and membership history. `expenses` owns immutable submission revisions, ordered participants, allocation policies, receipt references, and approval state. `ledger` owns allocation/effect batches and pairwise reconciliation. `payments` owns validated settlement effects. `notifications`, `receipts`, and `reports` are ports with asynchronous adapters where appropriate. `presentation` contains web/mobile/Swing controllers and view models only. Application use cases depend on ports; infrastructure implements ports.

## Data and commands

Core records include Account, Group, Membership (active/former and role), ExpenseRevision (payer, ordered participant IDs, amount/currency, allocation mode, percentages, receipt reference), Allocation, ApprovalDecision, FinancialEffect, Payment, AuditEvent, and NotificationOutbox entry. IDs are immutable. Monetary values carry currency and explicit scale. Ordered participant persistence is mandatory because equal-split remainders depend on it.

Commands include SubmitExpense, DecideExpense, RecordPayment, CorrectPayment, AmendExpense, ReverseApproval, and ManageMembership. Queries include PairwiseBalances, ExpenseHistory, PaymentHistory, and Report. Every retryable command accepts an idempotency key; optimistic version is required for mutable aggregate commands.

## Worked allocation policy

Equal: total minor units `T`, `n` participants, `q=floor(T/n)`, `r=T mod n`; participant positions 1..r receive q+1. Percentage: validate sum and precision, compute exact shares, round using the chosen policy, and allocate residual deterministically. The policy and supported currencies remain open; recommended choices are recorded only in the decision log.

## Transaction table

| Operation | Atomic boundary | Uniqueness/source of truth |
|---|---|---|
| Approval completion | decision + state transition + effect batch + outbox event | expense revision/version and idempotency key; financial effects authoritative |
| Payment | obligation re-read + payment + settlement effect + outbox event | payment idempotency key; effect ledger authoritative |
| Projection rebuild | read all effects, replace/rebuild projection | effect IDs and reconciliation totals |

Receipt upload is staged and virus/content validated before attachment; authorization is checked on every retrieval. Reporting selects a consistent snapshot and labels unresolved timezone/currency/export policies.

## Channel boundary

All channels use the same application contracts and server-side rules. The Swing MVP can use an in-process adapter for demonstration, but its port boundary must remain visible and its scope must not be represented as the full product. Client notifications are never the authority for approval or balance changes.

