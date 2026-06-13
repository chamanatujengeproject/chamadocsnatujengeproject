# 1. Authentication & Identity Module

**Purpose:** Who the user is and what they can access.

### Models

* User
* Role
* Permission
* UserSession
* VerificationToken
* RefreshToken
* MultiChamaMembership

### Responsibilities

* Registration
* Login
* JWT authentication
* Role-based access control (RBAC)
* Multi-chama membership
* Session management
* Email/phone verification

---

# 2. Chama Management Module

**Purpose:** Core Chama setup and configuration.

### Models

* Chama
* ChamaType
* Constitution
* ConstitutionVersion
* ChamaSettings
* WalletAllocationRule

### Responsibilities

* Create chama
* Configure chama type
* Define constitution
* Edit chama
* Soft delete chama
* Configure governance rules
* Hybrid wallet configuration

---

# 3. Member Management Module

**Purpose:** Manage people inside a chama.

### Models

* Member
* MemberInvitation
* MemberProfile
* MemberRole
* MemberStatus
* ExitRequest

### Responsibilities

* Add member
* Edit member
* Deactivate member
* Invite members
* Bulk CSV upload
* Member history
* Member exit workflow

---

# 4. Contribution Management Module

**Purpose:** Money coming into the chama.

### Models

* ContributionCycle
* Contribution
* ContributionAllocation
* ContributionBalance
* ContributionHistory

### Responsibilities

* Contribution tracking
* Partial payments
* Carry-forward balances
* Contribution splitting
* Cycle management
* Late contribution handling

---

# 5. ROSCA (Merry-Go-Round) Module

**Purpose:** Rotational payout logic.

### Models

* PayoutSchedule
* RotationPosition
* PositionSwap
* PayoutRecord
* ShortfallRecord

### Responsibilities

* Rotation scheduling
* Lottery/fixed order
* Payout release
* Position swapping
* Shortfall recovery
* Rotation notifications

---

# 6. Loan Management Module

**Purpose:** Lending system.

### Models

* Loan
* LoanApplication
* LoanApproval
* LoanRepayment
* RepaymentSchedule
* LoanPenalty
* LoanStatus

### Responsibilities

* Loan application
* Approval workflow
* Interest calculation
* Repayment tracking
* Loan defaults
* Penalty management

---

# 7. Guarantor Module

**Purpose:** Loan security.

### Models

* Guarantor
* GuarantorAcceptance
* GuarantorLiability
* RecoveryRequest

### Responsibilities

* Assign guarantors
* Digital acceptance
* Liability tracking
* Recovery management

> Keep this separate from Loan module because guarantor logic becomes complex and reusable.

---

# 8. Welfare Management Module

**Purpose:** Emergency support fund.

### Models

* WelfareFund
* WelfareApplication
* WelfareApproval
* WelfareDisbursement
* WelfareCategory

### Responsibilities

* Welfare applications
* Qualification checks
* Approval flow
* Grant/loan welfare support
* Welfare history

---

# 9. Investment Management Module

**Purpose:** Group investments.

### Models

* InvestmentAsset
* Portfolio
* AssetValuation
* OwnershipRecord
* ProfitDistribution

### Responsibilities

* Asset tracking
* Portfolio valuation
* Ownership calculation
* Profit distribution
* Exit valuation

---

# 10. Share Management Module

**Purpose:** Share-based ownership.

### Models

* Share
* ShareRegister
* ShareTransfer
* CapitalCall
* DividendDistribution
* ShareRedemption

### Responsibilities

* Share issuance
* Transfers
* Dividends
* Buybacks
* Capital calls

---

# 11. Hybrid Wallet Module

**Purpose:** Multi-wallet money separation.

### Models

* Wallet
* WalletLedger
* WalletTransaction
* WalletTransfer
* WalletBalance

### Responsibilities

* Wallet creation
* Contribution splitting
* Wallet independence
* Cross-wallet transfer approval
* Aggregated balances

---

# 12. Payment Module

**Purpose:** External money movement.

### Models

* Payment
* PaymentMethod
* Transaction
* PaymentAttempt
* PaymentReceipt

### Responsibilities

* M-Pesa integration
* Visa payments
* Mastercard payments
* Partial payments
* Payment validation
* Real-time confirmation

---

# 13. Fines & Discipline Module

**Purpose:** Rule enforcement.

### Models

* FineRule
* Fine
* FineWaiver
* FineLedger
* ViolationRecord

### Responsibilities

* Auto fines
* Manual fines
* Fine waiver
* Grace periods
* Fine tracking

---

# 14. Governance & Voting Module

**Purpose:** Decision making.

### Models

* Motion
* Vote
* VotingRule
* Quorum
* MotionOutcome

### Responsibilities

* Voting
* Member approval
* Loan approval voting
* Constitutional amendments
* Governance workflows

---

# 15. Meeting Management Module

**Purpose:** Meeting operations.

### Models

* Meeting
* Agenda
* Attendance
* Minutes
* MeetingNotification

### Responsibilities

* Schedule meetings
* Attendance tracking
* Minutes management
* Fine linkage for absentees

---

# 16. Notification Module

**Purpose:** Communication system.

### Models

* Notification
* NotificationPreference
* NotificationLog
* SMSQueue
* EmailQueue
* PushNotification

### Responsibilities

* SMS
* Email
* Push notifications
* Reminder scheduling
* Notification preferences

---

# 17. Reporting & Analytics Module

**Purpose:** Insights and downloadable reports.

### Models

* Report
* AnalyticsSnapshot
* DashboardMetric
* ExportHistory

### Responsibilities

* Financial reports
* Loan reports
* Contribution reports
* Portfolio analytics
* PDF/Excel exports

---

# 18. Audit & Compliance Module

**Purpose:** Immutable history.

### Models

* AuditLog
* EventLog
* ActivityRecord
* ComplianceRecord

### Responsibilities

* Financial audit trail
* Governance tracking
* User actions
* Compliance reporting

---

# 19. Customer Support Module

**Purpose:** Help system.

### Models

* SupportTicket
* ChatSession
* FAQ
* KnowledgeBaseArticle

### Responsibilities

* In-app support
* Ticket management
* FAQ
* Knowledge base

---

# 20. Admin / Platform Management Module

**Purpose:** Operate FundLoop platform.

### Models

* SystemAdmin
* SystemSetting
* PlatformHealth
* Subscription
* BillingPlan

### Responsibilities

* Platform monitoring
* Chama onboarding
* Subscription billing
* Platform settings
* Escalations

---

## Recommended Architecture (Very Important)

You should **not** start with 20 microservices.

Start with a **modular monolith**:

```txt
fundloop/

├── auth/
├── chama/
├── members/
├── contributions/
├── rosca/
├── loans/
├── guarantors/
├── welfare/
├── investments/
├── shares/
├── wallets/
├── payments/
├── fines/
├── governance/
├── meetings/
├── notifications/
├── reporting/
├── audit/
├── support/
└── admin/
```
