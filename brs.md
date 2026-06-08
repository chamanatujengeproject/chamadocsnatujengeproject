```md
# FundLoop - Business Requirements Document (BRD)

## Project Name
**Chama Management System (CMS) – FundLoop**

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Problem Statement](#2-problem-statement)
3. [Proposed Solution](#3-proposed-solution)
4. [Business Objectives](#4-business-objectives)
5. [Stakeholders](#5-stakeholders)
6. [Business Requirements](#6-business-requirements)
7. [User Personas and Use Cases](#7-user-personas-and-use-cases)
8. [Functional Requirements](#8-functional-requirements)
9. [Non-Functional Requirements](#9-non-functional-requirements)
10. [Assumptions](#10-assumptions)
11. [Constraints](#11-constraints)

---




# 1. Introduction

Chamas play a critical role in savings, investment, and financial empowerment, especially in community-based financial systems. However, many Chama operations today rely heavily on manual record-keeping, fragmented communication channels, and inefficient financial tracking methods.

These manual processes often lead to inaccuracies, lack of transparency, delayed reporting, and administrative inefficiencies.

This document outlines the business requirements for a digital **Chama Management System (CMS)** designed to streamline operations, automate financial processes, improve accountability, and enhance member engagement.

---

# 2. Problem Statement

Current Chama management systems face several operational challenges.

## 2.1 Manual Record Keeping

- Heavy reliance on physical ledger books for recording financial transactions.
- Increased risk of human error in contribution tracking.

## 2.2 Inefficient Financial Monitoring

- Difficulty tracking member contributions and balances in real time.
- Lack of centralized financial visibility.

## 2.3 Communication Gaps

- No automated reminders for payments, deadlines, or meetings.
- Delayed communication among members.

## 2.4 Reporting Challenges

- Financial reports are tedious and error-prone to generate manually.
- Difficulty obtaining real-time financial insights.

## 2.5 Accountability and Transparency Issues

- Limited visibility of financial records causes distrust among members.

## 2.6 Administrative Burden

- Treasurers and Chairpersons spend excessive time managing records manually.

---

# 3. Proposed Solution

The proposed solution is a centralized digital **Chama Management Platform** that will:

- Digitize Chama records and financial transactions.
- Automate contribution tracking and reconciliation.
- Enable secure digital payments via:
  - **M-Pesa**
  - **Visa**
  - **Mastercard**
- Provide real-time notifications and reminders.
- Improve transparency through reporting and analytics.
- Support role-based access for:
  - Chairpersons
  - Treasurers
  - Members
  - Administrators

---

# 4. Business Objectives

The system aims to achieve the following objectives.

## 4.1 Market Adoption

- Enable seamless onboarding of multiple Chamas onto the platform.

## 4.2 Process Automation

- Automate contribution tracking, payment processing, and financial reconciliation.

## 4.3 Member Engagement

Send proactive notifications for:

- Late payments
- Upcoming meetings
- Deadlines
- Outstanding balances

## 4.4 Financial Visibility

- Generate financial insights and reports to improve decision-making.

## 4.5 Monetization

Create a sustainable business model through:

- Subscription fees
- Transaction fees
- Platform service charges

## 4.6 Security and Compliance

- Maintain secure financial processing.
- Ensure data integrity through audit logs and secure payment integrations.

---

# 5. Stakeholders

| Stakeholder | Role | Description |
|-------------|------|-------------|
| Meliora | System Owner | Organization responsible for developing and managing the platform |
| System Administrators | Platform Management | Manage system security, configurations, and platform health |
| Chama Chairperson | Governance | Oversees Chama policies and operations |
| Chama Treasurer | Financial Administration | Manages contributions, loans, and transaction verification |
| Chama Members | End Users | Make contributions, repay loans, and participate in meetings |

---

# 6. Business Requirements

## 6.1 Contribution Management

The system shall:

### Contribution Cycles

Support customizable contribution cycles:

- Weekly
- Fortnightly 
- Monthly
- Custom periods

### Contribution Tracking

Automatically calculate:

- Expected contributions
- Received contributions
- Remaining balances

### Payment Rules

- Support partial payments.
- Prevent users from paying beyond the required contribution amount.
- Maintain an immutable audit trail for all financial transactions.

### Notifications

Notify Treasurers about:

- Missed contributions
- Late payments
- Partial payments

### Member Access

Allow members to:

- View contribution cycle
- Track balances
- View payment history

---

## 6.2 Loan Management

The system shall:

### Loan Administration

Allow Treasurers to:

- Create loans
- Manage loans

### Loan Details

Record:

- Principal amount
- Interest
- Loan duration
- Repayment schedule

### Loan Monitoring

- Send repayment reminders before due dates.
- Track:
  - Active loans
  - Repaid loans
  - Defaulted loans

---

## 6.3 Payment Management

The system shall:

### Supported Payment Methods

- M-Pesa
- Visa
- Mastercard

### Payment Features

Allow users to:

- Make full payments
- Make partial payments

### Payment Restrictions

- Prevent overpayments.

### Payment Notifications

- Notify Treasurers immediately after payment.

### Record Keeping

- Store transaction records for auditing purposes.

---

## 6.4 Notification Management

The system shall send automated notifications for:

### Contribution Notifications

- Payment deadlines
- Late contributions
- Outstanding balances

### Meeting Notifications

- Upcoming meetings
- Schedule changes

### Loan Notifications

- Loan repayment reminders

### Payment Notifications

- Payment confirmations
- Transaction updates

The system shall support targeted notifications for members with pending obligations.

---

## 6.5 Member Management

Chairpersons and Treasurers shall be able to:

- Add members manually
- Remove members
- Edit member information

### Stored Member Data

The system shall securely store:

- Full Name
- Phone Number
- Email Address
- Joining Date
- Contribution History
- Loan Records

### Bulk Upload

Support bulk uploads using:

- CSV
- Excel files

---

## 6.6 Chama Management

The system shall allow users to:

- Create Chamas
- Edit Chama details
- Search for Chamas
- Filter Chamas
- View all members in a Chama

### Chama Creator Permissions

The Chama creator shall be able to:

- Edit Chama information
- Manage members
- Configure contribution cycles

### Chama Lifecycle

Support:

- Chama creation
- Chama editing
- Soft deletion

---

## 6.7 Meeting Management

The system shall:

### Meeting Scheduling

Schedule meetings with:

- Meeting agenda
- Date and time
- Physical or virtual location

### Meeting Alerts

- Notify members of upcoming meetings.

### Meeting History

- Maintain meeting history and minutes.

---

## 6.8 Transaction Management

The system shall allow users to:

- View transaction history
- View transaction details
- View payment records

Treasurers shall be able to:

- View all transactions
- Monitor contribution cycles
- Track payment progress

---

## 6.9 Reporting and Analytics

The system shall generate downloadable reports.

### Contribution Reports

- Individual contributions
- Group-wide contributions

### Loan Reports

- Active loans
- Repaid loans
- Defaulted loans

### Financial Reports

- Cash flow summaries
- Balance sheets
- Financial statements

### Timeline Filtering

Reports shall be filterable by:

- Weekly
- Monthly
- Fortnightly
- Custom periods

---

## 6.10 Customer Support

The system shall include customer support chat functionality allowing users to communicate with customer care.

---

# 7. User Personas and Use Cases

## 7.1 Chairperson

Responsibilities:

- Create Chama groups
- Define governance policies
- Configure penalties and loan rules
- Add or remove members
- Review reports

---

## 7.2 Treasurer

Responsibilities:

- Manage member onboarding
- Track loans and repayments
- Configure contribution cycles
- Monitor payments
- Generate financial reports

---

## 7.3 General Member

Responsibilities:

- Make contributions
- Make loan repayments
- Receive notifications
- View balances and payment history

---

# 8. Functional Requirements

The system shall provide:

## Authentication & Authorization

- User registration
- User login
- Secure authentication
- Role-Based Access Control (RBAC)

## Payment Processing

- M-Pesa integration
- Card payment integration
- Partial payment support
- Overpayment prevention

## Notifications

- Automated reminders
- Payment alerts
- Meeting reminders

## Financial Tracking

- Contribution tracking
- Loan tracking
- Transaction history

## Reporting

- Financial reporting
- Downloadable reports
- Analytics dashboards

## Chama Operations

- Chama creation
- Chama search and filtering
- Member management

## Customer Support

- Customer care chat support

## Performance Optimization

- Frequently accessed data shall be cached for improved performance.

---

# 9. Non-Functional Requirements

## 9.1 Backup and Recovery

The system shall:

- Perform automated daily backups.
- Support disaster recovery.
- Maintain a recovery objective of **less than 4 hours**.

---

## 9.2 Security

The system shall:

- Use **JWT Authentication**
- Encrypt passwords using **bcrypt**
- Secure communications using **TLS/SSL**

### Access Control

- Members shall only access permitted information.
- Treasurers shall have broader financial access.

### Modular Architecture

The system shall adopt modularization to:

- Improve maintainability
- Improve security isolation
- Reduce compromise risk between modules

---

## 9.3 Availability

The system shall maintain:

- **98% uptime**
- **24/7/365 availability**

### Performance Target

- Average response time less than **200ms**

---

## 9.4 Reliability

The system shall maintain:

- **99% operational reliability**

Testing shall include:

- Unit Testing
- Integration Testing
- User Acceptance Testing (UAT)

---

## 9.5 Performance and Latency

API endpoints shall maintain:

- Average response time below **2 seconds** under normal conditions.

---

## 9.6 Scalability

The architecture shall support:

- Up to **10× traffic growth**

### Scaling Strategy

#### Vertical Scaling
Increase server resources initially.

#### Horizontal Scaling
Adopt distributed architecture when data exceeds thresholds (**100GB+**).

The system must scale during:

- Peak contribution periods
- High transaction deadlines
- Increased user activity

The platform shall define:

- Service Level Agreement (SLA)
- Service Level Objective (SLO)
- Service Level Indicator (SLI)

---

## 9.7 Usability

The platform shall provide:

- Simple interfaces
- Minimal learning friction
- User-friendly experiences for varying technical literacy levels

---

## 9.8 Compliance

The system shall comply with:

- Financial regulations
- Privacy policies
- Terms and Conditions
- Taxation requirements

---

## 9.9 Observability

The system shall include:

### Monitoring

- Application monitoring
- Infrastructure monitoring

### Logging

- System logs
- Error logs
- Transaction logs

### Analytics

- User behavior tracking
- Usage metrics

---

# 10. Assumptions

- Chamas already have established membership structures.
- Users have internet or mobile network access.
- Users own Android or iOS smartphones.

---

# 11. Constraints

## Technical Literacy

Some users may have limited technical skills, requiring simplified UX.

## Payment Restrictions

Supported payment methods:

- M-Pesa
- Visa
- Mastercard

## Internet Connectivity

The platform must gracefully handle unstable internet connectivity.
```
