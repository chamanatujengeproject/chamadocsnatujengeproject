# FUNDLOOP — EVENT FLOW DOCUMENT

> **Reading order:** Domain Model → Business Rules → Workflows → **Event Flow (this doc)**
>
> Event flow answers: **What happens system-wide when something occurs?**
> Workflows say HOW a process runs. Events say HOW domains talk to each other.

---

## WHAT IS AN EVENT?

```
An event is a fact.
Something happened. It cannot be undone.
The system broadcasts it. Others react.

NOT a command:   "PleaseReleasePayout"   ← telling something to do
YES an event:    "PayoutReleased"         ← something already done
```

---

## EVENT NAMING CONVENTION

```
{Entity}{PastTenseVerb}

Examples:
  MemberJoined
  ContributionPaid
  PayoutReleased
  ClaimApproved
  TransactionReversed
```

---

## EVENT ENVELOPE (STRUCTURE)

Every event in FundLoop carries this shape:

```json
{
  "eventId":     "uuid-v4",
  "eventType":   "ContributionPaid",
  "version":     "1.0",
  "occurredAt":  "2025-01-15T10:30:00Z",
  "chamaId":     "chama_abc123",
  "actorId":     "member_xyz789",
  "payload":     { ... event-specific data ... },
  "metadata": {
    "correlationId": "request-uuid",
    "source":        "rosca-service"
  }
}
```

---

# MASTER EVENT CATALOGUE

## MEMBERSHIP EVENTS

---

### EVT-MEM-001: `MemberJoined`

**Published by:** Membership Domain
**Trigger:** New member successfully joins a chama

```json
{
  "eventType": "MemberJoined",
  "payload": {
    "memberId":   "member_abc",
    "chamaId":    "chama_xyz",
    "role":       "MEMBER",
    "joinedAt":   "2025-01-15T10:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS member:  "Welcome to {chamaName}! You are now an active member."
  → SMS chairman: "New member {name} has joined."

Audit Domain
  → Log: MemberJoined, chamaId, memberId, timestamp

Reporting Domain
  → Update: MemberReport (active member count +1)
```

---

### EVT-MEM-002: `MemberSuspended`

**Published by:** Membership Domain
**Trigger:** Chairman/Admin suspends a member

```json
{
  "eventType": "MemberSuspended",
  "payload": {
    "memberId":    "member_abc",
    "chamaId":     "chama_xyz",
    "suspendedBy": "member_chairman",
    "reason":      "Repeated missed contributions",
    "suspendedAt": "2025-01-20T09:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
ROSCA Domain
  → Remove member from active rotation for current cycle
  → Flag RotationPosition as INELIGIBLE

Welfare Domain
  → Freeze any PENDING_APPROVAL claims for this member

Notification Domain
  → SMS member: "Your membership has been suspended. Contact your chairman."

Audit Domain
  → Log: MemberSuspended, by whom, reason
```

---

### EVT-MEM-003: `MemberReactivated`

**Published by:** Membership Domain
**Trigger:** Suspended member reinstated

```json
{
  "eventType": "MemberReactivated",
  "payload": {
    "memberId":      "member_abc",
    "chamaId":       "chama_xyz",
    "reactivatedBy": "member_chairman",
    "reactivatedAt": "2025-02-01T08:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
ROSCA Domain
  → Restore member to rotation queue (end of queue if mid-cycle)

Notification Domain
  → SMS member: "Your membership has been reinstated."

Audit Domain
  → Log: MemberReactivated
```

---

### EVT-MEM-004: `InvitationSent`

**Published by:** Membership Domain
**Trigger:** Existing member invites someone to chama

```json
{
  "eventType": "InvitationSent",
  "payload": {
    "inviterId":  "member_abc",
    "chamaId":    "chama_xyz",
    "phoneNumber": "+254700000000",
    "expiresAt":  "2025-01-22T10:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS invitee: "You've been invited to join {chamaName}. Click: {link}"

Audit Domain
  → Log: InvitationSent
```

---

# ROSCA EVENTS

---

### EVT-ROS-001: `CycleStarted`

**Published by:** ROSCA Domain
**Trigger:** Treasurer starts a new ROSCA cycle

```json
{
  "eventType": "CycleStarted",
  "payload": {
    "cycleId":              "cycle_001",
    "chamaId":              "chama_xyz",
    "cycleNumber":          3,
    "contributionAmount":   1000,
    "currency":             "KES",
    "contributionDeadline": "2025-02-01T23:59:00Z",
    "payoutRecipientId":    "member_abc",
    "startedBy":            "member_treasurer"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS all members:
    "Cycle #3 started. Contribute KES 1,000 by 1st Feb.
     This cycle's payout goes to: {recipientName}."

Audit Domain
  → Log: CycleStarted, cycleId, treasurer

Reporting Domain
  → Create new RoscaCycleReport entry
```

---

### EVT-ROS-002: `ContributionPaid`

**Published by:** Finance Domain (after successful wallet transfer)
**Trigger:** Member pays their cycle contribution

```json
{
  "eventType": "ContributionPaid",
  "payload": {
    "contributionId": "contrib_001",
    "cycleId":        "cycle_001",
    "chamaId":        "chama_xyz",
    "memberId":       "member_abc",
    "amount":         1000,
    "currency":       "KES",
    "paidAt":         "2025-01-28T14:22:00Z",
    "transactionId":  "txn_aabbcc"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS member: "✓ KES 1,000 contribution received for Cycle #3."

ROSCA Domain
  → Mark ContributionRecord: status = PAID
  → Check: have ALL active members paid?
       YES → publish: AllContributionsReceived (internal trigger)
       NO  → wait

Reporting Domain
  → Update ContributionSummary

Audit Domain
  → Log: ContributionPaid
```

---

### EVT-ROS-003: `ContributionMissed`

**Published by:** ROSCA Domain (scheduler fires at deadline)
**Trigger:** Contribution deadline passes with no payment

```json
{
  "eventType": "ContributionMissed",
  "payload": {
    "cycleId":   "cycle_001",
    "chamaId":   "chama_xyz",
    "memberId":  "member_abc",
    "deadline":  "2025-02-01T23:59:00Z",
    "penalty":   "FINE",
    "fineAmount": 200
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS member: "You missed your contribution for Cycle #3.
                 A fine of KES 200 has been applied."
  → SMS chairman: "Member {name} missed contribution. Penalty applied."

Finance Domain
  → If penalty = FINE: create PenaltyTransaction
  → Debit member wallet: fine amount → chama penalty pool

Audit Domain
  → Log: ContributionMissed, penalty type, amount
```

---

### EVT-ROS-004: `PayoutReleased`

**Published by:** Finance Domain (after successful payout transfer)
**Trigger:** All contributions collected → payout sent to recipient

```json
{
  "eventType": "PayoutReleased",
  "payload": {
    "payoutId":      "payout_001",
    "cycleId":       "cycle_001",
    "chamaId":       "chama_xyz",
    "recipientId":   "member_abc",
    "amount":        10000,
    "currency":      "KES",
    "releasedAt":    "2025-02-02T09:15:00Z",
    "transactionId": "txn_xxyyzz"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS recipient: "🎉 KES 10,000 payout sent to your wallet!"
  → SMS all members:
    "Cycle #3 complete. {recipientName} received payout.
     Next cycle recipient: {nextName}."

ROSCA Domain
  → Mark RoscaCycle: status = COMPLETED
  → Advance RotationPosition pointer

Reporting Domain
  → Update RoscaCycleReport: status = COMPLETED

Audit Domain
  → Log: PayoutReleased, recipient, amount
```

---

### EVT-ROS-005: `RotationSwapRequested`

**Published by:** ROSCA Domain
**Trigger:** Member requests to swap payout position with another

```json
{
  "eventType": "RotationSwapRequested",
  "payload": {
    "requesterId":   "member_abc",
    "targetId":      "member_def",
    "chamaId":       "chama_xyz",
    "cycleId":       "cycle_001"
  }
}
```

**Subscribers & Reactions:**

```
Governance Domain
  → Create Vote: requires 2/3 majority approval

Notification Domain
  → SMS all members: "{name} requests rotation swap with {name2}. Vote now."

Audit Domain
  → Log: RotationSwapRequested
```

---

# WELFARE EVENTS

---

### EVT-WEL-001: `ClaimSubmitted`

**Published by:** Welfare Domain
**Trigger:** Member submits an emergency welfare claim

```json
{
  "eventType": "ClaimSubmitted",
  "payload": {
    "claimId":       "claim_001",
    "chamaId":       "chama_xyz",
    "claimantId":    "member_abc",
    "emergencyType": "medical",
    "amount":        5000,
    "currency":      "KES",
    "evidenceUrls":  ["https://storage/bill.pdf"],
    "submittedAt":   "2025-02-10T11:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
Governance Domain
  → Create Vote record
    requiredThreshold: 70%
    voters: all active chama members

Notification Domain
  → SMS all members:
    "Welfare claim submitted by {name}. Type: Medical. Amount: KES 5,000.
     Please vote to approve or reject."

Audit Domain
  → Log: ClaimSubmitted
```

---

### EVT-WEL-002: `ClaimApproved`

**Published by:** Governance Domain (vote threshold reached)
**Trigger:** 70%+ members approve the welfare claim

```json
{
  "eventType": "ClaimApproved",
  "payload": {
    "claimId":      "claim_001",
    "chamaId":      "chama_xyz",
    "claimantId":   "member_abc",
    "amount":       5000,
    "approvalRate": 0.80,
    "approvedAt":   "2025-02-11T16:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
Welfare Domain
  → Claim.status → APPROVED
  → Trigger: WF-WEL-003 (Disbursement)

Notification Domain
  → SMS claimant: "Your welfare claim has been approved. Funds incoming."

Audit Domain
  → Log: ClaimApproved, approval rate, voters
```

---

### EVT-WEL-003: `ClaimRejected`

**Published by:** Governance Domain
**Trigger:** Rejection threshold crossed or chairman veto

```json
{
  "eventType": "ClaimRejected",
  "payload": {
    "claimId":      "claim_001",
    "chamaId":      "chama_xyz",
    "claimantId":   "member_abc",
    "rejectionRate": 0.65,
    "rejectedAt":   "2025-02-11T16:00:00Z",
    "reason":       "Insufficient evidence"
  }
}
```

**Subscribers & Reactions:**

```
Welfare Domain
  → Claim.status → REJECTED
  → Member can re-submit with more evidence

Notification Domain
  → SMS claimant: "Your welfare claim was not approved. Reason: {reason}."

Audit Domain
  → Log: ClaimRejected, reason
```

---

### EVT-WEL-004: `WelfareDisbursed`

**Published by:** Finance Domain (after successful transfer)
**Trigger:** Welfare funds sent to claimant's wallet

```json
{
  "eventType": "WelfareDisbursed",
  "payload": {
    "disbursementId": "disb_001",
    "claimId":        "claim_001",
    "chamaId":        "chama_xyz",
    "recipientId":    "member_abc",
    "amount":         5000,
    "currency":       "KES",
    "disbursedAt":    "2025-02-11T16:30:00Z",
    "transactionId":  "txn_welfare_001"
  }
}
```

**Subscribers & Reactions:**

```
Welfare Domain
  → Claim.status → DISBURSED
  → WelfarePool.balance -= 5000

Notification Domain
  → SMS member: "KES 5,000 welfare support sent to your wallet. Get well soon."

Reporting Domain
  → Update WelfareReport

Audit Domain
  → Log: WelfareDisbursed
```

---

# FINANCE EVENTS

---

### EVT-FIN-001: `WalletTopUp`

**Published by:** Finance Domain
**Trigger:** Member successfully deposits into wallet

```json
{
  "eventType": "WalletTopUp",
  "payload": {
    "walletId":      "wallet_abc",
    "memberId":      "member_abc",
    "amount":        2000,
    "currency":      "KES",
    "paymentMethod": "MPESA",
    "transactionId": "txn_topup_001",
    "topUpAt":       "2025-01-27T08:45:00Z"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS member: "KES 2,000 deposited to your FundLoop wallet."

Audit Domain
  → Log: WalletTopUp
```

---

### EVT-FIN-002: `PaymentFailed`

**Published by:** Finance Domain
**Trigger:** Payment attempt (contribution or top-up) fails

```json
{
  "eventType": "PaymentFailed",
  "payload": {
    "memberId":      "member_abc",
    "chamaId":       "chama_xyz",
    "amount":        1000,
    "reason":        "Insufficient wallet balance",
    "failedAt":      "2025-01-28T10:00:00Z",
    "attemptId":     "attempt_001"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS member: "Payment of KES 1,000 failed. Reason: {reason}.
                 Top up your wallet and try again."

Audit Domain
  → Log: PaymentFailed, reason, attempt
```

---

### EVT-FIN-003: `TransactionReversed`

**Published by:** Finance Domain
**Trigger:** Treasurer/Admin reverses an erroneous transaction

```json
{
  "eventType": "TransactionReversed",
  "payload": {
    "reversalId":          "rev_001",
    "originalTxnId":       "txn_aabbcc",
    "chamaId":             "chama_xyz",
    "amount":              1000,
    "reversedBy":          "member_treasurer",
    "reason":              "Duplicate payment error",
    "reversedAt":          "2025-01-29T11:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
Notification Domain
  → SMS affected member: "A transaction of KES 1,000 has been reversed.
                          Reason: {reason}."

Audit Domain
  → Log: TransactionReversed, original txn, actor, reason
  (HIGH PRIORITY — finance audit trail)
```

---

# GOVERNANCE EVENTS

---

### EVT-GOV-001: `VoteCast`

**Published by:** Governance Domain
**Trigger:** Any member casts a vote (welfare claim, rule change, swap)

```json
{
  "eventType": "VoteCast",
  "payload": {
    "voteId":     "vote_001",
    "voterId":    "member_abc",
    "chamaId":    "chama_xyz",
    "subjectId":  "claim_001",
    "subjectType":"WELFARE_CLAIM",
    "decision":   "APPROVE",
    "castAt":     "2025-02-11T12:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
Governance Domain
  → Tally vote
  → Check if threshold reached → publish outcome event

Audit Domain
  → Log: VoteCast, voter, decision, subject
```

---

### EVT-GOV-002: `RuleChanged`

**Published by:** Governance Domain
**Trigger:** Rule change vote passes majority

```json
{
  "eventType": "RuleChanged",
  "payload": {
    "ruleId":       "rule_contribution_amount",
    "chamaId":      "chama_xyz",
    "previousValue": 1000,
    "newValue":      1500,
    "changedBy":    "VOTE",
    "effectiveFrom": "2025-03-01T00:00:00Z"
  }
}
```

**Subscribers & Reactions:**

```
All Core Domains
  → Reload rule configuration for this chama

Notification Domain
  → SMS all members: "Chama rule updated: Contribution amount is now
                      KES 1,500 starting March 2025."

Audit Domain
  → Log: RuleChanged, who voted, previous vs new value
```

---

# FULL EVENT → SUBSCRIBER MATRIX

```
                              NOTIFICATION  ROSCA   WELFARE  FINANCE  GOVERNANCE  AUDIT  REPORTING
EVENT                         ────────────  ─────   ───────  ───────  ──────────  ─────  ─────────
MemberJoined          (MEM)       ✓                                               ✓         ✓
MemberSuspended       (MEM)       ✓           ✓       ✓                           ✓
MemberReactivated     (MEM)       ✓           ✓                                   ✓
InvitationSent        (MEM)       ✓                                               ✓
CycleStarted          (ROS)       ✓                                               ✓         ✓
ContributionPaid      (ROS)       ✓           ✓                                   ✓         ✓
ContributionMissed    (ROS)       ✓                           ✓                   ✓
PayoutReleased        (ROS)       ✓           ✓                                   ✓         ✓
RotationSwapRequested (ROS)       ✓                                    ✓          ✓
ClaimSubmitted        (WEL)       ✓                                    ✓          ✓
ClaimApproved         (WEL)       ✓                   ✓                           ✓
ClaimRejected         (WEL)       ✓                   ✓                           ✓
WelfareDisbursed      (WEL)       ✓                   ✓                           ✓         ✓
WalletTopUp           (FIN)       ✓                                               ✓
PaymentFailed         (FIN)       ✓                                               ✓
TransactionReversed   (FIN)       ✓                                               ✓
VoteCast              (GOV)                                            ✓          ✓
RuleChanged           (GOV)       ✓          ALL     ALL      ALL     ALL         ✓
```

---

# EVENT BUS ARCHITECTURE

```
                    ┌─────────────────────────────┐
                    │       CORE DOMAINS           │
                    │  Membership | ROSCA | Welfare│
                    │  Finance   | Governance      │
                    └──────────────┬──────────────┘
                                   │  publish events
                                   ▼
                    ┌─────────────────────────────┐
                    │         EVENT BUS            │
                    │   (Kafka / RabbitMQ / SQS)   │
                    │                             │
                    │  Topics:                    │
                    │    fundloop.membership      │
                    │    fundloop.rosca           │
                    │    fundloop.welfare         │
                    │    fundloop.finance         │
                    │    fundloop.governance      │
                    └──────────────┬──────────────┘
                                   │  subscribe
                    ┌──────────────▼──────────────┐
                    │     SUPPORTING DOMAINS       │
                    │  Notification | Audit        │
                    │  Reporting   | Integration   │
                    └─────────────────────────────┘
```

---

# EVENT TOPICS (KAFKA / QUEUE)

```
Topic                        Events inside
────────────────────────────────────────────────────────────
fundloop.membership          MemberJoined, MemberSuspended,
                             MemberReactivated, InvitationSent

fundloop.rosca               CycleStarted, ContributionPaid,
                             ContributionMissed, PayoutReleased,
                             RotationSwapRequested

fundloop.welfare             ClaimSubmitted, ClaimApproved,
                             ClaimRejected, WelfareDisbursed

fundloop.finance             WalletTopUp, PaymentFailed,
                             TransactionReversed

fundloop.governance          VoteCast, RuleChanged
```

---

# EVENT FLOW: END-TO-END EXAMPLE

## Scenario: Full ROSCA Cycle (10 members, KES 1,000 each)

```
[Day 1]
Treasurer starts cycle
  → EVT-ROS-001: CycleStarted
    → Notification: SMS all 10 members

[Days 1–7]
Members pay contributions (one by one)
  → EVT-ROS-002: ContributionPaid (×10)
    → Notification: SMS each member confirmation
    → ROSCA Domain: tracks count

[Member 4 misses deadline]
  → EVT-ROS-003: ContributionMissed
    → Notification: SMS member 4 + chairman
    → Finance: debit fine from member 4 wallet

[All 9 remaining paid → threshold met]
  → ROSCA Domain: trigger payout check
  → Finance Domain: atomic transfer chama→recipient wallet
  → EVT-ROS-004: PayoutReleased
    → Notification: SMS recipient (🎉) + all members
    → ROSCA Domain: cycle = COMPLETED, advance rotation
    → Reporting: update cycle report
    → Audit: log payout
```

---

## Scenario: Welfare Claim Lifecycle

```
[Member submits claim]
  → EVT-WEL-001: ClaimSubmitted
    → Governance: create vote
    → Notification: SMS all members to vote

[8 of 10 members vote APPROVE]
  → EVT-GOV-001: VoteCast (×8)
    → Governance tallies: 80% ≥ 70% threshold

[Threshold reached]
  → EVT-WEL-002: ClaimApproved
    → Welfare: status = APPROVED
    → Trigger disbursement flow

[Finance completes transfer]
  → EVT-WEL-004: WelfareDisbursed
    → Notification: SMS claimant
    → Welfare: pool balance updated
    → Audit: log disbursement
```

---

# EVENT DESIGN PRINCIPLES

```
1. EVENTS ARE FACTS — past tense, immutable
   "PayoutReleased" not "ReleasePayout"

2. EVENTS ARE DUMB — they carry data, not logic
   Subscribers decide what to do with them

3. PUBLISHERS DON'T KNOW SUBSCRIBERS
   Finance publishes PayoutReleased
   It does NOT call Notification directly

4. SUBSCRIBERS ARE IDEMPOTENT
   Processing the same event twice = same result
   Guards against duplicate delivery

5. EVENTS ARE ORDERED PER ENTITY
   All events for cycle_001 stay in order
   Kafka: partition by chamaId

6. FAILED EVENT PROCESSING = RETRY, NOT DROP
   Dead-letter queue for poison events
   Alert ops team on repeated failure

7. AUDIT SUBSCRIBES TO EVERYTHING
   No event is too small for the audit log
   Full traceability is non-negotiable
```

---

# RETRY & DEAD-LETTER STRATEGY

```
Event published
      │
      ▼
Subscriber processes
      │
  SUCCESS → done
      │
  FAILURE
      │
      ▼
  Retry (exponential backoff)
  Attempt 1: 5s
  Attempt 2: 30s
  Attempt 3: 2min
  Attempt 4: 10min
      │
  Still failing?
      │
      ▼
  Dead-Letter Queue (DLQ)
  Alert: ops dashboard
  Manual investigation required
```

---

*Document version 1.0 — FundLoop Platform*
*Sequence: Domain Model → Business Rules → Workflows → Event Flow → API Design → DB Schema*