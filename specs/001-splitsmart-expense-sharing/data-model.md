# SplitSmart Data Model

All monetary values use a currency-aware `BigDecimal` value with explicit scale and rounding at allocation boundaries. The supported-currency policy remains open.

| Entity | Key fields and rules | Relationships |
|---|---|---|
| Account | id, email, password credential reference, status | owns memberships, submits/reviews expenses, sends/receives payments |
| Group | id, name, approval configuration | has memberships, expenses, notifications |
| Membership | account, group, role, active status, effective dates | controls current access and participant eligibility |
| Expense | id, group, submitter, payer, amount, currency, date, description, split method, status, version | has ordered participants/shares, receipt reference, review decisions |
| Expense Share | expense, participant, percentage or allocated amount, participant order | contributes obligation to approved balance calculation |
| Review Decision | expense, reviewer, decision, timestamp, reason | records review history; valid transition applies effects once |
| Notification | recipient, group, expense reference, type, delivery/status, timestamp | delivered only to authorized active members at creation |
| Balance | account pair, currency, net amount/version | derived from approved expense effects minus payments |
| Payment | payer, recipient, group/context, amount, currency, date, status | reduces the applicable outstanding balance; preserves history |
| Report | requester, group/context, parameters, period, generated results | reads authorized expenses, balances, and payments |

State transitions: Expense `PENDING -> APPROVED` or `PENDING -> REJECTED`; terminal states cannot apply a second effect. Membership changes do not mutate historical transactions. Payment correction policy is open; no destructive rewrite is assumed.

Validation includes positive, currency-valid amounts; active participants at submission; non-negative percentages totaling exactly 100%; equal allocation in recorded participant order; authorized group access; and payment amount no greater than current outstanding payer-to-recipient balance.
