# SplitSmart — Independent Architecture and Design Prompts

**Purpose:** Generate two independent architecture/design proposals (Spec Kit and Codex), then have ChatGPT perform an evidence-based comparison and cross-check.

**Repository:** `berlina-m/lab7`  
**Feature:** `001-splitsmart-expense-sharing`  
**Status:** Draft prompts for human editing. Do not treat this document as an approved architecture.

---

## 0. Shared instructions and source-of-truth rules

Use these rules in all three tools:

1. Treat `UserRequirement.md` as the authoritative original customer request. Do not edit, rename, summarize away, or weaken it.
2. Treat `.specify/memory/constitution.md` as the governing engineering constitution.
3. Treat `specs/001-splitsmart-expense-sharing/spec.md` as the clarified feature specification. It is currently marked Draft; do not silently call it approved. Flag any unresolved or inconsistent requirements.
4. Read `specs/001-splitsmart-expense-sharing/checklists/requirements.md` and other relevant existing project artifacts before making decisions.
5. Preserve the distinction between:
   - **Customer requirements**: product behavior and delivery scope from `UserRequirement.md`.
   - **Clarifications**: decisions recorded in the specification's Clarifications section.
   - **Constitutional engineering constraints**: Java 21, Maven, Java Swing for the specified MVP, JUnit 5, PlantUML, object-oriented design, separation of GUI and business logic, `BigDecimal` for all money, traceability, and human review.
   - **Implementation/design decisions**: choices proposed by the architecture author that the customer and constitution do not already mandate.
   - **Assumptions/open questions**: unresolved matters that must not be presented as facts or customer requirements.
6. The full customer product must remain visible in the architecture: primary web delivery plus Android and iOS applications. A Java Swing MVP/prototype is only a separately labelled subset and does not satisfy those full-channel requirements.
7. Do not invent customer requirements, acceptance criteria, integrations, infrastructure, databases, cloud providers, APIs, security certifications, deployment targets, or performance targets. Where a technical decision is needed but not specified, label it as a proposal, give rationale and alternatives, and state what must be confirmed.
8. Do not generate application source code or begin implementation. This task is architecture and design documentation only.
9. Keep all monetary values and calculations based on `BigDecimal`; define explicit currency precision, scale, rounding, and allocation boundaries as design matters, while flagging the supported-currency policy as unresolved if not specified.
10. Every important component and design decision must trace to one or more requirement IDs (FR/NFR), constitution principles, or be explicitly labelled as a proposed implementation decision with no direct customer requirement.
11. Do not alter `UserRequirement.md`, `constitution.md`, or the clarified `spec.md`. If a conflict is found, report it; do not silently resolve it by changing source documents.
12. Prefer a simple, cohesive, testable design. Explain trade-offs rather than claiming one design is the only possible solution.

### Known project facts that must be respected

- Product name: SplitSmart.
- Original customer features: individual accounts; groups and membership; expenses with amount, date, description, equal or percentage split and optional receipt image; involved users; notifications; expense approval; pairwise balances; payment tracking; reports and date-range parameters; primary web product plus Android and iOS apps.
- Clarifications already recorded in the specification:
  - Record one payer separately from participants; payer may or may not be a participant.
  - Only active group members may be expense participants at submission.
  - Equal-split leftover cents are distributed one at a time in the expense's recorded participant order.
  - Email/password authentication and password recovery.
  - Reject a payment greater than the payer's current outstanding balance to the recipient.
- Engineering constraints in the constitution: Java 21, Maven, Java Swing for the Swing MVP, JUnit 5, PlantUML, object-oriented design, GUI/business-logic separation, `BigDecimal` for money, requirement traceability, and mandatory human review of AI output.
- The spec explicitly says a Swing-only application is not a substitute for web, Android, and iOS delivery.

---

# 1. Prompt for Spec Kit — first independent architecture/design proposal

Run this in the Spec Kit-enabled agent from the repository root. Use the installed workflow/command syntax supported by this repository. The intended planning stage is `/speckit.plan`; do not invent a different slash command if it is not available.

```text
/speckit.plan

Create the first independent architecture and technical design proposal for SplitSmart using the repository's current source-of-truth documents.

Before planning, read:
- UserRequirement.md
- .specify/memory/constitution.md
- specs/001-splitsmart-expense-sharing/spec.md
- specs/001-splitsmart-expense-sharing/checklists/requirements.md
- relevant Spec Kit templates and project configuration

The clarified specification is currently Draft. Preserve that status and identify any remaining blockers or contradictions.

OBJECTIVE
Produce an architecture and design blueprint, not application code. Cover both:
(A) the explicitly labelled Java 21 / Maven / Java Swing MVP or prototype, and
(B) the complete customer product target, which must include a primary web experience and Android and iOS applications.

Do not imply that the Swing MVP fulfils the complete web/mobile customer requirement. Explain which capabilities and architectural boundaries are shared, which are MVP-specific, and what additional clients/services/adapters would be required for the full product. If a coherent cross-platform target architecture cannot be determined from the supplied requirements, present options and mark the decision open rather than guessing.

REQUIRED ARCHITECTURE CONTENT
1. Architecture overview and goals.
2. Scope boundaries: full customer product versus Swing MVP/prototype.
3. Architecture style and rationale; compare credible alternatives where the requirements do not determine one.
4. System context and external actors/systems. Do not invent external integrations.
5. Component/container view: responsibilities, dependencies, interfaces, and allowed dependency direction.
6. Layer/package/module structure, including clear separation of Swing presentation, application/use-case coordination, domain/business rules, and infrastructure/persistence boundaries.
7. Domain model and key relationships for Account, Group, Membership, Expense, Expense Share, Review Decision, Notification, Balance, Payment, and Report.
8. Critical workflows and sequence descriptions: account access, group membership, expense submission, approval/rejection, equal split, percentage split, notification, balance calculation, payment settlement, and report generation.
9. Data ownership and persistence requirements. Persistence technology is not specified: present options and a recommendation only if justified; mark it as a proposed decision.
10. Security and privacy design: authentication, authorization, group isolation, password recovery, receipt access, and audit/history. Do not claim compliance with an unspecified standard.
11. Error handling, validation, transaction boundaries, concurrency/idempotency, and notification failure behavior.
12. Monetary design: BigDecimal, currency precision, rounding, equal-split remainder allocation in participant order, percentage reconciliation, and prevention of duplicate balance effects.
13. Test architecture using JUnit 5, including unit, integration, component, and acceptance-level coverage where appropriate.
14. PlantUML diagrams: context, component/container, domain/class, and key sequence diagrams. Ensure diagram source is included as editable .puml text.
15. Deployment/runtime view for the Swing MVP and the full web/Android/iOS target. Clearly label unselected technology choices.
16. Risks, trade-offs, assumptions, open questions, and decisions requiring human/customer approval.
17. Requirement traceability matrix mapping every FR-001 through FR-015 and NFR-001 through NFR-006 to components, workflows, and planned verification. Include constitution principles where relevant.
18. A decision log using stable IDs (for example ADR-001), status (proposed/accepted/open/rejected), context, options, decision, rationale, consequences, and approver required. Do not mark a decision accepted without human approval.

QUALITY AND CONSISTENCY CHECKS
- Do not put business rules in Swing event handlers or make domain/business components depend on Swing.
- Do not use float or double for money.
- Ensure payer is modelled separately from participants.
- Ensure only active group members can be participants at submission.
- Ensure pending/rejected expenses do not affect balances and an approved expense affects balances exactly once.
- Preserve the clarified equal-split remainder order and payment-over-balance rejection.
- Preserve historical expense/payment records when membership changes; identify the access-policy question.
- Distinguish in-application notification as an MVP proposal from any unconfirmed production delivery channel.
- Do not decide unresolved currencies, receipt constraints, report exports, reviewer self-approval, persistence, or password-recovery verification without labelling the choice as proposed/open.
- Identify any requirement that cannot be supported by the proposed MVP architecture.

OUTPUT AND FILE SAFETY
Use the normal Spec Kit planning artifact(s) for this feature, but keep the architecture proposal clearly identifiable as the Spec Kit output. Create or update only the intended planning/design artifacts for feature 001. Do not overwrite the source requirement, constitution, or clarified specification. If the normal plan file already exists, inspect it and preserve useful content; do not overwrite unrelated work.

At minimum, produce:
- the normal Spec Kit plan artifact (for example plan.md, according to this repository's workflow),
- an architecture document,
- a design document,
- editable PlantUML source files,
- a requirement-to-architecture traceability matrix,
- an ADR/decision log,
- a list of unresolved questions and risks.

Use a dedicated subfolder such as specs/001-splitsmart-expense-sharing/architecture/speckit/ for supplementary architecture/design and diagram artifacts if that fits the existing workflow. Do not create duplicate competing specifications.

At the end, report every file created/modified, summarize the main architectural choices, list unresolved decisions, identify constitution/specification conflicts, and state explicitly that no application code was generated and no source-of-truth documents were changed.
```

---

# 2. Prompt for Codex — second independent architecture/design proposal

**Important:** Run Codex independently from the Spec Kit proposal. Give Codex the source-of-truth documents listed above, but do not ask it to read or copy the Spec Kit architecture output. This avoids anchoring and makes the comparison meaningful.

```text
You are producing an independent architecture and technical design proposal for the SplitSmart project in this repository.

INDEPENDENCE RULE
Do not read, reuse, imitate, or base your proposal on any Spec Kit-generated architecture, plan, design, ADR, diagram, or other proposal. In particular, do not inspect the Spec Kit output folder. Work only from the original customer request, the constitution, the clarified feature specification, the requirements checklist, and repository-level technical constraints. The goal is an independent second opinion.

READ THESE SOURCE DOCUMENTS
- UserRequirement.md (authoritative original customer request; do not modify)
- .specify/memory/constitution.md (governing engineering principles)
- specs/001-splitsmart-expense-sharing/spec.md (clarified feature specification; currently Draft)
- specs/001-splitsmart-expense-sharing/checklists/requirements.md
- relevant build/configuration files, if present

Do not assume that a missing file or technology has already been selected.

PROJECT FACTS AND CONSTRAINTS
SplitSmart supports individual accounts, groups, shared expenses, equal and percentage splits, optional receipt images, expense notifications, approval workflows, pairwise balances, payment settlement, and reports. The customer requires a primary web product plus Android and iOS applications.

The constitution requires Java 21, Maven, Java Swing for the specified MVP, JUnit 5, PlantUML, object-oriented design, separation of GUI and business logic, BigDecimal for all monetary values/calculations, requirement traceability, and human review of AI output.

The specification clarifies:
- payer is separate from participants and may or may not be a participant;
- only active group members may be selected as participants at expense submission;
- equal-split leftover cents are distributed one at a time in recorded participant order;
- authentication uses email/password with password recovery;
- payments above the payer's current outstanding balance to the recipient are rejected.

A Swing-only MVP/prototype does not satisfy the customer's complete web, Android, and iOS delivery requirement. You must explicitly design and discuss this scope distinction.

TASK
Create your own architecture and design from first principles using the source documents. Do not generate application source code. Do not modify UserRequirement.md, constitution.md, spec.md, or the requirements checklist.

COVER THE FOLLOWING
1. Architecture goals, scope, assumptions, and constraints.
2. Full-product target architecture versus the Java Swing MVP/prototype.
3. System context, actors, major components, responsibilities, interfaces, and dependency direction.
4. Object-oriented domain model and relationships for all key business entities.
5. Package/module structure and boundaries, keeping Swing presentation independent from business logic.
6. Use cases and end-to-end flows for accounts, groups, expense creation, approval/rejection, split calculations, notifications, balances, payments, and reports.
7. Data and persistence boundaries; compare options because persistence is not specified.
8. Authentication, authorization, group data isolation, receipt access, password recovery, and audit/history considerations.
9. Money model using BigDecimal, supported-currency precision questions, rounding, equal-split remainder allocation, percentage splits, and balance reconciliation.
10. Transaction consistency, concurrency, idempotency, duplicate submission/review/payment prevention, and failure handling.
11. Notification behavior and separation between MVP in-app notification and unconfirmed full-product delivery channels.
12. Test strategy using JUnit 5, including domain tests, use-case tests, adapter/integration tests, and requirement acceptance tests.
13. PlantUML diagrams with editable .puml source: system context, component architecture, domain/class model, and important sequence flows.
14. Deployment/runtime options for Swing MVP and web/Android/iOS product; label all unselected technologies as proposals.
15. Risks, trade-offs, unresolved questions, and decisions requiring approval.
16. Complete traceability matrix for FR-001–FR-015 and NFR-001–NFR-006.
17. Architecture decision records with stable IDs, options, rationale, consequences, and status. Mark decisions proposed/open until a human approves them.

NON-NEGOTIABLE REVIEW POINTS
- Keep customer requirements separate from implementation decisions.
- Do not claim Swing satisfies web/Android/iOS.
- Do not invent databases, frameworks, cloud vendors, APIs, integrations, currency support, receipt limits, export formats, or security compliance.
- Do not use float/double for money.
- Do not put business rules in GUI event handlers.
- Preserve payer/participant distinction and all five recorded clarifications.
- Pending/rejected expenses must not change balances; approved expense effects must be applied once.
- Payment greater than current outstanding amount must be rejected.
- Preserve historical records on membership change and identify access-policy implications.
- Every proposed design element must be traceable to requirements or clearly marked as an implementation proposal.
- Identify missing information instead of hiding it behind assumptions.

OUTPUT LOCATION
Write only your independent Codex proposal under:
architecture-review/codex/

Create:
- architecture-review/codex/architecture.md
- architecture-review/codex/design.md
- architecture-review/codex/traceability-matrix.md
- architecture-review/codex/decision-log.md
- architecture-review/codex/open-questions-and-risks.md
- architecture-review/codex/diagrams/*.puml

If a target file already exists, inspect it and do not overwrite user-authored work without asking. Do not modify Spec Kit artifacts or source-of-truth documents.

FINAL RESPONSE
List files created, summarize your architecture, identify key assumptions and open decisions, explain how the MVP relates to the complete product, and confirm that no application code or source-of-truth documents were changed.
```

---

# 3. Prompt for ChatGPT — independent comparison, cross-check, and report

Use this only after both proposals are complete. Provide ChatGPT with the repository access or upload the generated files. ChatGPT must inspect the actual files; it must not infer what either tool produced from the prompts alone.

```text
Act as an independent senior software architect and requirements-assurance reviewer for SplitSmart.

OBJECTIVE
Compare the architecture and design independently produced by Spec Kit and Codex. Cross-check both against the original customer request, the project constitution, and the clarified specification. Produce an evidence-based review report that identifies strengths, omissions, contradictions, unsupported assumptions, risks, and recommended resolutions.

SOURCE MATERIAL TO INSPECT
1. UserRequirement.md — authoritative original customer request.
2. .specify/memory/constitution.md — governing engineering principles.
3. specs/001-splitsmart-expense-sharing/spec.md — clarified specification (check its status; it is Draft unless formally changed).
4. specs/001-splitsmart-expense-sharing/checklists/requirements.md.
5. The actual Spec Kit architecture/design/plan/diagram/traceability/ADR files.
6. The actual Codex architecture/design/diagram/traceability/ADR files.
7. Relevant repository build/configuration files if they affect feasibility.

Do not review only summaries. Read the actual artifacts and cite exact file paths and section headings (and line numbers when available) for every significant finding. If a file is missing or inaccessible, say so and do not pretend to have reviewed it.

REVIEW PRINCIPLES
- The original customer request is the authority for customer scope.
- The constitution governs engineering constraints.
- The clarified specification supplies recorded clarifications, but its Draft status must be respected.
- Customer requirements, constitutional constraints, clarifications, assumptions, and implementation decisions are different categories. Do not conflate them.
- Neither AI proposal is authoritative merely because it is detailed or confident.
- Do not automatically prefer the more elaborate proposal.
- Do not silently merge conflicting proposals. Explain the conflict and recommend a decision with rationale and trade-offs.
- Do not invent requirements or make unapproved decisions on behalf of the customer.
- Human approval is required before accepting the final architecture.

REQUIRED CROSS-CHECKS
A. Customer-scope coverage:
Check accounts, groups, expense creation, amount/date/description, equal and percentage splits, payer and participants, optional receipt, notifications, approval workflow, balances, payment tracking, reporting/date ranges, and web/Android/iOS delivery.

B. Clarification correctness:
Check separate payer/participants; active-member-only participants at submission; equal-split remainder allocation in participant order; email/password plus password recovery; and rejection of payments above current outstanding balance.

C. Constitution compliance:
Check Java 21, Maven, Java Swing MVP, JUnit 5, PlantUML, object-oriented design, GUI/business-logic separation, BigDecimal-only monetary arithmetic, traceability, and mandatory human review.

D. Architecture quality:
Evaluate cohesion, coupling, dependency direction, separation of concerns, extensibility, testability, consistency, security/privacy boundaries, data integrity, concurrency, idempotency, failure handling, maintainability, and whether the architecture is proportionate to the project.

E. MVP versus full-product boundary:
Explicitly verify that Swing is labelled as an MVP/prototype subset and does not falsely claim to satisfy the required web, Android, and iOS product. Identify what architecture is shared and what additional clients/services/adapters are required.

F. Traceability:
Verify every FR-001–FR-015 and NFR-001–NFR-006 is mapped to architecture components, design decisions, workflows, and verification. Identify orphan components, unaddressed requirements, weak mappings, and claims unsupported by the specification.

G. Monetary/business correctness:
Verify payer credit, participant obligations, equal and percentage splits, currency precision/rounding boundaries, remainder allocation, balance reconciliation, approval gating, duplicate processing prevention, and payment limits. Flag ambiguous cases rather than inventing policy.

H. Open decisions:
Review persistence, currencies, reviewer permissions/self-approval, multi-level approval, notification channels, receipt formats/limits/access, report parameters/exports, payment corrections, password recovery verification, deployment, and cross-platform delivery. Identify which decisions require customer input versus engineering approval.

COMPARISON METHOD
1. Create a requirement-by-requirement matrix with columns:
   Requirement ID / customer intent / Spec Kit treatment / Codex treatment / evidence (file + section) / assessment / severity / recommended action.
2. Create a design-decision comparison table:
   Topic / Spec Kit proposal / Codex proposal / agreement or disagreement / trade-offs / recommendation / approval owner.
3. Identify:
   - common strengths;
   - common omissions;
   - unique strengths of each proposal;
   - direct contradictions;
   - unsupported assumptions;
   - overengineering and underengineering;
   - risks that neither proposal addresses.
4. Assess each proposal separately before recommending a combined direction.
5. Do not mark a requirement as satisfied merely because it is mentioned; check whether the architecture gives it a credible component, interaction, and verification approach.

SEVERITY
Use Critical, High, Medium, and Low. Explain the impact and why the severity applies. Distinguish a genuine requirement violation from an unresolved design choice.

REQUIRED REPORT
Produce:
1. Executive summary and overall recommendation.
2. Review scope, files inspected, and limitations.
3. Independent assessment of the Spec Kit proposal.
4. Independent assessment of the Codex proposal.
5. Requirement-by-requirement traceability and coverage matrix.
6. Constitution compliance matrix.
7. Architecture/design comparison and conflict register.
8. Critical and high-priority defects or gaps.
9. Security, privacy, monetary correctness, and data-integrity review.
10. MVP versus full-product scope review.
11. Open questions grouped by customer decision, product decision, and engineering decision.
12. Recommended consolidated architecture direction, clearly labelled as a recommendation rather than an approved design.
13. Prioritized action list with owner type (customer / human product owner / architect / developer / tester).
14. Human approval checklist and explicit go/no-go recommendation for proceeding to task generation and implementation.

OUTPUT SAFETY
Save the report only under:
architecture-review/chatgpt/

Use:
- architecture-review/chatgpt/cross-check-report.md
- architecture-review/chatgpt/requirement-coverage-matrix.md
- architecture-review/chatgpt/decision-and-conflict-register.md

Do not modify UserRequirement.md, constitution.md, spec.md, either tool's proposal, source code, or existing project files. Do not commit or merge anything. If ChatGPT cannot write directly to the repository, return the complete report contents for the human to save.

End with a clear statement of whether the architecture is ready for human approval, what must be resolved first, and whether it is safe to proceed to task generation. Do not generate implementation code.
```

---

## 4. Recommended execution order

1. Review and edit this prompt file before using it.
2. Confirm the clarified specification is ready for planning; it is currently marked Draft.
3. Run the Spec Kit prompt and save its output in the Spec Kit location.
4. Run Codex independently. Do not show it the Spec Kit output before it completes its own proposal.
5. Confirm both sets of files are present and unchanged.
6. Give ChatGPT both complete proposals plus the source documents and run the cross-check prompt.
7. Review the report and resolve critical/high issues.
8. Obtain human approval of the consolidated architecture and design.
9. Only then proceed to task generation and implementation planning.

## 5. Human approval checklist

- [ ] Original customer requirements remain unchanged and fully represented.
- [ ] The specification's Draft status and remaining open questions are acknowledged.
- [ ] Swing MVP is not represented as the full web/Android/iOS product.
- [ ] Every requirement has a credible architecture and verification mapping.
- [ ] BigDecimal and monetary allocation rules are correctly designed.
- [ ] GUI and business logic are separated.
- [ ] Security, authorization, history, and data integrity are addressed.
- [ ] All unresolved customer and engineering decisions are visible.
- [ ] PlantUML diagrams match the written architecture.
- [ ] Spec Kit and Codex differences have been reviewed by a human.
- [ ] Human approver has explicitly accepted the architecture before tasks or code are generated.

**Reminder:** This file contains prompts only. It is not the architecture, does not approve any proposed design, and does not authorize implementation.
