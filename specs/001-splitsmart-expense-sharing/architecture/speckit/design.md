# SplitSmart Design Proposal — Spec Kit

## Package/module design

`presentation.swing` contains views/controllers only. `application` contains use-case coordinators and DTO/port contracts. `domain` contains entities, value objects, allocation, approval, balance, payment, and report policies. `infrastructure` contains repository, credential, receipt, and notification implementations. The dependency rule is inward toward domain.

## Workflow designs

Submission validates membership, payer, amount, split, and receipt; persists PENDING; then creates authorized notifications. Approval atomically checks PENDING and reviewer authority, records the decision, and applies one balance effect. Rejection records history without an effect. Balance calculation credits the payer and debits each participant share, then subtracts recorded payments. Payment validation occurs against the current pairwise outstanding amount before recording.

## Monetary design

Use a currency-aware BigDecimal value object. Currency minor units, accepted scale, and rounding mode require approval. Equal allocation divides at the minor-unit boundary and distributes remainder in stored participant order. Percentage allocation validates exact 100% at the defined percentage precision, rounds shares at the currency boundary, and reconciles any permitted rounding difference by an explicit approved policy.

## Full product versus MVP

The Swing MVP can exercise the core use cases and in-application notifications. The full product additionally needs approved channel adapters and a shared service/data boundary so the web, Android, and iOS clients expose the same capabilities and authorization. Client technology and deployment are open decisions.

## Sequence references

See editable context, component, domain, and workflow diagrams in `diagrams/`. The diagrams deliberately label unselected storage and delivery choices as ports rather than inventing vendors.
