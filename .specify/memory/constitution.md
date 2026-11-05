<!--
Sync Impact Report
Version change: unversioned scaffold → 1.0.0
Modified principles: None (initial adoption)
Added principles: I. Customer Requirements and Traceability; II. Java 21 and Maven; III. Object-Oriented Design and Swing Separation; IV. Exact Monetary Arithmetic; V. Automated Verification, Architecture Diagrams, and Human Review
Added sections: Customer Requirements and Implementation Decisions; Engineering Workflow
Removed sections: None
Follow-up TODOs: Confirm the original ratification date; the source project does not record it.
-->
# SplitSmart Constitution

## Core Principles

### I. Customer Requirements and Traceability
The customer request in `UserRequirement.md` is authoritative for product behavior and
delivery scope. Every approved customer requirement MUST have a stable identifier and be
traceable to its design decisions and verification tests. Updates to requirements MUST
preserve or revise those links. Implementation choices MUST be identified as such and MUST
NOT be represented as customer requirements.

### II. Java 21 and Maven
Project code MUST target Java 21, and Maven MUST be the authoritative build, dependency,
and test lifecycle. Changes to the Java version or build system require an approved
constitution amendment or a documented, approved exception.

### III. Object-Oriented Design and Swing Separation
The application MUST use object-oriented design to keep responsibilities cohesive and
business invariants explicit. Java Swing MUST be confined to presentation and interaction;
business rules and calculations MUST reside in components that do not depend on Swing.
The GUI MUST invoke those components rather than implementing domain behavior in event
handlers. This separation keeps core behavior independently testable and protects it from
presentation changes.

### IV. Exact Monetary Arithmetic
All monetary amounts and monetary calculations MUST use `BigDecimal`. Floating-point
types MUST NOT be used for money. Scale and rounding MUST be explicit at defined business
boundaries, consistently applied, and consistent with approved requirements.

### V. Automated Verification, Architecture Diagrams, and Human Review
Automated tests MUST use JUnit 5. Tests MUST verify business behavior and the requirement
traceability they cover. PlantUML diagrams MUST be updated when a change affects documented
architecture or important component interactions. AI-generated code, tests, diagrams, and
documentation MUST receive human review for correctness, requirement alignment, and
consistency before acceptance; AI output is not authoritative.

## Customer Requirements and Implementation Decisions

The customer request specifies accounts, groups, shared expenses (including equal or
percentage splits and optional receipts), notifications, expense approval, balances,
payment tracking, reporting, and web delivery with Android and iOS apps. These remain
customer requirements unless changed through an approved customer-facing requirements
process.

Java 21, Maven, Java Swing, JUnit 5, PlantUML, and `BigDecimal` are implementation decisions
and engineering constraints, not customer requirements. In particular, a Swing interface
MUST NOT be treated as satisfying the requested web, Android, or iOS delivery scope.
Any mismatch between an implementation choice and customer scope MUST be made explicit
and resolved through the requirements process before the product is represented as meeting
that scope.

## Engineering Workflow

Changes MUST identify the requirements they address and the tests that verify them.
JUnit 5 tests MUST be run for affected behavior before acceptance, and the Maven build
MUST succeed. Reviewers MUST check requirement traceability, separation of GUI and
business logic, correct monetary arithmetic, and relevant PlantUML updates. Human review
of AI-assisted work is mandatory under Principle V.

## Governance

This constitution governs project decisions and supersedes conflicting local practices.
Amendments MUST be reviewed and approved, update this document, and include a rationale
and an impact assessment for affected requirements, designs, tests, and guidance.
Constitution versions use semantic versioning: MAJOR for incompatible principle or
governance changes, MINOR for new principles or materially expanded requirements, and
PATCH for clarifications and non-semantic refinements.

Every change and review MUST assess compliance with this constitution. Any exception MUST
be documented with its scope, rationale, approver, and duration; temporary exceptions MUST
be revisited before expiration. AI-generated material is subject to the same review and
approval requirements as other project changes.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): Original adoption date unknown | **Last Amended**: 2026-11-05
