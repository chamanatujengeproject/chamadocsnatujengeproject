.

---

# FUNDLOOP DOMAIN ARCHITECTURE (UPDATED)

```text id="fundloop_root"
CORE DOMAINS (Business Logic / Write Side)
------------------------------------------
1. Membership Domain
2. ROSCA Domain
3. Welfare Domain
4. Finance Domain
5. Governance Domain

SUPPORTING DOMAINS (Event-driven / Read / Infra)
------------------------------------------------
6. Reporting Domain
7. Notification Domain
8. Audit Domain
9. Identity & Access Domain
10. Event/Integration Domain
```

---

# 1. MEMBERSHIP DOMAIN

Handles people, chamas, roles, and participation.

## Entities

```text id="mem_entities"
Member
Chama
Membership
Role
Invitation
```

### Responsibility

* User identity inside the system
* Joining and leaving chamas
* Assigning roles (Treasurer, Chairman, etc.)

---

# 2. ROSCA DOMAIN

Handles rotating savings logic.

## Entities

```text id="rosca_entities"
RoscaCycle
RotationPosition
Contribution
Payout
Penalty
```

### Responsibility

* Rotational payouts
* Contribution collection per cycle
* Cycle progression
* Rotation order logic

---

# 3. WELFARE DOMAIN

Handles emergency support system.

## Entities

```text id="welfare_entities"
WelfarePool
Claim
Approval
Disbursement
```

### Responsibility

* Emergency claims
* Voting/approval system
* Welfare fund management
* Disbursement of aid

---

# 4. FINANCE DOMAIN

Handles all money movement and ledger correctness.

## Entities

```text id="finance_entities"
Wallet
Transaction
Payment
LedgerEntry
ContributionRecord
```

### Responsibility

* Money movement (source of truth)
* Wallet balances
* Transaction integrity
* Idempotency
* Ledger/audit correctness

---

# 5. GOVERNANCE DOMAIN

Handles rules, voting, and system control.

## Entities

```text id="gov_entities"
Rule
Vote
Meeting
Policy
```

### Responsibility

* System rules enforcement
* Voting mechanisms
* Approval thresholds
* Policy updates

---

# 6. REPORTING DOMAIN (SUPPORTING)

READ-ONLY domain (no state changes)

## Entities

```text id="report_entities"
MemberReport
RoscaCycleReport
ContributionSummary
WelfareReport
FinancialReport
AuditReport
```

### Responsibility

* Analytics
* Dashboards
* Historical summaries
* Trends
* Insights

### Key Idea

```text id="report_idea"
Reads data from all domains → produces insights
```

---

# 7. NOTIFICATION DOMAIN (SUPPORTING)

Event-driven messaging system.

## Entities

```text id="notif_entities"
Notification
NotificationTemplate
NotificationPreference
DeliveryAttempt
NotificationQueue
```

### Responsibility

* Sending SMS / Email / WhatsApp / Push
* Event-based alerts
* Retry logic
* Delivery tracking

### Example Events it listens to:

```text id="notif_events"
ContributionPaid
PayoutReleased
ClaimApproved
CycleStarted
PaymentFailed
MemberJoined
```

---

# 8. AUDIT DOMAIN (SUPPORTING)

Tracks everything for transparency.

## Entities

```text id="audit_entities"
AuditLog
ActionTrace
SystemEvent
ChangeRecord
```

### Responsibility

* Who did what and when
* Immutable logs
* Fraud detection
* Compliance

---

# 9. IDENTITY & ACCESS DOMAIN (SUPPORTING)

Security and authentication.

## Entities

```text id="auth_entities"
UserAccount
Session
Permission
RoleBinding
AuthToken
```

### Responsibility

* Login/logout
* JWT/session management
* Role-based access control
* Permissions

---

# 10. EVENT / INTEGRATION DOMAIN (SUPPORTING)

This is the “nervous system” of the platform.

## Entities

```text id="event_entities"
DomainEvent
EventBus
EventHandler
IntegrationWebhook
```

### Responsibility

* Communication between domains
* Event publishing (Kafka/RabbitMQ)
* Decoupling services

---

# HOW EVERYTHING CONNECTS

```text id="flow_arch"
                CORE DOMAINS
------------------------------------------------
Membership → ROSCA → Finance → Welfare → Governance
      ↓            ↓           ↓          ↓
      └────────────┴───────────┴──────────┘
                    EVENTS
                      ↓
------------------------------------------------
SUPPORTING DOMAINS
Reporting   Notification   Audit   Integration
```

---

# KEY DESIGN INSIGHT

## CORE DOMAINS (WRITE SYSTEM)

These change state:

```text id="core_write"
Membership
ROSCA
Welfare
Finance
Governance
```

---

## SUPPORTING DOMAINS (READ / INFRA)

These do NOT own business truth:

```text id="supporting_read"
Reporting → analyzes data
Notification → sends messages
Audit → records history
Identity → secures system
Event → connects everything
```

---

# 
