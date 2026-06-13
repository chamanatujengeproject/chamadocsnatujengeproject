# FUNDLOOP — WORKFLOW & EVENT FLOW DOCUMENT

> **Reading order:** Domain Model → Business Rules → **Workflows (this doc)**
>
> Workflows answer: **HOW does the system move from one state to the next?**

---

## WORKFLOW TAXONOMY

```
WF-MEM   →  Membership Workflows
WF-ROS   →  ROSCA Workflows
WF-WEL   →  Welfare Workflows
WF-FIN   →  Finance Workflows
WF-GOV   →  Governance Workflows
```

---

# WF-MEM — MEMBERSHIP WORKFLOWS

---

## WF-MEM-001: Member Registration & Chama Join

**Trigger:** User signs up and joins or creates a chama.

```
[User signs up]
      │
      ▼
[Identity & Access Domain]
  Create UserAccount
  Issue AuthToken
      │
      ▼
[Membership Domain]
  Create Member record
      │
      ├─── Creating new chama?
      │         │
      │         ▼
      │    [Create Chama]
      │    Assign role: CHAIRMAN
      │    Assign role: TREASURER (can be self)
      │
      └─── Joining existing chama?
                │
                ▼
          [Invitation exists?]
               │
        YES ───┤
               ▼
        Create Membership
        role: MEMBER
        status: ACTIVE
               │
        NO ────┤
               ▼
        Reject with: "Invitation required"
      │
      ▼
[Event Published]
  → MemberJoined
      │
      ▼
[Notification Domain listens]
  → SMS/Email: "Welcome to {ChamaName}"

[Audit Domain listens]
  → Log: MemberJoined
```

**Business Rules Applied:**
- BR-MEM-001: Must be registered member
- BR-MEM-002: Cannot join same chama twice

---

## WF-MEM-002: Member Suspension

**Trigger:** Chairman or Admin suspends a member.

```
[Chairman initiates suspension]
      │
      ▼
[Authorization Check]
  Role = CHAIRMAN or ADMIN?
  NO → Reject: "Unauthorized"
      │
      ▼
[Membership Domain]
  Member.status → SUSPENDED
      │
      ▼
[Event Published]
  → MemberSuspended
      │
      ├── [ROSCA Domain]
      │     Remove from active rotation
      │
      ├── [Welfare Domain]
      │     Mark active claims → FROZEN
      │
      └── [Notification Domain]
            SMS to member: "Your account has been suspended"

[Audit Domain]
  → Log: MemberSuspended, by whom, timestamp
```

**Business Rules Applied:**
- BR-MEM-004: Suspended member cannot contribute, vote, or receive payout

---

# WF-ROS — ROSCA WORKFLOWS

---

## WF-ROS-001: Start a New ROSCA Cycle

**Trigger:** Treasurer initiates a new cycle.

```
[Treasurer clicks "Start Cycle"]
      │
      ▼
[Authorization Check]
  Role = TREASURER?
  NO → Reject
      │
      ▼
[ROSCA Domain]
  Validate: no active cycle exists for this chama
  Create RoscaCycle
    status: OPEN
    contributionAmount: {fixed per chama rules}
    deadline: {set date}
      │
      ▼
[Rotation Order Assigned]
  Method = FIXED_ORDER?  → use existing order
  Method = LOTTERY?      → randomize positions
      │
      ▼
[Event Published]
  → CycleStarted
      │
      └── [Notification Domain]
            SMS all members: "Cycle #{N} started. Contribute KES {amount} by {deadline}"

[Audit Domain]
  → Log: CycleStarted
```

**Business Rules Applied:**
- BR-GOV-001: Only Treasurer can initiate
- BR-ROSCA-001: Fixed contribution amount
- BR-ROSCA-003: Rotation order assigned

---

## WF-ROS-002: Member Contribution

**Trigger:** Member pays their cycle contribution.

```
[Member submits payment]
      │
      ▼
[Finance Domain]
  Check idempotency key (BR-FIN-003)
  Duplicate? → Return existing result, stop
      │
      ▼
[Validation]
  Amount = required contribution amount?
  NO → Reject: "Incorrect amount"

  Member status = ACTIVE?
  NO → Reject: "Suspended members cannot contribute"

  Cycle status = OPEN?
  NO → Reject: "No open cycle"
      │
      ▼
[Finance Domain — Atomic Transfer] (BR-FIN-004)
  Member Wallet  →  debit {amount}
  Chama Wallet   →  credit {amount}
  Create Transaction record
  Create LedgerEntry
      │
      ▼
[ROSCA Domain]
  Create ContributionRecord
    status: PAID
    cycleId, memberId, amount, timestamp
      │
      ▼
[Event Published]
  → ContributionPaid
      │
      ├── [Notification Domain]
      │     SMS member: "Contribution received ✓"
      │
      └── [ROSCA Domain — check trigger]
            All members paid?
            YES → trigger WF-ROS-003 (Payout Release)

[Audit Domain]
  → Log: ContributionPaid
```

**Business Rules Applied:**
- BR-MEM-001, BR-MEM-004
- BR-ROSCA-001: Fixed amount
- BR-ROSCA-002: All must pay before payout
- BR-FIN-001: No negative wallet
- BR-FIN-003: Idempotency
- BR-FIN-004: Atomic transfer
- BR-FIN-005: Ledger entry

---

## WF-ROS-003: Payout Release

**Trigger:** All contributions collected for cycle → auto-triggered, or Treasurer manually initiates.

```
[All contributions received OR Treasurer initiates]
      │
      ▼
[ROSCA Domain — Pre-checks]
  All active members contributed?
  NO → Reject: "Contributions incomplete"

  Payout already exists for this cycle?
  YES → Reject: "Payout already issued" (BR-ROSCA-005)
      │
      ▼
[Determine Recipient]
  Lookup next RotationPosition
  Member at position: ACTIVE and ELIGIBLE?
  NO (suspended/missed) → skip, move to next
      │
      ▼
[Finance Domain — Atomic Transfer] (BR-FIN-004)
  Chama Wallet   →  debit {payout amount}
  Member Wallet  →  credit {payout amount}
  Create Transaction record
  Create LedgerEntry
      │
      ▼
[ROSCA Domain]
  Create Payout record
    cycleId, recipientMemberId, amount, timestamp
  RoscaCycle.status → COMPLETED
  RotationPosition → mark as RECEIVED
      │
      ▼
[Event Published]
  → PayoutReleased
      │
      ├── [Notification Domain]
      │     SMS recipient: "KES {amount} payout sent to your wallet"
      │     SMS all: "Cycle #{N} complete. Next: {nextMember}"
      │
      └── [Audit Domain]
            Log: PayoutReleased, recipient, amount

[Reporting Domain]
  Update: RoscaCycleReport
```

**Business Rules Applied:**
- BR-ROSCA-002: Full contribution before payout
- BR-ROSCA-005: One payout per cycle
- BR-GOV-001: Treasurer authority
- BR-FIN-004: Atomic
- BR-FIN-005: Ledger

---

## WF-ROS-004: Missed Contribution Handling

**Trigger:** Cycle deadline passes with outstanding contributions.

```
[Scheduler: deadline passed]
      │
      ▼
[ROSCA Domain]
  Find all members in cycle with no ContributionRecord
      │
      ▼
  For each missing member:
    ContributionRecord.status → MISSED
    Apply penalty per chama policy:
      │
      ├── FINE: create PenaltyRecord
      ├── DOUBLE_NEXT: flag for next cycle
      └── SKIP_TURN: mark RotationPosition ineligible
      │
      ▼
[Event Published]
  → ContributionMissed (per member)
      │
      └── [Notification Domain]
            SMS member: "You missed your contribution. Penalty applied."

[Audit Domain]
  → Log: MissedContribution, penalty type
```

**Business Rules Applied:**
- BR-ROSCA-004: Missed contribution penalty
- BR-MEM-004: Cannot receive payout

---

# WF-WEL — WELFARE WORKFLOWS

---

## WF-WEL-001: Submit Welfare Claim

**Trigger:** Member submits an emergency welfare request.

```
[Member submits claim]
  emergencyType, amount, evidence attached
      │
      ▼
[Welfare Domain — Validation]
  Member status = ACTIVE? (BR-WEL-002)
  NO → Reject

  emergencyType in [medical, funeral, disaster]? (BR-WEL-001)
  NO → Reject: "Emergency type not covered"

  Member has active claim already? (BR-WEL-006)
  YES → Reject: "One active claim allowed"

  Evidence attached? (BR-WEL-005)
  NO → Reject: "Evidence required"

  Amount ≤ 30% of welfare pool balance? (BR-WEL-003)
  NO → Reject: "Amount exceeds limit"
      │
      ▼
[Welfare Domain]
  Create Claim
    status: PENDING_APPROVAL
    claimant, amount, type, evidence
      │
      ▼
[Governance Domain]
  Create Vote record
    required: 70% approval (BR-WEL-004)
    voters: all active chama members
      │
      ▼
[Event Published]
  → ClaimSubmitted
      │
      └── [Notification Domain]
            SMS all members: "New welfare claim. Vote required."

[Audit Domain]
  → Log: ClaimSubmitted
```

---

## WF-WEL-002: Welfare Claim Voting & Approval

**Trigger:** Members vote on a pending claim.

```
[Member submits vote: APPROVE or REJECT]
      │
      ▼
[Governance Domain]
  Record vote
  Check: has this member already voted?
  YES → Reject: "Already voted"
      │
      ▼
[Tally votes]
  approvalRate = approvals / totalVoters

  approvalRate ≥ 70%?
  │
  YES ──────────────────────────────────────────┐
  │                                             │
  NO: wait for more votes                       ▼
  │                                    [Claim.status → APPROVED]
  │                                             │
  Rejection threshold crossed?                 ▼
  YES → Claim.status → REJECTED        [WF-WEL-003: Disbursement]
      │
      ▼
[Event Published]
  → ClaimApproved OR ClaimRejected
      │
      └── [Notification Domain]
            SMS claimant: "Your claim was approved/rejected"

[Audit Domain]
  → Log result
```

**Business Rules Applied:**
- BR-WEL-004: 70% threshold
- BR-GOV-002: Chairman veto power

---

## WF-WEL-003: Welfare Disbursement

**Trigger:** Claim approved → auto-triggers disbursement.

```
[Claim.status = APPROVED]
      │
      ▼
[Finance Domain — Validation]
  WelfarePool.balance ≥ claim amount?
  NO → Claim.status → PENDING_FUNDS (wait for top-up)
      │
      ▼
[Finance Domain — Atomic Transfer] (BR-FIN-004)
  WelfarePool  →  debit {amount}
  Member Wallet →  credit {amount}
  Create Transaction
  Create LedgerEntry
      │
      ▼
[Welfare Domain]
  Create Disbursement record
  Claim.status → DISBURSED
      │
      ▼
[Event Published]
  → WelfareDisbursed
      │
      └── [Notification Domain]
            SMS member: "KES {amount} welfare support sent"

[Audit Domain]
  → Log: WelfareDisbursed
```

---

# WF-FIN — FINANCE WORKFLOWS

---

## WF-FIN-001: Wallet Top-Up (Deposit)

**Trigger:** Member deposits money into wallet via M-Pesa/card.

```
[Member initiates deposit]
  amount, paymentMethod
      │
      ▼
[Finance Domain]
  Validate idempotency key (BR-FIN-003)
  amount > 0? (BR-VAL-001)
      │
      ▼
[Payment Gateway]
  Process external payment
  FAILED → Retry / notify member
      │
      ▼
[Finance Domain — on success]
  Member Wallet balance += amount
  Create Transaction (type: DEPOSIT)
  Create LedgerEntry (BR-FIN-005)
      │
      ▼
[Event Published]
  → WalletTopUp
      │
      └── [Notification Domain]
            SMS: "KES {amount} deposited to your wallet"
```

---

## WF-FIN-002: Transaction Reversal

**Trigger:** Error detected on a completed transaction.

```
[Treasurer/Admin initiates reversal]
      │
      ▼
[Authorization Check]
  Role = TREASURER or ADMIN?
  NO → Reject
      │
      ▼
[Finance Domain]
  Original transaction: status = COMPLETED?
  NO → Reject: "Can only reverse completed transactions"

  Create REVERSAL transaction
    type: REVERSAL
    references: originalTransactionId
      │
      ▼
[Finance Domain — Atomic Transfer]
  Reverse debit/credit
  Update both wallet balances
  Create LedgerEntry for reversal
      │
      ▼
[Event Published]
  → TransactionReversed

[Audit Domain]
  → Log: reversal, reason, actor
```

**Business Rules Applied:**
- BR-FIN-002: Immutable transactions → reversal only, no edits

---

# WF-GOV — GOVERNANCE WORKFLOWS

---

## WF-GOV-001: Rule Change Proposal & Vote

**Trigger:** Chairman proposes a rule change (e.g., raise contribution amount).

```
[Chairman submits proposal]
  ruleType, currentValue, proposedValue, reason
      │
      ▼
[Governance Domain]
  Create Rule proposal
  Create Vote record
    required: 50% + 1 (BR-GOV-003)
      │
      ▼
[Event Published]
  → RuleChangeProposed
      │
      └── [Notification Domain]
            SMS all members: "Vote required: {proposal summary}"
      │
      ▼
[Members vote]
      │
      ▼
[Tally]
  Majority reached?
  YES → Rule.value updated
        Event: RuleChanged
  NO  → Proposal.status → REJECTED
        Event: RuleRejected
      │
      ▼
[Audit Domain]
  → Log: who voted, outcome, timestamp
```

---

# CROSS-DOMAIN EVENT MAP

```
EVENT                   PUBLISHER          SUBSCRIBERS
─────────────────────────────────────────────────────────────────
MemberJoined            Membership         Notification, Audit
MemberSuspended         Membership         ROSCA, Welfare, Notification, Audit
CycleStarted            ROSCA              Notification, Audit
ContributionPaid        Finance/ROSCA      Notification, Audit, ROSCA (payout check)
ContributionMissed      ROSCA              Notification, Audit
PayoutReleased          Finance/ROSCA      Notification, Audit, Reporting
ClaimSubmitted          Welfare            Notification, Audit, Governance
ClaimApproved           Governance         Welfare (disbursement), Notification, Audit
ClaimRejected           Governance         Notification, Audit
WelfareDisbursed        Finance/Welfare    Notification, Audit
WalletTopUp             Finance            Notification, Audit
TransactionReversed     Finance            Notification, Audit
RuleChanged             Governance         All domains (rule reload), Audit
```

---

# WORKFLOW STATE MACHINES

## ROSCA Cycle States

```
DRAFT → OPEN → IN_PROGRESS → COMPLETED
                     │
                     └──(missed contributions)──→ PARTIAL_COMPLETION
```

## Welfare Claim States

```
PENDING_APPROVAL → APPROVED → DISBURSED
        │               │
        ▼               └──(insufficient funds)──→ PENDING_FUNDS
    REJECTED
```

## Member States

```
INVITED → ACTIVE → SUSPENDED → REACTIVATED → ACTIVE
                       │
                       └──→ REMOVED
```

## Transaction States

```
INITIATED → PROCESSING → COMPLETED → (REVERSED)
                │
                └──→ FAILED
```

---

# WORKFLOW DESIGN PRINCIPLES

```
1. ALWAYS authorize before acting
   Check role/permission at the start of every workflow

2. ALWAYS validate before mutating
   Check business rules before changing any state

3. ALWAYS atomic for money
   Debit and credit in the same transaction — never partial

4. ALWAYS publish an event after state change
   Supporting domains react via events, not direct calls

5. ALWAYS write to audit log
   Every state change is traceable

6. NEVER delete — only transition state
   Missed → MISSED, not deleted
   Bad transaction → REVERSED, not edited
```

---

*Document version 1.0 — FundLoop Platform*
*Sequence: Domain Model → Business Rules → Workflows → API Design → DB Schema*