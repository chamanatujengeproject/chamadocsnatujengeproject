# Authentication Module

## User

* id
* name
* phone
* email
* password_hash
* national_id_enc
* status
* created_at

---

# Chama Management Module

## Chama

* id
* name
* type
* status
* constitution
* created_by
* created_at

## ChamaMember

* id
* chama_id
* user_id
* role
* status
* joined_at

---

# Contribution Module

## Contribution

* id
* chama_id
* member_id
* cycle_id
* amount_expected
* amount_paid
* status
* paid_at

## Cycle

* id
* chama_id
* cycle_name
* cycle_number
* contribution_deadline
* payout_date
* status
* created_at

---

# Financial Transactions Module

## Transaction

* id
* chama_id
* member_id
* type
* amount
* channel
* reference
* status
* created_at

---

# Loan Management Module

## Loan

* id
* chama_id
* member_id
* principal
* interest_rate
* interest_model
* duration_months
* status
* disbursed_at
* created_at

## LoanRepayment

* id
* loan_id
* member_id
* amount_paid
* payment_date
* status
* transaction_id
* created_at

---

# Wallet & Treasury Module

## Wallet

* id
* chama_id
* wallet_type
* balance
* updated_at

## WalletTransaction

* id
* wallet_id
* transaction_id
* amount
* transaction_type
* created_at

---

# Governance & Roles Module

## RolePermission

* id
* role
* permission
* created_at

## Invitation

* id
* chama_id
* invited_by
* invited_user_id
* invite_status
* expires_at
* created_at

---

# Penalty & Fine Module

## Fine

* id
* chama_id
* member_id
* amount
* reason
* status
* issued_at
* paid_at

---

# Welfare Module

## WelfareRequest

* id
* chama_id
* member_id
* title
* description
* requested_amount
* approved_amount
* status
* created_at

## WelfareContribution

* id
* welfare_request_id
* member_id
* amount
* created_at

---

# ROSCA

## PayoutQueue

* id
* chama_id
* cycle_id
* member_id
* payout_order
* payout_status
* paid_at

---

# Audit 

## AuditLog

* id
* chama_id
* actor_id
* event_type
* entity_type
* entity_id
* before_state
* after_state
* row_hash
* created_at

---

# Notification Module

## Notification

* id
* user_id
* title
* message
* type
* read_status
* created_at

---

# Payment Integration Module

## Payment

* id
* chama_id
* member_id
* transaction_id
* provider
* reference
* amount
* status
* paid_at
* created_at
