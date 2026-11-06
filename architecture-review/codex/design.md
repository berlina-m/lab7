# SplitSmart Independent Codex Design Proposal

**Status:** Proposed design; not approved and not implementation code.

## Design principles

The design keeps commands and queries explicit, preserves immutable business history, uses `BigDecimal` at every monetary boundary, and makes every state-changing use case return a success or actionable error. Client controllers call application ports; they never calculate shares, mutate balances, or decide authorization.

## Use-case contracts

Representative commands are `RegisterAccount`, `RecoverAccountAccess`, `CreateGroup`, `ChangeMembership`, `SubmitExpense`, `ReviewExpense`, and `RecordPayment`. Representative queries are `GetGroupBalances`, `GetExpense`, `GetNotifications`, and `GenerateReport`. Each carries an authenticated actor, a request/idempotency key where state changes, and validated identifiers. Results contain a stable outcome, user-facing error code/message data, and a traceable event or version when successful.

The application service performs authorization and orchestration, then calls domain methods. Domain methods return typed validation/state errors. Adapters translate transport/storage errors without changing financial rules.

## State models

Expense status is `PENDING → APPROVED` or `PENDING → REJECTED`; terminal states cannot be changed by a repeated request. An approval effect has a unique expense identity and is created transactionally with approval. Notification state is separate from expense state. Payment is recorded only after its obligation check passes; a later correction policy is intentionally open.

Membership has active/inactive history. New participants are checked against active membership at submission. Existing expense and payment records retain their original account IDs and amounts after membership changes.

## Money model

Every amount is a `BigDecimal` paired with a currency code. A future approved currency policy must declare minor-unit scale, accepted input scale, rounding mode, percentage precision, and allocation boundary. The design rejects malformed, non-positive, over-precision, and currency-incompatible values.

Equal allocation converts the amount to declared minor units, assigns the integer base share, and gives one remaining minor unit to each participant in recorded order until exhausted. Percentage allocation validates non-negative percentages totaling 100%, computes with explicit scale/rounding, then reconciles to the total at the currency boundary using a documented deterministic policy. No `double` or `float` is permitted for monetary values.

## Balance ledger and exactly-once effects

An approved expense produces immutable obligation/effect entries: each participant owes their share and the recorded payer receives the amount paid. If payer and participant are the same person, the resulting self-obligation is omitted or netted by a documented domain rule; this case needs explicit product confirmation. A payment applies only to the specified payer-recipient direction and cannot exceed its current outstanding amount. Pairwise display can net entries but must retain source history.

## Authorization and history policy

Authorization is evaluated in the application layer and repeated for reads of expenses, shares, receipts, notifications, balances, payments, and reports. Organizer/reviewer permissions are policy objects/configuration rather than hardcoded UI behavior. The product owner must decide whether a removed member can see historical records; the default design is to preserve records and enforce a disclosed access policy without deleting transactions.

## Receipt and notification ports

Receipt handling is an optional port with `attach`, `retrieveAuthorized`, and `delete/retention` semantics to be selected. It must reject unsupported/invalid images and avoid silently accepting a failed attachment. Notification publication targets in-application notifications for the Swing MVP proposal. Email, push, preferences, retries, and delivery providers are open for the full product.

## Failure and concurrency behavior

Validation errors are returned before state mutation. Persistence conflicts return a retryable conflict outcome. A duplicate request with the same idempotency key returns the original outcome where safely available. Review approval and payment checks use a transaction and version/constraint protection. Notification failure is recorded as delivery failure and retried or surfaced; it does not turn an approved expense into rejected or vice versa.

## Reports

Reports read only authorized records and include their effective date range and parameters. Date boundaries must be explicitly chosen (recommended: inclusive start, exclusive end) before acceptance. Reports must reconcile to the same approved-effect and payment source used by balances. Export is not designed until the product owner specifies formats.

## Channel design

The Swing prototype uses presenters/controllers around application interfaces. Web, Android, and iOS each supply channel-specific presentation and transport adapters over the shared capability boundary. The full product is complete only when FR-001–FR-013 are verified through all three requested channels; the prototype is a learning/release subset and does not claim FR-014/NFR-006.

## Verification design

Test fixtures create accounts, memberships, expenses, review decisions, effects, payments, and reports through application ports. Every test names one or more FR/NFR IDs. Property-style datasets should cover participant order permutations, currency boundaries, percentage totals, repeated review/payment requests, concurrent review, membership removal, notification failure, unauthorized reads, and report boundary dates. JUnit 5 is mandatory under the constitution.
