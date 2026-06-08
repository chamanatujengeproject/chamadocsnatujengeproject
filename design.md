# FundLoop Chama System – Design Team Responsibilities



#Eric Wambua  – Business Process & Governance Designer

## Primary Responsibility

Design how the Chama business operations work inside the system.

You are responsible for converting the BRD into:

* User workflows
* Decision processes
* Permissions
* Governance rules
* System behavior

You answer this question:

**“How should the Chama operate inside the platform?”**

---

## Your Tasks

### 1. Define User Roles and Permissions

Clearly define what each user can and cannot do.

Roles:

* Chairperson
* Treasurer
* Member
* System Administrator

For each role define:

#### Actions allowed

Example:

**Treasurer**

* View payments
* Approve contributions
* Manage loans
* Generate reports

#### Actions restricted

Example:

**Member**

* Cannot delete transactions
* Cannot approve loans

---

### 2. Create User Flows

Design step-by-step journeys for:

#### Authentication

* Register
* Login
* Password reset

#### Chama Management

* Create Chama
* Join Chama
* Leave Chama

#### Contribution Process

From payment initiation to confirmation.

#### Loan Process

From loan request to repayment.

#### Meeting Process

Meeting scheduling → reminders → attendance → minutes.

#### Reporting Process

How reports are generated and viewed.

Expected output:

Flow diagrams for every major process.

---

### 3. Design Governance & Decision Rules

Define approval and decision-making logic.

Examples:

#### Loan Approval Rules

Who approves loans?

Example:

* Small loan → Treasurer approval
* Medium loan → Treasurer + Chairperson approval
* Large loan → Group voting

#### Penalty Rules

* Late contribution penalties
* Missed payment penalties

#### Voting Rules

* Minimum number of voters
* Approval percentage

---

### 4. Identify Edge Cases

Document unusual situations.

Examples:

* Member leaves Chama with unpaid loan
* Failed payment
* Duplicate payment
* Treasurer removed
* Chairperson inactive
* Loan default

---

## Deliverables

You must submit:

1. User Roles & Permissions Document
2. User Flow Diagrams
3. Governance Rules Document
4. Decision Processes
5. Edge Case Documentation

---

## Success Criteria

Your work should answer:

**“How does the business function inside the system?”**

No one should be confused about how a Chama process works.

---

#  Alex    – Database & Financial Logic Designer

## Primary Responsibility

Design the data structure and financial logic of the system.

You answer this question:

**“What data must exist and how is financial integrity maintained?”**

---

## Your Tasks

### 1. Design Database Structure

Create the database model.

Required entities:

* Users
* Chamas
* Members
* Contributions
* Transactions
* Loans
* Loan Repayments
* Meetings
* Notifications
* Reports
* Audit Logs
* Votes
* Penalties

Define:

* Tables
* Fields
* Relationships
* Constraints

---

### 2. Create ERD

Create a complete Entity Relationship Diagram.

Define:

* One-to-one relationships
* One-to-many relationships
* Many-to-many relationships

Example:

Chama → many members

Member → many contributions

Loan → many repayments

---

### 3. Define Financial Rules

Design logic for:

#### Contributions

* Full payment
* Partial payment
* Remaining balance
* Overpayment prevention

#### Loans

* Interest calculation
* Repayment schedules
* Loan duration
* Default tracking

#### Transactions

* Immutable transaction history
* Payment status tracking
* Transaction reconciliation

---

### 4. Design Data States

Create system statuses.

Examples:

#### Loan Status

* Requested
* Under Review
* Approved
* Rejected
* Disbursed
* Repaying
* Completed
* Defaulted

#### Payment Status

* Pending
* Successful
* Failed
* Reversed
* Partial

---

### 5. Data Integrity Rules

Ensure:

* No duplicate transactions
* No negative balances
* No deleted financial records
* Proper audit logging

---

## Deliverables

You must submit:

1. Database Schema
2. ERD Diagram
3. Financial Logic Rules
4. State Diagrams
5. Data Constraints Document

---

## Success Criteria

Your work should answer:

**“How is all system data organized and protected?”**

No developer should be confused about how data is stored.

---

Victor codes

 – System Architecture & Technical Design Designer

## Primary Responsibility

Design the technical architecture of the platform.

You answer this question:

**“How will the system technically work?”**

---

## Your Tasks

### 1. Design System Architecture

Design:

* Frontend architecture
* Backend architecture
* Database integration
* External services

Required integrations:

* M-Pesa
* Visa
* Mastercard
* Notifications
* Customer Support Chat

Create architecture diagrams.

---

### 2. Define Modules

Break system into modules.

Required modules:

* Authentication
* Chama Management
* Member Management
* Contributions
* Loans
* Payments
* Notifications
* Reports
* Meetings
* Customer Support

Define responsibilities for each module.

---

### 3. Design APIs

Define APIs for all major functionality.

Example:

Authentication

POST /login

POST /register

Loans

POST /loan/request

POST /loan/approve

Payments

POST /payment/process

GET /transactions

Document:

* Inputs
* Outputs
* Validation rules
* Error handling

---

### 4. Security Design

Define:

* JWT authentication
* Password encryption
* Role authorization
* Audit logging
* Payment verification
* Fraud prevention

---

### 5. Infrastructure & Deployment

Propose:

* Hosting strategy
* Backup system
* Monitoring
* Logging
* Scalability approach

---

## Deliverables

You must submit:

1. Architecture Diagram
2. Module Design Document
3. API Documentation
4. Security Design
5. Deployment Strategy

---
