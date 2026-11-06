# Open Questions and Risks

## Customer or product-owner decisions

1. Which currencies, minor-unit scales, input precision, and rounding modes are supported?
2. How are percentage residual minor units allocated after rounding?
3. Which members may review, may a submitter review their own expense, and are quorum/multi-level approvals required?
4. What notification channels beyond MVP in-app notifications are required, and are preferences/quiet periods needed?
5. Which receipt formats, size/retention limits, and post-membership access rules apply?
6. Which report parameters and export formats are required, and what are date-boundary semantics?
7. How are edited expenses, payment corrections, refunds, and duplicate submissions handled?
8. What password-recovery verification and account-recovery safeguards are required?
9. What release boundary and acceptance criteria distinguish the Swing prototype from the complete web/Android/iOS product?

## Engineering decisions requiring approval

- Relational versus another persistence model and the deployment topology.
- API/transport and web/mobile frameworks.
- Identity/session implementation and receipt storage adapter.
- Whether balances are a maintained projection or rebuilt from an immutable effect ledger.
- Transaction isolation, retry policy, notification retry mechanism, and observability approach.

## Risks

| Risk | Impact | Mitigation |
|---|---|---|
| Currency policy is absent | Incorrect allocations or unusable acceptance tests | Resolve scale/rounding before final money implementation; keep all arithmetic `BigDecimal`. |
| Reviewer policy is ambiguous | Unauthorized or contradictory balance effects | Model explicit state/version checks and obtain policy approval. |
| Membership history access is unclear | Privacy breach or inaccessible audit history | Preserve records and make access policy explicit and testable. |
| Duplicate/concurrent approval or payment | Double financial effects | Transactional unique effect/payment keys and optimistic version checks. |
| Channel parity is underestimated | Swing prototype falsely appears complete | Require cross-channel FR/NFR acceptance matrix. |
| Notification failure is coupled to financial state | Users see misleading status or missing alerts | Separate event delivery from transaction outcome and expose retries/failure. |
| Receipt/report requirements expand late | Rework in adapters and UI | Keep ports stable and resolve formats/limits before acceptance. |

No implementation code is authorized by this document. Human review is required under the constitution.
