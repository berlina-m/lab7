# Open Questions and Risks

## Product decisions

Currencies/precision/rounding; reviewer roles, self-approval and quorum; notification channels/preferences; receipt formats, limits, and post-membership access; report parameters/exports; payment and post-submission corrections; password recovery verification; and Swing MVP release boundary.

## Engineering decisions

Persistence technology, service/API shape, deployment, client frameworks, concurrency mechanism, receipt storage, and notification adapters remain proposals until selected.

## Risks

- Draft requirements may change after architecture work.
- A Swing-only delivery could be mistaken for satisfying the full product.
- Ambiguous currency and rounding rules can cause irreconcilable balances.
- Concurrent review/payment operations can duplicate effects without transaction and idempotency controls.
- Membership changes can expose or hide historical data unless access policy is explicit.
- Notification failures can create misleading user expectations unless status and delivery are separated.

Human review and source-document preservation are required before task generation or implementation.
