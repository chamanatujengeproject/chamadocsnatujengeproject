# FundLoop — Business Operations

---

## Overview

FundLoop supports three types of Chamas:

- **ROSCA / Merry-Go-Round** — most common
- **Table Banking / Loan Chama** — most complex



Fines, meetings, notifications, and member exit logic are shared across all three types.

---

## Roles

**Member** — regular Chama participant. Makes contributions, applies for loans, votes. Lowest access level.

**Guarantor** — member who vouches for another member's loan. Has a special liability role. Subset of Member.

**Treasurer** — manages all financial operations. Approves loans, tracks payments. Financial authority.

**Chairperson** — governs the Chama. Final approval on key decisions. Manages the constitution. Highest role.

**Secretary** — manages meetings, attendance records, and member communications. Administrative role.

**Sys Admin** — platform-level access. Manages Chama onboarding and system health.

---

## Core System Entities

**Member** — member_id, name, phone, email, national_id, joining_date, status, chama_id

**Chama** — chama_id, name, type, constitution_id, created_by, status, created_at

**Contribution** — contribution_id, member_id, chama_id, cycle_id, amount_expected, amount_paid, status, paid_at

**Cycle** — cycle_id, chama_id, cycle_type, start_date, end_date, status

**Transaction** — txn_id, chama_id, member_id, type, amount, channel, reference, created_at

**Fine** — fine_id, member_id, chama_id, trigger_event, amount, status, waived_by, waiver_reason

**Notification** — notif_id, member_id, chama_id, type, channel, message, sent_at, read_at

**Meeting** — meeting_id, chama_id, agenda, date, location, type, minutes_url, created_by

**Audit Log** — log_id, chama_id, actor_id, event_type, entity_id, before_state, after_state, timestamp

---

## ROSCA-Specific Entities

**Payout Schedule** — schedule_id, chama_id, member_id, position, cycle_number, payout_date, status
- status: `upcoming | released | skipped`

**Position Swap** — swap_id, requester_id, target_id, approved_by, reason, status, created_at
- status: `pending | approved | rejected`

**Shortfall Record** — shortfall_id, chama_id, member_id, amount, recovery_status, created_at
- triggered when a post-payout member defaults

---

## Table Banking / Loan Entities

**Loan** — loan_id, member_id, chama_id, principal, interest_rate, interest_model, duration_months, status
- status: `applied | approved | rejected | disbursed | active | repaid | defaulted`

**Repayment** — repayment_id, loan_id, instalment_no, amount_due, amount_paid, due_date, paid_at, status
- status: `upcoming | paid | partial | overdue`

**Guarantor** — guarantor_id, loan_id, member_id, status, accepted_at, liability_amount
- status: `invited | accepted | declined`

**Loan Approval** — approval_id, loan_id, approved_by, vote_id (nullable), decision, reason, decided_at
- links to Vote if constitution requires member vote

**Loan Penalty** — penalty_id, loan_id, repayment_id, amount, assessed_at, status
- triggered on overdue repayment; compounds per cycle

---

## ROSCA / Merry-Go-Round

### How It Works

All members contribute the same fixed amount every cycle. The entire pool is paid out to one member per cycle, rotating until every member has received once.

Core business rules:

- All members must contribute the same fixed amount per cycle.
- A member who has received their payout is still obligated to contribute for all remaining cycles.
- Payout is only released when all member contributions for that cycle are confirmed.
- Rotation order is defined at group creation; changes require Treasurer approval.
- A member who exits after receiving payout triggers a shortfall recovery process.
- Fines for late contributions are assessed automatically; payout is not blocked by fines alone.
- If the next-in-rotation member has an outstanding fine, Treasurer may hold payout pending resolution.

### Workflow: Member Contributes

1. System opens the cycle on the configured start date and generates contribution records for all members with status = `pending`.
2. System sends payment reminders 7 days before cycle close to all members still on `pending`.
3. Member initiates payment via M-Pesa STK Push, card, or manual entry by Treasurer.
4. Payment gateway confirms receipt. System records the Transaction and updates Contribution status to `complete` or `partial`.
5. Treasurer is notified of payment confirmation in real time.
6. On cycle close date, system checks for any contributions still `pending` or `partial` and auto-assesses late fines.
7. System checks: all contributions = `complete`? If yes, unlock payout for the next-in-rotation member.
8. Treasurer reviews the payout confirmation screen and releases payout to the designated member via M-Pesa.
9. Payout Transaction is recorded. Payout Schedule status → `released`. Cycle status → `closed`. Next cycle opens.

### Payout Release Gate — Decision Logic

- All contributions this cycle = `complete` → **Pass**. Unlock payout release button for Treasurer.
- One or more contributions = `partial` → **Fail**. Payout locked. Treasurer notified. Shortfall amount shown.
- One or more contributions = `pending` → **Fail**. Payout locked. Overdue reminders sent. Fine auto-assessed.
- Payout member has waived obligation (admin override) → **Pass**. Treasurer confirms. Override is audit-logged with reason.
- Payout member is flagged inactive or exited → **Fail**. Skip to next-in-rotation. Treasurer must confirm skip. Original member's position archived.

### Workflow: Position Swap Request

1. Member A submits a position swap request, selects target Member B, and provides a reason. Swap status = `pending`.
2. System notifies Member B. Member B has 48 hours to accept or decline.
3. Member B accepts or declines. Swap status → `accepted` or `declined`.
4. If Member B accepts: Treasurer reviews the request and confirms both members have no outstanding obligations.
5. On Treasurer approval: Payout Schedule is updated and both positions are swapped.
6. Swap record is archived in Audit Log with both member confirmations and Treasurer approval.

### Edge Case: Member Exits After Receiving Payout

If a member has already received their payout and then exits, they create a financial shortfall.

- The shortfall = remaining cycles × contribution amount the exited member will no longer pay.
- System immediately generates a Shortfall Record and notifies Treasurer.
- Treasurer selects a recovery option:
  - (a) Distribute shortfall equally among remaining members.
  - (b) Reduce remaining payout amounts proportionally.
  - (c) Recover from the exiting member's savings balance.
- If recovering from the member's savings: system calculates net settlement before releasing the exit balance.
- Recovery method must be confirmed by Chairperson and is audit-logged.

### Governance Rules — ROSCA

- Rotation order is set by Chairperson at group creation. Changing it after the first cycle requires Chairperson approval only.
- Position swap between existing members: Treasurer approval is sufficient.
- Adding a new member mid-cycle: Chairperson approval + simple majority member vote required.
- Removing a member mid-cycle who has not yet received payout: Chairperson decision with justification.
- Changing contribution amount between full ROSCA cycles: Chairperson approval + member vote.
- Pausing or suspending a cycle: Chairperson decision; all members notified.

---

## Table Banking / Loan Chama

### How It Works

Contributions accumulate in a central loan pool. Members borrow from this pool and repay with interest. Interest income grows the pool and is distributed as dividends at year-end or another configured interval. This model operates as a member-owned mini-bank.

Core business rules:

- Money does not rotate out — it stays in the fund and grows through interest income.
- Maximum borrowable amount is configurable per constitution (e.g. up to 3× member's total contributions).
- Interest model is set at Chama creation: flat-rate, reducing balance, or fixed monthly rate.
- Loans above a configured threshold require guarantors.
- Loan approval is either Treasurer single-approval or member vote, per constitution.
- A member with an active overdue loan cannot apply for a new loan.
- Guarantors must digitally accept their guarantee before the loan is disbursed.
- On default: penalties compound. Guarantors are notified and liable for the outstanding balance.
- Interest earned is credited to the loan pool and distributed as dividend at configured intervals.

### Workflow: Loan Application and Approval

1. Member opens the loan application form, enters amount requested, purpose, and preferred duration. Status = `applied`.
2. System runs eligibility check: (a) no active overdue loan, (b) requested amount ≤ max multiplier × contributions.
3. If amount ≥ guarantor threshold: system prompts member to nominate 1–3 guarantors from the Chama.
4. System sends guarantee invitations to each nominated guarantor. 48-hour acceptance window.
5. Each guarantor reviews loan details and accepts or declines.
6. If all required guarantors accept: application moves to approval queue. If any decline: member is notified to nominate a replacement.
7. Treasurer reviews the application, member contribution history, and guarantor list. Approves or rejects. Status → `approved` or `rejected`.
8. If approved: system generates repayment schedule and presents it to the member for confirmation.
9. Member reviews and digitally accepts the repayment schedule.
10. Treasurer initiates disbursement via M-Pesa to the member's registered number.
11. Transaction recorded. Loan status → `active`. Repayment reminders scheduled. Loan pool balance updated.

### Loan Eligibility — Decision Logic

- Member has an active overdue loan → **Fail**. Application rejected immediately. Member shown outstanding balance.
- Requested amount > max multiplier × contributions → **Fail**. System shows member's current max eligible amount.
- Loan pool balance < requested amount → **Fail**. Treasurer notified. Member informed of pool constraints.
- All conditions pass, amount < guarantor threshold → **Pass**. Moves directly to Treasurer approval queue.
- All conditions pass, amount ≥ guarantor threshold → **Pass***. Moves to guarantor nomination step before Treasurer review.

### Workflow: Loan Repayment

1. System sends repayment reminder 7 days before each instalment due date via SMS and push.
2. Second reminder 3 days before. Final reminder 1 day before.
3. Member makes repayment via M-Pesa or card.
4. Payment confirmed. Repayment record updated. Loan pool balance increased. Status → `paid`.
5. System recalculates remaining balance and outstanding instalments. Member notified.
6. If due date passes with no payment: status → `overdue`. Penalty calculated and assessed. Treasurer notified.
7. If loan fully repaid: Loan status → `repaid`. Guarantors released from obligation. Member notified.

### Default Handling and Guarantor Liability

1. Penalty compounds daily or weekly per configured rate on the outstanding balance.
2. After X days overdue (configurable, e.g. 30 days): loan flagged as `defaulted`. Treasurer notified.
3. System calculates guarantor liability: outstanding balance ÷ number of accepting guarantors.
4. Guarantors are notified of their individual liability amounts and given a payment deadline.
5. If a guarantor pays their share: their obligation is cleared. Remaining balance tracked against the defaulting member.
6. A guarantor who defaults on their liability: a new fine is raised against them.
7. Treasurer documents recovery outcome. Loan archived with default record.
8. Defaulting member is flagged and cannot apply for new loans until the default is cleared.


