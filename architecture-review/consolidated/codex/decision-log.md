# Decision and risk register — Codex

No decision below is approved by this proposal.

| ID | Decision and impact | Options | Unapproved recommendation | Owner | Status |
|---|---|---|---|---|---|
| C-01 | payer/participant and self-debt | include self; eliminate self | eliminate self, ordered remainder | Product/finance | proposed |
| C-02 | currencies/scale/rounding | fixed/metadata/arbitrary | explicit currency policy before release | Product/finance | open |
| C-03 | percentage residual | reject/largest remainder/order | largest remainder + order tie | Product/finance | open |
| C-04 | service/API/channels | client rules/shared service | shared server-side rules | Architecture | proposed |
| C-05 | reviewer roles/quorum/self-approval | variants | group-configured policy | Product | open |
| C-06 | approval transaction/idempotency | retries/unique transaction | unique decision/effect batch | Architecture | proposed |
| C-07 | payment concurrency/idempotency | last write/lock/version | re-read + lock/version + key | Architecture | proposed |
| C-08 | payment correction/refund | edit/compensation | compensating entry | Finance | proposed |
| C-09 | historical access after removal | deny/retain/role | retention separate from auth | Product/legal | open |
| C-10 | identity/recovery | session/token policies | revocation, rate limits, audit | Security | open |
| C-11 | receipt lifecycle | formats/limits/retention | validated authorized object reference | Product/security | open |
| C-12 | notifications | channels/sync/async | outbox retry/dedup; channels open | Product | open |
| C-13 | reports | timezone/currency/export variants | consistent snapshot; policy open | Product/finance | open |
| C-14 | persistence | relational/document/in-memory | relational source; confirm | Architecture | open |
| C-15 | balance source | projection/effects | effects + rebuildable projection | Architecture | proposed |
| C-16 | MVP boundary | Swing product/prototype | limited Swing prototype | Product | proposed |

Risks: allocation policy errors (medium/high—property tests and policy decision); duplicate financial effects (medium/high—constraints and transaction tests); unauthorized history/receipts (medium/high—central auth/audit); notification retries (medium/medium—outbox backoff); projection drift (medium/high—rebuild/reconcile); confusion between MVP and full product (high/high—release gate and explicit docs).

