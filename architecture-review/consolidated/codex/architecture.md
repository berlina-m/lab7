# SplitSmart consolidated architecture proposal — Codex

Status: unapproved proposal. `spec.md` remains Draft. This independent consolidation uses the Codex proposal as its detailed baseline while preserving the Spec Kit framing and the ChatGPT review findings.

## Scope and boundaries

The customer scope is accounts/authentication, groups/membership, expenses with amount/date/description/payer/participants/optional receipt, equal and percentage splits, member notifications, configurable review, pairwise balances, payments, dated reports, and web/Android/iOS. The constitution additionally requires Java 21/Maven, OO design, strict Swing presentation/business separation, BigDecimal monetary arithmetic, JUnit 5, PlantUML, traceability, and human review. Frameworks, vendors, databases, deployment, and policy choices below are not customer requirements.

Web, Android, iOS, and Swing are clients of a logical service boundary. The Swing MVP is a deliberately limited Java 21/Maven prototype that demonstrates use cases through the same inward-facing application ports; it is not the complete product and cannot alone satisfy FR-014/NFR-006. The service owns trust-sensitive rules; clients never calculate authoritative balances or approval effects.

## Accounting and ledger

Store an expense revision with currency, integer-minor-unit-normalized total, payer, and an explicitly ordered participant list. Equal allocation uses quotient/remainder: `q=floor(T/n)`, `r=T mod n`; the first `r` recorded participants receive `q+1`. Percentage allocation validates the percentage sum and precision; residual minor units require a human decision. The recommended, unapproved policy is largest remainder with participant-order tie break.

An approved expense emits immutable allocation/effect records. For each participant allocation, create participant→payer obligation except when the participant is the payer; eliminate self-debt. Thus A pays $90 for A/B/C equally: A/B/C allocations are $30 and only B→A $30 and C→A $30 exist. A pays $90 for B/C equally: B→A $45 and C→A $45. Pairwise balances net opposing effects per currency and member pair. Payments add settlement effects after an atomic outstanding-obligation check.

Invariants are conservation of allocations, no self-obligation, no pending/rejected effect, exactly-once approved effect batch, no payment above outstanding amount, and projection reconciliation to immutable effects. Money never uses float/double; scale, accepted input, rounding, currency support, and percentage precision are explicit policy boundaries awaiting decision.

## Approval, transactions, and corrections

Proposed state machine: PENDING→APPROVED or REJECTED; rejection/resubmission creates a new revision and preserves history. Reviewer eligibility, self-approval, single/multiple reviewers, quorum, and multi-level rules remain open. `DecideExpense` runs one transaction: authenticate/authorize, version-check, insert a unique decision by idempotency key, transition state, and if final approval insert a unique effect batch keyed by revision. Duplicate retries return the original result; concurrent decisions conflict or return already-decided.

`RecordPayment` runs one transaction: lock/version-check, re-read effects, validate parties/currency/remaining amount, insert unique payment and settlement effect, commit. Rollback prevents partial changes. The append-only effect ledger is authoritative; balance projections are rebuildable and reconcilable. Corrections are compensating payment/refund, expense revision, or approval reversal, never destructive financial edits (recommendation pending approval).

## Security and auxiliary capabilities

Identity handles password recovery, token/session lifecycle, revocation, rate limits, and audit. Each read/write enforces membership, role, resource ownership, former-member policy, and receipt authorization server-side. Former-member historical access is explicitly undecided and separate from record retention. Receipt type/size/content validation, retention/deletion, notification channels/preferences/retry/deduplication, report timezone/date boundaries/currency aggregation/snapshot/export are all open decisions. Notification failure is decoupled from a committed financial outcome.

PlantUML sources define context, dependency direction, domain, approval/payment transactions, and trust boundaries. Syntax validation was not run because no validator was available. No implementation, build, deployment, security, or runtime verification is claimed.

