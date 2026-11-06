# Decision and risk log — Spec Kit

All entries are proposals/open decisions; none are approved by this document.

| ID | Topic / why it matters | Options | Recommendation (unapproved) | Owner | Status | Evidence |
|---|---|---|---|---|---|---|
| D-001 | Payer/participant accounting | gross self rows; eliminate self-debt | eliminate self-obligation; ordered minor-unit split | Product + finance | proposed | UserRequirement; Draft spec |
| D-002 | Currency, scale, rounding | fixed supported set; ISO metadata; arbitrary | explicit supported set and minor-unit policy | Product/finance | open | review register |
| D-003 | Percentage residual | reject; largest remainder; payer absorbs | largest remainder + order tie break | Product/finance | open | review register |
| D-004 | API/channels | clients own rules; shared service | shared server-side application boundary | Architecture | proposed | FR/NFR scope |
| D-005 | Approval roles | submitter; group role; quorum | group-configured reviewer policy | Product | open | Draft spec |
| D-006 | Approval idempotency | client retry only; unique effect batch | transaction + unique keys/version | Architecture | proposed | critical finding |
| D-007 | Payment concurrency | last-write-wins; lock/version | transaction re-read + optimistic/pessimistic control | Architecture | proposed | critical finding |
| D-008 | Payment correction/refund | edit; compensating entry | append-only compensation | Finance | proposed | review register |
| D-009 | Former-member access | deny; retain; role-based | retention separate from authorization | Product/legal | open | review register |
| D-010 | Identity/recovery | session/token variants | managed policy with revocation/rate limits | Security owner | open | NFR |
| D-011 | Receipt lifecycle | formats/limits/retention choices | validated object reference + per-read authorization | Product/security | open | review register |
| D-012 | Notifications | push/email/in-app; sync/async | outbox + retry/dedup; channels open | Product | open | FR-009 |
| D-013 | Reporting semantics | timezone/currency/export variants | snapshot query; policy open | Product/finance | open | review register |
| D-014 | Persistence | relational/document/in-memory MVP | relational source of truth; confirm | Architecture | open | plan/data model |
| D-015 | Balance source | projection only; effects + projection | append-only effects + rebuildable projection | Architecture | proposed | critical finding |
| D-016 | MVP boundary | Swing as product; Swing prototype | limited Java/Swing prototype; full channels later | Product | proposed | constitution + FR-014 |

Risks: wrong rounding or residual policy (medium/high; decide currency policy and property-test conservation; product/finance); duplicate approval/payment effects (medium/high; unique constraints, transaction tests; architecture); unauthorized receipt/history access (medium/high; centralized authorization and audit; security/legal); notification retry storms (medium/medium; outbox backoff/dedup; operations); projection drift (medium/high; reconciliation/rebuild jobs; architecture); prototype scope confusion (high/high; explicit release gate/docs; product).

