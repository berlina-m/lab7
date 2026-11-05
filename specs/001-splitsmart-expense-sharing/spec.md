# Feature Specification: SplitSmart Expense Sharing

**Feature Branch**: `001-splitsmart-expense-sharing`

**Created**: 2026-11-05

**Status**: Draft

**Input**: User description: "Read UserRequirement.md in this workspace as the authoritative customer requirements. Write a testable SplitSmart feature specification: actors, user scenarios, functional and non-functional requirements, acceptance criteria, requirement IDs (FR-001, etc.), and open questions. Preserve all original customer features in the requirements inventory; label the Java Swing MVP subset separately. Do not write Java code. Do not edit UserRequirement.md."

## User Scenarios & Testing *(mandatory)*

### Actors

- **Account holder**: A person who signs in to access their expenses, group memberships,
  balances, payments, and reports.
- **Group member**: An account holder who participates in shared expenses and receives
  relevant group expense notifications.
- **Group organizer**: A group member who creates or manages a group and configures its
  expense approval process.
- **Expense submitter**: A group member who records an expense and identifies the people
  and shares involved.
- **Expense reviewer**: A group member authorized by the group approval process to review
  submitted expenses.

An account holder may hold several of these roles. The customer has not specified separate
account types.

### User Story 1 - Record and approve a shared expense (Priority: P1)

A group member records an expense with its amount, date, description, split method,
participants, and optional receipt. The group’s reviewer or reviewers can approve or reject
it. Only approved expenses affect balances.

**Why this priority**: Recording and validating shared obligations is the foundation for
useful balance tracking.

**Independent Test**: Create a group with test members, submit an expense, review it, and
verify that its status and resulting balances match the review decision.

**Acceptance Scenarios**:

1. **Given** a signed-in group member and an active group, **When** they submit an expense
   with valid details and participant shares, **Then** the expense is saved as pending
   review and the group members receive an expense notification.
2. **Given** a pending expense and an authorized reviewer, **When** the reviewer approves
   it, **Then** it becomes approved and its shares are included in the affected members’
   balances exactly once.
3. **Given** a pending expense and an authorized reviewer, **When** the reviewer rejects
   it, **Then** it is marked rejected, its rejection is visible to the submitter, and it
   does not change balances.
4. **Given** an expense with an equal split, **When** it is approved, **Then** the expense
   is allocated equally among its selected participants, with any indivisible minor-unit
   remainder distributed according to a consistent, disclosed rule.
5. **Given** an expense with a percentage split, **When** it is submitted, **Then** the
   participant percentages total 100% and the corresponding shares total the expense
   amount after currency rounding.

---

### User Story 2 - Manage an account and groups (Priority: P1)

A person creates or uses their own account, signs in, and creates or manages groups for
shared activities. Group members can access only information they are permitted to see.

**Why this priority**: Accounts and groups establish the people and boundaries needed to
record shared expenses safely.

**Independent Test**: Create two accounts and a group, add members, sign in as each account,
and verify that group access and personal information are limited to the appropriate user.

**Acceptance Scenarios**:

1. **Given** a person without an account, **When** they register with valid account
   details and then sign in, **Then** they can access their own expense and balance
   information.
2. **Given** a group organizer, **When** they create a group and add or remove members,
   **Then** the group membership reflects each completed action.
3. **Given** an account that is not a member of a group, **When** it requests that group’s
   private expenses or balances, **Then** access is denied and no private data is shown.

---

### User Story 3 - Review balances and settle up (Priority: P1)

A group member sees the amount they owe to or are owed by each other member, records a
payment, and sees the updated outstanding balances.

**Why this priority**: Users need to understand and settle obligations produced by approved
expenses.

**Independent Test**: Approve an expense between test members, record a payment between
them, and compare the displayed outstanding balances with the expected amounts.

**Acceptance Scenarios**:

1. **Given** approved expenses involving multiple members, **When** a member views their
   balances, **Then** the system shows the net amount owed to or by each relevant member.
2. **Given** an outstanding balance, **When** a payer records a valid payment to the
   recipient, **Then** the payment is recorded and the outstanding balance is reduced by
   that amount without changing the historical expense.
3. **Given** a payment amount greater than the outstanding amount, **When** the payer
   attempts to record it, **Then** the system rejects it or explicitly records the excess
   as a credit according to a disclosed rule; the behavior is not silent.

---

### User Story 4 - Receive expense notifications (Priority: P2)

A group member is notified when another member creates a group expense, so they can stay
aware of new activity and review expenses when authorized.

**Why this priority**: Notifications make group activity visible and support timely review.

**Independent Test**: Have one member submit an expense and verify that other members of
that group receive a notification identifying the new expense without exposing it to
non-members.

**Acceptance Scenarios**:

1. **Given** a group with multiple members, **When** one member creates an expense,
   **Then** the other group members are notified of the new expense.
2. **Given** a person who is not a group member, **When** a group expense is created,
   **Then** that person receives no notification or expense details.

---

### User Story 5 - Review expense and payment reports (Priority: P2)

A group member reviews summaries of expenses, balances, and payments, and can request a
report for a specified date range and other available parameters.

**Why this priority**: Reports help members understand spending and verify settlement
history over time.

**Independent Test**: Enter known approved expenses and payments, request a report for a
defined period, and compare the report totals and included records with the source entries.

**Acceptance Scenarios**:

1. **Given** expenses and payments in a group, **When** a member requests a summary,
   **Then** the summary reports expense, balance, and payment information consistent with
   the underlying records.
2. **Given** a valid date range and report parameters, **When** a member requests a custom
   report, **Then** only matching records are included and the selected period is shown.

---

### Edge Cases

- An expense amount is zero, negative, malformed, or has more precision than the supported
  currency permits.
- Selected participants do not belong to the expense’s group, or the submitter is not
  authorized to add an expense to that group.
- Percentage shares are missing, negative, or do not total 100%; equal shares create a
  rounding remainder.
- An expense is rejected, edited, or submitted twice; it must not affect approved balances
  more than once.
- A reviewer tries to approve their own expense, or multiple reviewers act at nearly the
  same time.
- A group changes membership after an expense has been submitted or approved.
- A payment is partial, duplicates an existing payment, exceeds the outstanding amount,
  or is later corrected.
- A report has an empty, reversed, or boundary date range.
- An optional receipt is absent, unreadable, or not an accepted image type.
- A user loses access or signs out while an action is in progress.
- A notification delivery attempt fails; the expense’s saved status and balance outcome
  remain explicit and correct.

### Functional Requirements

- **FR-001**: The system MUST allow each person to create and use an individual account,
  sign in, and access that account’s permitted expense and balance information.
  **Acceptance**: A signed-in account can retrieve its own information; a different
  account cannot retrieve it without authorization.
- **FR-002**: The system MUST allow authorized users to create and manage groups and their
  memberships.
  **Acceptance**: Group creation and membership changes are reflected in subsequent group
  views, and non-members cannot access private group information.
- **FR-003**: The system MUST allow an authorized group member to create an expense with
  amount, date, description, split method, and selected involved users.
  **Acceptance**: A valid expense can be submitted with all required fields; invalid or
  unauthorized submissions are rejected with an actionable explanation.
- **FR-004**: The system MUST allow an expense to include an optional receipt image.
  **Acceptance**: An expense can be saved both with and without an accepted image; an
  invalid image is not silently treated as an attached receipt.
- **FR-005**: The system MUST support equal expense splits among selected participants.
  **Acceptance**: Each participant’s share is equal to the extent allowed by the currency’s
  minor units, and any remainder is allocated by a consistent disclosed rule.
- **FR-006**: The system MUST support percentage expense splits among selected
  participants.
  **Acceptance**: A split with non-negative percentages totaling exactly 100% is accepted;
  other percentage totals are rejected; calculated shares reconcile to the expense amount.
- **FR-007**: The system MUST provide a group-configured approval process for submitted
  expenses before they are included in balances.
  **Acceptance**: A pending expense does not affect balances; only an expense approved by
  the configured reviewer process affects balances; a rejected expense does not.
- **FR-008**: The system MUST retain and show each expense’s review status and decision
  history to users authorized to view it.
  **Acceptance**: Submitters and authorized reviewers can distinguish pending, approved,
  and rejected expenses and see the applicable decision.
- **FR-009**: The system MUST notify group members when another group member creates a new
  expense.
  **Acceptance**: Other members receive a notification associated with the group and
  expense; non-members receive no group expense notification.
- **FR-010**: The system MUST track the balance owed by each user to every other relevant
  user based on approved expenses and recorded payments.
  **Acceptance**: For a controlled set of expenses and payments, displayed pairwise
  balances equal the independently calculated net obligations.
- **FR-011**: The system MUST allow users to record payments made to settle balances,
  including payer, recipient, amount, and date.
  **Acceptance**: A valid payment appears in payment history and reduces the applicable
  outstanding balance exactly once.
- **FR-012**: The system MUST provide summaries of users’ expenses, balances, and
  payments.
  **Acceptance**: Summary totals reconcile with the authorized underlying records.
- **FR-013**: The system MUST allow users to request reports using a specified date range
  and other available report parameters.
  **Acceptance**: The generated report states its parameters and includes only records
  matching them.
- **FR-014**: The complete product MUST be available primarily through the web and also
  through applications that run on Android and iOS.
  **Acceptance**: A user can access the required account, group, expense, approval,
  balance, payment, notification, and reporting capabilities through the web and through
  supported Android and iOS applications.
- **FR-015**: The system MUST preserve historical expense and payment records when a
  group’s membership changes.
  **Acceptance**: A membership change does not silently rewrite or remove previously
  recorded transactions; access to historical records follows the group’s disclosed
  access policy.

### Non-Functional Requirements

- **NFR-001**: The system MUST prevent access to account and group data by users who are
  not authorized to view it.
  **Acceptance**: Access-control tests covering own-account, group-member, and non-member
  views produce no unauthorized expense, balance, receipt, or payment data.
- **NFR-002**: The system MUST maintain monetary accuracy for supported currencies.
  **Acceptance**: For all supported test cases, shares, balances, and payments reconcile
  to the corresponding transaction totals at the currency’s declared precision.
- **NFR-003**: The system MUST make expense status and balance effects understandable to
  users.
  **Acceptance**: In usability testing, users can correctly identify whether a submitted
  expense is pending, approved, or rejected and whether it affects their balance.
- **NFR-004**: The system MUST provide consistent results when the same transaction is
  viewed by different authorized group members.
  **Acceptance**: Concurrent views of an unchanged group transaction show the same status,
  participants, shares, and balance effects.
- **NFR-005**: The system MUST provide a user-visible confirmation or actionable error
  after each account, group, expense, review, payment, or report action.
  **Acceptance**: Each tested action ends with an explicit success result or an actionable
  failure; no failed action is presented as successful.
- **NFR-006**: The system MUST offer the complete product capability set across the
  customer-requested web, Android, and iOS channels.
  **Acceptance**: Cross-channel verification confirms that each required capability in
  FR-001 through FR-013 is available through all three requested channels.

### Key Entities

- **Account**: A person’s individual identity and access to their own information.
- **Group**: A shared context with members, expenses, and an approval process.
- **Membership**: A person’s participation and permissions within a group.
- **Expense**: A shared cost with amount, date, description, submitter, participants,
  split method, shares, optional receipt, and review status.
- **Expense share**: A participant’s portion of an expense, determined equally or by
  percentage.
- **Review decision**: An approval or rejection of an expense by an authorized reviewer.
- **Notification**: An alert to a group member about a newly created expense.
- **Balance**: A user’s net amount owed to or by another user based on approved expenses
  and recorded payments.
- **Payment**: A dated transfer recorded between a payer and recipient to settle a
  balance.
- **Report**: A summary or filtered view of expenses, balances, and payments.

### Requirements Inventory

The following inventory preserves the original customer-requested feature set. The FR
references are the testable specification of each item.

| Original customer feature | Requirement references |
|---|---|
| Individual user accounts, login, and access to own expense and balance information | FR-001 |
| Create and manage groups of people | FR-002 |
| Create expenses with amount, date, description, and equal or percentage splits | FR-003, FR-005, FR-006 |
| Attach an optional receipt image | FR-004 |
| Specify users involved in each expense and adjust appropriate balances | FR-003, FR-010 |
| Notify users when other group members create an expense | FR-009 |
| Review and approve expenses before balance adjustments, with a group-appropriate approval process | FR-007, FR-008 |
| Track balances owed between users across entered expenses | FR-010 |
| Record payments to settle balances | FR-011 |
| Provide expense, balance, and payment reports, including date ranges and parameters | FR-012, FR-013 |
| Primary web product and applications runnable on Android and iOS | FR-014, NFR-006 |

### Java Swing MVP Subset (Implementation Decision; Not Full Customer Scope)

The following is a separately labeled implementation subset for a Java Swing MVP or
prototype. It does not redefine or satisfy the complete customer product scope. The
customer explicitly requires web, Android, and iOS delivery; a Swing-only application is
not a substitute for those channels.

- Account access and basic group creation and membership management (FR-001, FR-002).
- Expense entry with amount, date, description, participants, equal or percentage split,
  and optional receipt (FR-003 through FR-006).
- A single group-configured review step, with pending, approved, and rejected states
  (FR-007, FR-008).
- In-application notification of new group expenses (FR-009).
- Pairwise balance display and payment recording (FR-010, FR-011).
- Basic summaries and date-range reports (FR-012, FR-013).

This subset is a scope label only. It does not decide persistence, authentication
mechanisms, image limits, currencies, or how the separate web and mobile applications
will be delivered.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: In acceptance tests, 100% of valid equal-split expenses reconcile to the
  submitted amount after applying the disclosed currency-rounding rule.
- **SC-002**: In acceptance tests, 100% of valid percentage splits total 100% and their
  calculated shares reconcile to the expense amount; invalid totals are rejected.
- **SC-003**: In approval tests, 100% of pending and rejected expenses produce no balance
  adjustment, and each approved expense adjusts balances exactly once.
- **SC-004**: For every controlled test dataset, pairwise balances reconcile to approved
  expense shares minus recorded payments.
- **SC-005**: In notification tests, all other group members receive a new-expense
  notification and no non-member receives the expense details.
- **SC-006**: At least 90% of representative users in acceptance testing complete account
  access, group expense submission and review, balance inspection, and payment recording
  on their first attempt without assistance.
- **SC-007**: The accepted product provides the complete feature set on web, Android, and
  iOS; a Swing-only MVP does not meet this criterion.
- **SC-008**: In report tests, every generated summary and date-filtered report reconciles
  to the authorized source transactions and stated report parameters.

## Assumptions

- The MVP approval process uses one authorized reviewer unless a group chooses a different
  supported approval configuration; the customer allows either single or multiple review.
- A pending or rejected expense does not affect balances. An approved expense is included
  once, regardless of repeated review actions.
- Supported currency precision and the rule for distributing indivisible equal-split
  remainders will be explicitly disclosed before expenses are used for settlement.
- Percentage shares must be non-negative and total 100%; invalid totals are rejected.
- Group organizers can manage membership and configure approval; group members can submit
  expenses. Exact permissions remain subject to product policy.
- Notifications in the MVP may be in-application notifications; the customer has not
  specified delivery channels.
- The Java Swing MVP is a subset/prototype and does not replace the required primary web
  product or Android and iOS applications.
- The specification does not assume a particular authentication provider, storage model,
  receipt format or size limit, supported currency list, or report export format.

## Open Questions

These questions do not remove or narrow any original customer feature. Defaults above
allow planning to proceed; answers may refine the acceptance criteria.

1. Which currencies and currency precision rules must be supported, and how should a
   minor-unit remainder be assigned in equal splits?
2. Which group roles may approve expenses, and can submitters approve their own expenses?
   Are multi-level or quorum approvals required for any groups?
3. Which notification channels are required in addition to in-application notifications
   (for example, email or mobile push), and are user preferences needed?
4. What receipt image formats and size limits are acceptable, and who can view receipts
   after a person leaves a group?
5. Which report parameters and output formats are required beyond date range, and should
   users be able to export reports?
6. What behavior is intended for overpayments, payment corrections, and expenses changed
   after submission or approval?
7. Which account recovery and authentication policies are required?
8. What release boundary is intended for the Java Swing MVP relative to the complete web,
   Android, and iOS product?
