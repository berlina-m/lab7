# SplitSmart Architecture Consolidation Instructions

**Repository:** `berlina-m/lab7`  
**Required branch:** `draft/architecture-design-prompts`  
**Purpose:** Direct Spec Kit and Codex to produce independently generated, requirement-traceable consolidated architecture proposals using the ChatGPT review package as review guidance.  
**Status:** Instructions only. These instructions do not approve the Draft specification, resolve product decisions, or authorize implementation.

---

## 1. Task objective

Create a consolidated SplitSmart architecture and design proposal that reconciles the existing Spec Kit and Codex proposals, preserves all customer requirements and constitution constraints, and addresses the findings in the ChatGPT review package.

The intended direction is to use the **Codex proposal as the more detailed starting baseline**, while retaining useful architecture framing, explanations, and diagrams from Spec Kit. This is a recommendation, not permission to copy either proposal without review.

The result must be a coherent proposal, not a third unrelated design and not a summary of the two existing proposals.

## 2. Required source documents

Read these documents before making changes:

### Authoritative requirements and constraints
- `UserRequirement.md` — original customer request. Treat as the authoritative source for customer scope and behavior.
- `.specify/memory/constitution.md` — mandatory engineering principles and governance.
- `specs/001-splitsmart-expense-sharing/spec.md` — clarified specification. It is currently **Draft**; do not describe it as approved.
- `specs/001-splitsmart-expense-sharing/checklists/requirements.md` — specification quality checklist. Completed checklist items do not mean open product decisions are approved.
- `specs/001-splitsmart-expense-sharing/plan.md` — implementation planning context; note and do not silently erase unresolved or template/action-required content.
- `specs/001-splitsmart-expense-sharing/data-model.md` — current data model.

### Existing architecture proposals
- `specs/001-splitsmart-expense-sharing/architecture/speckit/architecture.md`
- `specs/001-splitsmart-expense-sharing/architecture/speckit/design.md`
- `specs/001-splitsmart-expense-sharing/architecture/speckit/decision-log.md`
- `specs/001-splitsmart-expense-sharing/architecture/speckit/traceability-matrix.md`
- `specs/001-splitsmart-expense-sharing/architecture/speckit/open-questions-and-risks.md`
- All relevant diagrams under `specs/001-splitsmart-expense-sharing/architecture/speckit/diagrams/`
- `architecture-review/codex/architecture.md`
- `architecture-review/codex/design.md`
- `architecture-review/codex/decision-log.md`
- `architecture-review/codex/traceability-matrix.md`
- `architecture-review/codex/open-questions-and-risks.md`
- All relevant diagrams under `architecture-review/codex/diagrams/`

### Independent review package
- `architecture-review/chatgpt/architecture-cross-check-report.md`
- `architecture-review/chatgpt/requirement-coverage-matrix.md`
- `architecture-review/chatgpt/decision-and-conflict-register.md`

The ChatGPT reports are review guidance, not customer requirements. If a review recommendation conflicts with an authoritative customer requirement or constitution principle, follow the authoritative source and document the conflict.

## 3. Output location and preservation rules

Generate your consolidated proposal in a separate output directory so the original proposals and review evidence remain available for comparison.

- Spec Kit output: `architecture-review/consolidated/speckit/`
- Codex output: `architecture-review/consolidated/codex/`

Run Spec Kit and Codex independently against the same inputs and these same instructions. Do not have one agent read the other's newly generated consolidated output as its starting point. This preserves an independent comparison.

Each output directory must contain:
1. `architecture.md`
2. `design.md`
3. `decision-log.md`
4. `traceability-matrix.md`
5. `open-questions-and-risks.md`
6. `diagrams/` with editable PlantUML source files and a diagram index or README.

Do not overwrite or delete:
- `UserRequirement.md`
- the constitution
- the Draft specification or checklist
- the existing plan or data model
- either original Spec Kit or Codex architecture proposal
- the ChatGPT review package

Do not write implementation code, change application behavior, or run implementation/task-generation workflows as part of this task. Do not commit to `main`. Keep all generated work on `draft/architecture-design-prompts`.

## 4. Non-negotiable requirements

### 4.1 Preserve customer scope

The architecture must account for:
- Individual accounts and authentication.
- Group creation and membership management.
- Expenses with amount, date, description, payer, participants, and optional receipt image.
- Equal and percentage-based allocation.
- Notifications when group members create expenses.
- Configurable expense review/approval before balances are affected.
- Pairwise balances.
- Payment tracking.
- Expense, balance, and payment reports, including date ranges.
- The primary web-based product and Android and iOS applications.

Do not silently narrow the customer product to a desktop Swing application.

### 4.2 Preserve constitution constraints

The proposal must explicitly preserve:
- Java 21 and Maven for the constitutionally required MVP/prototype context.
- Java Swing for the MVP/prototype presentation layer.
- Object-oriented design.
- Strict separation between Swing presentation and business logic.
- `BigDecimal` for all monetary values and calculations; never use `float` or `double` for money.
- Explicit currency scale, precision, and rounding policy boundaries.
- JUnit 5 for automated tests.
- PlantUML source for architecture and sequence diagrams.
- Requirement traceability.
- Human review of AI-generated output.

Make clear that the Swing MVP/prototype is a limited deliverable and does **not** by itself satisfy the full web/Android/iOS product scope or FR-014/NFR-006.

### 4.3 Distinguish requirements from decisions

Label information clearly as one of:
- Customer requirement (from `UserRequirement.md`).
- Clarified specification behavior (from the Draft specification).
- Constitution constraint.
- Proposed architecture/implementation decision.
- Open product decision.
- Open engineering decision.

Never present an architecture preference, framework, database, vendor, deployment platform, or assumption as a customer requirement unless an authoritative source says so.

## 5. Required design work

Address each item below explicitly. If a decision cannot be made from authoritative inputs, keep it open, explain its impact, and identify the human decision owner.

### 5.1 Accounting and balances — critical

Define a precise, auditable accounting model for:
- The payer being a participant.
- The payer not being a participant.
- The payer's own share and elimination of self-debt.
- Equal allocation and remainder-minor-unit distribution in recorded participant order.
- Percentage allocation and the unresolved residual-minor-unit policy.
- Pairwise balances and consolidation/netting.
- Approved expense effects and payments as sources of balance changes.

Include worked examples with amounts and participant sets. At minimum include:
- A pays $90 for A, B, and C, equally: A has no debt to self; B and C each owe A $30.
- A pays $90 for B and C only, equally: B and C each owe A $45.

Define ledger invariants, including conservation/reconciliation of allocated amounts, no self-obligation, no balance effect before approval, and exactly-once application of an approved expense. Do not leave the balance calculation as a vague statement such as “calculate pairwise balances.”

### 5.2 Currency and allocation policy — critical

Identify the unresolved decisions:
- Supported currencies.
- Currency minor-unit scale.
- Accepted input scale and validation.
- Rounding mode and where rounding occurs.
- Percentage precision.
- Deterministic residual-minor-unit allocation for percentage splits.

Do not invent product approval. You may propose a deterministic policy, but label it as a proposal and record the human decision required.

### 5.3 Product service/API and channel boundaries — critical

Show the logical relationship between:
- Web client.
- Android client.
- iOS client.
- Java Swing MVP/prototype.
- Application/service boundary.
- Domain/business rules.
- Persistence.
- Identity/authentication.
- Receipt storage/retrieval.
- Notification delivery.
- Reporting.

Define which rules must be enforced server-side and shared by all clients. Describe API responsibilities and trust boundaries at a logical level without prematurely choosing a vendor or framework. Explain how the Swing MVP demonstrates the core use cases without being mistaken for the full customer product.

### 5.4 Approval workflow

Specify the proposed state model and behavior for pending, approved, and rejected expenses. Address:
- Who may review.
- Whether the submitter may approve their own expense.
- Single reviewer versus multiple reviewers.
- Quorum or multi-level approval.
- Concurrent and duplicate decisions.
- Rejection and resubmission behavior.
- Audit history.
- When and how balance effects are created.

Keep unresolved policy choices open rather than guessing.

### 5.5 Transactions, idempotency, and concurrency

Show explicit transaction boundaries for:
- Recording an approval decision and creating its financial effect(s).
- Validating an outstanding obligation and recording a payment.
- Preventing duplicate effects on retries.
- Concurrent approvals and concurrent payments.
- Rollback and recovery behavior.

Diagrams must show transaction/commit boundaries, uniqueness/idempotency controls, and the source of truth. If balance projections are used, explain how they are reconciled or rebuilt from authoritative records.

### 5.6 Historical records and corrections

Separate record retention from access authorization. Define open decisions for former-member access to historical expenses, receipts, balances, and payments.

Describe an auditable proposed approach to:
- Payment correction and refund.
- Expense amendment after submission or approval.
- Reversal of an incorrect approval/effect.

Prefer append-only or compensating financial entries over destructive changes to financial history, but label this as an architecture recommendation pending approval.

### 5.7 Security and privacy

Document the logical trust boundary and service-side enforcement responsibilities. Address:
- Authentication and password recovery.
- Session/token lifecycle.
- Authorization on every relevant read and write.
- Group membership and former-member access.
- Receipt access.
- Rate limiting and abuse protection.
- Sensitive data handling and audit events.

Do not claim security controls are implemented or verified.

### 5.8 Notifications, receipts, and reports

Record the unresolved product decisions for:
- Notification channels, preferences, retry/deduplication, and delivery expectations.
- Receipt formats, file-size limits, content validation, retention/deletion, and authorization.
- Report date/time boundaries, timezone, currency aggregation, snapshot consistency, and export formats.

Do not couple notification delivery failure to the financial approval outcome unless the authoritative specification explicitly requires it.

## 6. Traceability requirements

Create a traceability matrix with **one row per requirement**:
- FR-001 through FR-015.
- NFR-001 through NFR-006.
- Relevant constitution principles.

For each row include:
- Exact requirement ID and concise requirement summary.
- Source document and section/anchor where available.
- Architecture component(s) and design section(s).
- Relevant diagram(s).
- Proposed verification/test case identifier(s).
- Status: covered, partially covered, open decision, or not covered.
- Notes explaining gaps or assumptions.

Do not group several FRs into a single row. Do not mark a requirement “implemented” or “verified” merely because it appears in an architecture document.

## 7. Diagram requirements

Provide editable PlantUML source for diagrams that cover, at minimum:
- System context and all client channels.
- Logical components/layers and dependency direction.
- Domain model, including payer, ordered participants, allocations, approvals, financial effects, balances, and payments.
- Expense submission and approval sequence.
- Payment sequence.
- Relevant security/trust boundaries or deployment/runtime topology.

Diagrams must agree with the prose and data model. In particular:
- Application/domain layers define inward-facing ports; infrastructure adapters implement those ports.
- Do not draw dependencies from the domain/application core to concrete infrastructure adapters.
- Show ordered participant semantics.
- Show atomic transaction boundaries and idempotency/uniqueness for approval and payment.
- Clearly distinguish the Swing prototype from the complete multi-channel product.

## 8. Required decision and risk register

Maintain a decision log that includes at least the open topics from the ChatGPT register:
- Payer/participant accounting.
- Currency and rounding.
- Percentage residual allocation.
- Service/API and channel architecture.
- Approval roles and policy.
- Approval transaction and idempotency.
- Payment concurrency and idempotency.
- Payment correction/refund.
- Historical access after membership removal.
- Identity and recovery security.
- Receipt lifecycle.
- Notification delivery.
- Reporting semantics.
- Persistence choice.
- Balance source of truth/projection.
- MVP versus full-product release boundary.

For each decision include:
- ID.
- Description and why it matters.
- Options considered.
- Recommendation, if appropriate, clearly marked as unapproved.
- Consequences/trade-offs.
- Decision owner.
- Status (open/proposed/approved/deferred).
- Evidence or source requirement.

Record risks with likelihood/impact where reasonably assessable, mitigation, and owner. Do not mark decisions approved without explicit human approval.

## 9. Quality checks before presenting output

Before declaring the proposal complete:
1. Check every FR/NFR and constitution principle against the architecture and traceability matrix.
2. Check every customer requirement is preserved without scope reduction.
3. Check all critical and high findings in the ChatGPT cross-check report are addressed or explicitly retained as open with rationale and owner.
4. Check that no open product decision has been silently converted into a requirement or approved decision.
5. Check that architecture prose, design, diagrams, data model, decision log, and traceability matrix do not contradict one another.
6. Check all PlantUML files are syntactically valid if the available environment supports validation; otherwise state that validation was not run.
7. Check that the plan/specification remain Draft unless a human has explicitly approved them.
8. State what was not validated (for example, code, builds, tests, deployment, or security implementation).

## 10. Required completion summary

When finished, report:
- Which output files were created and their paths.
- Which original files were read.
- Which critical/high findings were resolved in the proposal.
- Which decisions remain open and who should decide them.
- Any requirement that remains partially covered or uncovered.
- Any diagram validation performed and its result.
- Explicit confirmation that source requirements, constitution, specification, original proposals, and ChatGPT review reports were not modified.
- Explicit confirmation that no implementation code was generated and no merge to `main` was performed.

## 11. Approval boundary

The generated documents are proposals only. Do not:
- Approve the architecture on behalf of the user or customer.
- Mark the Draft specification approved.
- Implement features or generate implementation tasks.
- Modify original customer requirements to fit an architecture.
- Merge or push changes to `main`.

Stop after presenting the consolidated proposal for human review. Wait for explicit approval before proceeding to task generation or implementation.
