# External Interface Boundaries

No concrete API, wire format, database, or vendor is selected by this Draft plan. The approved implementation must expose equivalent use-case contracts for account access, groups, expenses/review, notifications, balances, payments, and reports to the Swing, web, Android, and iOS presentation adapters.

Each contract must enforce authorization at the boundary, return explicit success or actionable failure, carry currency and precision metadata for monetary values, and preserve expense/review/payment history. Concrete endpoint or client schemas require a later approved design decision.
