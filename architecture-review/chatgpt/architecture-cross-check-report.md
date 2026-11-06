# SplitSmart Architecture Cross-Check Report

**Repository:** `berlina-m/lab7`  
**Reviewed branch:** `draft/architecture-design-prompts`  
**Review date:** 2026-11-05  
**Review status:** Draft review for human approval; not an approval or implementation authorization.

## 1. Executive assessment

Both the Spec Kit and Codex architecture proposals are directionally sound and broadly consistent with the customer request, clarified Draft specification, and constitution. Both preserve the distinction between the customer’s complete product (primary web plus Android and iOS) and the constitutionally required Java 21/Maven/Swing MVP or prototype. Both identify `BigDecimal`, GUI/business-logic separation, JUnit 5, PlantUML, traceability, and human review.

**Recommendation:** Use the Codex proposal as the more detailed baseline, selectively retain Spec Kit's concise architecture framing and workflow diagrams, then create one consolidated proposal after resolving the critical product and financial questions in the decision register. Do not approve either proposal unchanged.

This review is based on the files available on the reviewed branch. It does not validate implementation code, execute tests, render/compile PlantUML, or establish that any proposed capability has been implemented.

## 2. Source baseline and authority

The review used:
- `UserRequirement.md` — authoritative original customer request.
- `.specify/memory/constitution.md` — engineering principles and governance.
- `specs/001-splitsmart-expense-sharing/spec.md` — clarified feature specification, explicitly **Draft**.
- `specs/001-splitsmart-expense-sharing/checklists/requirements.md` — specification quality checklist.
- `specs/001-splitsmart-expense-sharing/plan.md` and `data-model.md`.
- `specs/001-splitsmart-expense-sharing/architecture/speckit/` — Spec Kit proposal and diagrams.
- `architecture-review/codex/` — independent Codex proposal and diagrams.

Authority order for this review:
1. Customer behavior and delivery scope: `UserRequirement.md`.
2. Engineering constraints and governance: constitution.
3. Testable interpretation and clarifications: Draft `spec.md`, subject to customer/product approval.
4. Architecture proposals: non-authoritative recommendations only.

The requirements checklist marks specification-quality items complete, but that does not mean unresolved product decisions are approved or the architecture is implementation-ready. The plan also retains template/action-required wording and lists several technical details as needing clarification.

## 3. Overall comparison

| Area | Spec Kit proposal | Codex proposal | Review conclusion |
|---|---|---|---|
| Architecture style | Dependency-inverted application/domain core with ports and adapters | Ports-and-adapters, modular monolith/service boundary | Compatible; Codex explains trade-offs more fully |
| Swing/full-product distinction | Explicit | Explicit | Both correct |
| Components and responsibilities | Concise | More granular | Codex is stronger baseline |
| Domain model | Core entities named | Entities and key rules described in more detail | Codex stronger, but both need more explicit accounting semantics |
| Monetary design | BigDecimal and open currency policy | BigDecimal, currency policy, percentage reconciliation | Both need approved currency and residual policy |
| Approval and balance effects | Atomic approval and idempotent effect described | Transactions, versions, unique effects described | Codex stronger; diagrams in both need transaction clarity |
| Payments | Outstanding-balance validation | Validation, transaction and idempotency detail | Codex stronger; both need correction/refund policy |
| Security | Authorization boundary and audit | More explicit read authorization and least privilege | Codex stronger |
| Testing | JUnit 5 test categories | Broader unit/application/adapter/cross-channel test plan | Codex stronger |
| Traceability | FR/NFR grouped in rows | Individual FR-001–FR-015 and NFR-001–NFR-006 rows | Codex format preferred |
| Decision register | 6 entries | 10 entries | Codex more actionable |
| Open questions | Identified | Categorized by product/engineering decisions and risks | Codex stronger |

## 4. Findings by severity

### Critical

**C-01 — Balance accounting semantics are not fully specified.**  
Both proposals state that the payer is separate from ordered participants and that balances derive from approved expenses and payments. However, neither provides a complete normative accounting formula, including the case where the payer is also a participant, payer is not a participant, self-obligations are netted, and multiple pairwise obligations are consolidated.

Example: A pays $90 for A, B, and C with equal shares of $30. A's own $30 share must not become a self-debt. B and C each owe A $30. If A is not a participant, the participants' shares must still credit A as payer.  
**Required action:** define a ledger/effect model and worked examples for both payer cases, then test reconciliation and invariants.

**C-02 — Full product service/API architecture remains too abstract.**  
Both proposals correctly say Swing alone cannot satisfy FR-014/NFR-006. Both describe shared business contracts and web/Android/iOS adapters, but neither fully specifies the proposed trusted service boundary, API responsibilities, authentication/session boundary, data ownership, or how the Java Swing prototype relates to that boundary.  
**Required action:** add a logical deployment/runtime view and client-to-service contract. Keep specific frameworks, hosting, and vendors open unless approved.

**C-03 — Currency and percentage residual policy are unresolved.**  
Both use `BigDecimal` and preserve the clarified equal-split rule (remainder minor units distributed in recorded participant order). The specification leaves supported currencies, currency scale, input precision, rounding mode, percentage precision, and percentage rounding residual allocation open. Codex explicitly proposes deterministic reconciliation but does not define an approved policy.  
**Required action:** product owner approves a currency policy and residual allocation rule before money implementation.

### High

**H-01 — Approval policy is not finalized.**  
Both proposals model pending → approved/rejected and one-time balance effects. The specification allows single or multiple review processes. Reviewer roles, self-approval, quorum, multi-level approval, and concurrent decision behavior require approval.  
**Required action:** define an approval policy/state machine, authorization matrix, and concurrency outcomes.

**H-02 — Transaction boundaries are not consistently explicit in diagrams.**  
Spec Kit's approval sequence shows decision recording and balance effect as sequential calls; its payment sequence shows a payment save followed by balance projection. The prose describes atomicity, but the diagrams should show a single transaction/commit boundary and failure/rollback behavior. Codex's sequence is stronger but still should show the persistence transaction explicitly around decision + effect and payment validation + record.  
**Required action:** revise diagrams to show transaction scope, uniqueness/idempotency constraints, retries, and projection failure/rebuild behavior.

**H-03 — Historical access after membership removal is unresolved.**  
Both preserve historical transaction records, but the user-facing access policy for former members is open. Preservation does not itself decide who may view old expenses, receipts, balances, and payment history.  
**Required action:** define and test historical access policy separately from record retention.

**H-04 — Payment corrections, refunds, and expense edits are not designed.**  
The spec identifies these edge cases but leaves policy open. Codex says corrections are open; Spec Kit likewise lists them.  
**Required action:** define whether corrections are compensating transactions, reversal entries, or another audited workflow. Avoid destructive mutation of financial history.

**H-05 — Security design needs a concrete trust boundary.**  
Codex is more detailed about authorization on reads, least privilege, and credential handling. Neither proposal fully defines service-side enforcement, token/session lifecycle, recovery verification, rate limiting, or sensitive receipt access.  
**Required action:** document the trusted service boundary and security controls before full-product implementation.

### Medium

**M-01 — Report semantics remain underspecified.**  
Date boundary inclusivity, timezone, currency aggregation, snapshot consistency, and export formats remain open. Codex recommends inclusive start/exclusive end, but this is only a proposal.  
**Required action:** product owner approves report parameter and date-boundary semantics.

**M-02 — Receipt lifecycle is only an adapter concept.**  
Both use a receipt port, but accepted formats, size limits, retention/deletion, malware/content validation, and former-member access are undecided.  
**Required action:** approve receipt policy and include authorized retrieval tests.

**M-03 — Notification delivery behavior needs product decisions.**  
Both separate notification delivery from financial status and support in-app MVP notifications. Channels beyond in-app, preferences, retries, deduplication, and delivery status expectations remain open.  
**Required action:** specify MVP delivery and future channel requirements.

**M-04 — Spec Kit's traceability matrix is too aggregated for audit.**  
It groups FRs (e.g. FR-003–FR-006) and several NFRs. Codex provides one row per requirement.  
**Required action:** use one row per FR/NFR, with design element, verification case, and status.

**M-05 — Diagram and text alignment needs tightening.**  
Codex component diagram has application services pointing toward named concrete adapters, while the prose says adapters implement inward-facing ports. The dependency direction should be corrected to show application/domain defining ports and infrastructure implementing them. Codex's domain diagram should make ordered participant identity/order explicit. Spec Kit's diagrams are simpler but omit some detailed transaction and port contracts.  
**Required action:** revise diagrams to match the written dependency rule and accounting design.

**M-06 — Plan document is not fully execution-ready.**  
`plan.md` retains template comments/action-required notes and leaves storage, testing beyond JUnit, performance, scale, and client technology as needs clarification. This is acceptable as a planning draft but not a completed implementation plan.  
**Required action:** remove template scaffolding and complete approved technical context before task breakdown.

## 5. Constitution compliance review

| Principle | Spec Kit | Codex | Assessment |
|---|---|---|---|
| I. Customer requirements and traceability | Addresses FR/NFR coverage, but grouped | Individual FR/NFR rows | Both aligned; Codex more auditable |
| II. Java 21 and Maven | States Java 21/Maven MVP context | States Java 21/Maven and package/module proposal | Both aligned |
| III. OOP and Swing separation | Explicit presentation/application/domain/infrastructure | Explicit client/application/domain/adapter boundaries | Both aligned |
| IV. Exact monetary arithmetic | BigDecimal and explicit boundaries | BigDecimal and explicit boundaries | Both aligned; policy remains open |
| V. JUnit 5, PlantUML, human review | Explicit | Explicit | Both aligned |

No proposal-level constitution violation was identified. This is not proof of implementation compliance. The architecture and implementation must still preserve Swing-only presentation, Java 21/Maven for the mandated MVP, JUnit 5 tests, editable PlantUML, BigDecimal-only money, traceability, and human approval.

## 6. Functional and non-functional requirement cross-check

Status key:
- **Covered** — architecture/design element identified.
- **Covered; decision open** — element identified, but an important policy remains unresolved.
- **Covered; verification needed** — design intent present, acceptance evidence remains to be produced.

| Requirement | Spec Kit | Codex | Review status / remaining concern |
|---|---|---|---|
| FR-001 Accounts, email/password, recovery | Identity/account boundary | Identity adapter and account use cases | Covered; recovery verification/session policy open |
| FR-002 Groups and membership | Group/Membership | Group aggregate and membership history | Covered; roles and former-member access open |
| FR-003 Expense, payer, participants | Expense/Allocation | Expense aggregate and active-member validator | Covered; payer accounting examples needed |
| FR-004 Optional receipt image | Receipt port | Receipt port and authorized retrieval | Covered; format/size/retention/access open |
| FR-005 Equal split | Ordered remainder allocation | Minor-unit allocation in participant order | Covered; currency policy open |
| FR-006 Percentage split | Validate 100%, reconcile after rounding | Validate percentages and deterministic reconciliation proposal | Covered; precision/residual policy open |
| FR-007 Configured approval | Approval policy/state transition | Review policy/state machine | Covered; reviewer/self-approval/quorum open |
| FR-008 Review status/history | Decision history | Review Decision history/version | Covered |
| FR-009 New expense notifications | Notification port | Domain event/notification adapter | Covered; channels/retries open |
| FR-010 Pairwise balances | Balance service/projection | Immutable effects and balance projection | Covered; accounting formula and self-obligation rule need definition |
| FR-011 Payment tracking/over-balance rejection | Payment use case and outstanding check | Payment use case, transaction, idempotency | Covered; correction/refund policy open |
| FR-012 Summaries | Reporting use case | Report/query service | Covered; currency aggregation/snapshot semantics open |
| FR-013 Date-range reports | Report parameters | Report query and proposed boundary semantics | Covered; final date/time/export policy open |
| FR-014 Web, Android, iOS | Three client adapters identified | Three client adapters identified | Covered conceptually; service/API and delivery architecture incomplete |
| FR-015 Preserve historical records | Historical references retained | Immutable transaction records | Covered; access policy open |
| NFR-001 Authorization/privacy | Authorization policy | Application-level authorization on reads | Covered; trust boundary/security detail open |
| NFR-002 Monetary accuracy | BigDecimal and currency policy | BigDecimal and reconciliation tests | Covered; currency policy open |
| NFR-003 Understandable status/effects | Status/effect presentation | Explicit outcome/result contracts | Covered; usability verification required |
| NFR-004 Consistency across views | Versioned transactions/projections | Versioned transactions and consistent snapshots | Covered; consistency model needs specification |
| NFR-005 Explicit success/error | Use-case result/error intent | Typed result/actionable error intent | Covered; verify each action |
| NFR-006 Capability parity | Shared use cases across clients | Shared application contracts across clients | Covered conceptually; cross-channel acceptance suite required |

## 7. Architecture recommendation

Use the following as the consolidated target direction, subject to human approval:

1. **Logical style:** modular monolith / ports-and-adapters as the initial logical architecture; do not introduce microservices without a demonstrated requirement.
2. **Domain core:** framework-independent account, group, expense, allocation, approval, balance/effect, payment, notification, and reporting model.
3. **Application layer:** explicit commands/queries, authorization, transaction orchestration, idempotency, and result/error contracts.
4. **Adapters:** Swing presentation for the mandated prototype; future web, Android, and iOS clients; persistence, identity, receipt, notification, and reporting adapters.
5. **Financial model:** currency-aware BigDecimal value objects; explicit approved scale/rounding; immutable approved-expense effect ledger; payments as auditable entries; projections rebuildable from authoritative effects.
6. **Consistency:** approval decision + unique financial effects in one transaction; payment obligation check + payment record in one transaction; version/constraint protection for concurrent requests.
7. **Testing:** JUnit 5 domain/application/adapter tests plus requirement-tagged cross-channel acceptance tests.
8. **Traceability:** one row per FR/NFR, each linked to design elements, acceptance tests, and review status.
9. **Scope:** Swing MVP is a clearly labelled subset and cannot be represented as complete customer delivery.
10. **Governance:** all proposals remain unapproved until reviewed by humans and the Draft specification/product decisions are ratified.

## 8. Approval gates before task generation

- [ ] Confirm the clarified specification and customer scope are approved (spec currently Draft).
- [ ] Approve payer/participant accounting and worked balance examples.
- [ ] Approve supported currencies, precision, rounding, and percentage residual allocation.
- [ ] Approve reviewer roles, self-approval, multi-level/quorum rules, and concurrency behavior.
- [ ] Approve historical access after membership removal.
- [ ] Approve payment correction/refund and post-approval expense change policy.
- [ ] Approve password recovery and service-side security requirements.
- [ ] Approve receipt policy and notification channels for MVP.
- [ ] Approve report date/time, currency aggregation, and export semantics.
- [ ] Approve logical full-product service/API architecture and channel parity strategy.
- [ ] Consolidate and correct PlantUML diagrams.
- [ ] Complete the implementation plan and one-row-per-requirement traceability matrix.
- [ ] Human reviewers approve the consolidated architecture and decision register.

## 9. Final disposition

**Disposition: Revise and consolidate; do not approve unchanged.**

Codex is the stronger baseline because it provides more explicit component responsibilities, domain detail, test strategy, traceability, and decision/risk records. Retain Spec Kit's concise dependency-inversion framing and workflow diagrams where useful. The consolidated result must resolve the critical accounting, channel/service, and currency issues and correct diagram/text inconsistencies.

No source requirement, constitution, specification, Spec Kit proposal, or Codex proposal should be silently overwritten as part of this review.
