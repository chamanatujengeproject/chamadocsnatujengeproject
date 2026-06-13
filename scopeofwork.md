```md
# CHAMA - Scope of Work (SOW)

## Project Information

| Field | Details |
|--------|----------|
| **Project Name** | Chama Management System (CMS) – FundLoop |
| **Client/Organization** | Meliora |

---


# Table of Contents

1. [Project Background](#1-project-background)  
2. [Purpose Statement](#2-purpose-statement)  
3. [Project Objectives](#3-project-objectives)  
4. [Scope Description (Included)](#4-scope-description-what-is-included)  
5. [Out of Scope (Exclusions)](#5-out-of-scope-exclusions)  
6. [Deliverables](#6-deliverables)

---

# 1. Project Background

Chamas, as savings and investment groups, play a crucial role in community-based financial systems. However, current operations heavily rely on manual record-keeping and fragmented communication channels, resulting in inefficiencies, human errors, and time-consuming administrative processes.

This manual approach creates several challenges, including:

- Difficulty tracking individual member contributions and balances in real time.
- Communication gaps regarding updates, payment deadlines, and meetings.
- Delays in generating accurate financial reports.
- Limited transparency in fund management, leading to distrust among members.
- Increased administrative burden on executive roles such as **Treasurers** and **Chairpersons**.

---

# 2. Purpose Statement

The primary purpose of this project is to develop a **centralized digital platform** that simplifies Chama operations, automates financial tracking, and significantly improves transparency and accountability among members.

The platform aims to:

- Digitize Chama records, member profiles, and financial transactions.
- Provide an intuitive and accessible interface for all users.
- Automate contribution tracking and payment reminders.
- Enable real-time notifications and on-demand reporting.
- Integrate secure mobile payment gateways.
- Improve collective decision-making through data-driven insights.

---

# 3. Project Objectives

The project aims to achieve the following objectives:

## 3.1 Market Adoption

- Seamlessly onboard multiple Chamas onto the digital platform.

## 3.2 Automation

- Automate **100% of contribution collections, tracking, and reconciliation workflows**.

## 3.3 Proactive Engagement

Notify members instantly about:

- Late payments
- Contribution deadlines
- Upcoming meetings
- Outstanding balances

## 3.4 Data Insight

- Provide comprehensive financial and member-level reports to support better investment and savings decisions.

## 3.5 Monetization

Create a sustainable revenue model through:

- Subscription fees
- Transaction charges
- Platform service fees

## 3.6 Security & Compliance

- Ensure secure transaction processing through:
  - **M-Pesa**
  - **Visa**
  - **Mastercard**

- Maintain rigorous audit logs to ensure accountability and data integrity.

---

# 4. Scope Description (What is Included)

The **Chama Management System (CMS)** will include the following features and functionalities.

---

## 4.1 Contribution Management

The system shall:

- Support customizable contribution cycles:
  - Weekly
  - Fortnightly
  - Monthly
  - Custom periods

- Automatically calculate:
  - Required contributions
  - Received contributions
  - Outstanding balances

- Maintain an immutable audit trail of all financial transactions.

- Send administrative alerts for:
  - Missed contributions
  - Late contributions
  - Partial payments

- Support partial payments with immediate balance updates.

---

## 4.2 Loan Management

The system shall:

- Allow Treasurers to:
  - Create loan records
  - Maintain loan records

- Capture:
  - Principal amount
  - Interest rate
  - Loan duration
  - Repayment schedules

- Trigger proactive repayment reminder notifications.

---

## 4.3 Notification Management

The system shall implement:

### Automated Notifications

For:

- Contribution deadlines
- Upcoming meetings
- Outstanding balances
- Penalties

### Targeted Notifications

Broadcasts for members with:

- Pending obligations
- Late payments
- Incomplete contributions

Notification channels include:

- Push Notifications
- SMS Notifications

---

## 4.4 Member Management

Chairpersons and Treasurers shall be able to:

- Add members
- Edit member details
- Remove members

### Member Data Storage

The system shall securely store:

- Full Name
- Contact Information
- Joining Date
- Historical contribution records
- Loan history

### Bulk Import

Support importing members via:

- CSV files
- Excel files

---

## 4.5 Chama Management

The system shall support:

- Creation of Chama groups
- Editing Chama details
- Soft deletion of Chamas

### Role-Based Access Control (RBAC)

The platform shall enforce permissions for:

- Member
- Treasurer
- Chairperson
- Administrator

---

## 4.6 Meeting Management

Administrators shall be able to:

- Schedule meetings
- Add agendas
- Define meeting time
- Define meeting location

Meeting locations may include:

- Physical locations
- Virtual meetings

The system shall:

- Notify members about upcoming meetings.
- Maintain a historical log of meetings.

---

## 4.7 Financial Management

The system shall generate downloadable reports including:

### Contribution Reports

- Individual contributions
- Group-wide contributions

### Loan Performance Reports

- Active loans
- Repaid loans
- Defaulted loans

### Financial Statements

- Cash Flow Statements
- Summary Financial Reports
- Balance Sheets

### Timeline Filtering

Financial reports shall support filtering by:

- Weekly cycles
- Monthly cycles
- Fortnightly cycles
- Custom periods

---

## 4.8 Authentication & Access Control

The system shall implement:

### Authentication

- Secure registration
- Secure login

Supported credentials:

- Phone Number
- Email Address
- Password

### Authorization

Strict **Role-Based Access Control (RBAC)** for:

- Member
- Treasurer
- Chairperson
- Admin

---

## 4.9 Payment Gateway Integration

The system shall integrate payment processing with:

- **M-Pesa**
- **Visa**
- **Mastercard**

for instant transaction settlement.

---

## 4.10 Automated Reminder Engine

The platform shall include a background service that:

- Monitors deadlines
- Dispatches automated alerts
- Sends reminders based on schedules

---

# 5. Out of Scope (Exclusions)

The following features are excluded from this phase of development:

## Payment Exclusions

- Integration with EFT methods beyond:
  - M-Pesa
  - Visa
  - Mastercard

## Platform Exclusions

- Native mobile application development outside:
  - Android
  - iOS

## Financial Exclusions

- Advanced financial forecasting
- Investment advisory systems

## Offline Capability Exclusions

- Full offline functionality without periodic internet connectivity

---

# 6. Deliverables

| Deliverable | Description | Format | Owner |
|-------------|-------------|--------|--------|
| **Digital Platform (Web)** | Fully functional Chama Management System with all in-scope features | Web-Based | Meliora / Developers |
| **Database Schema** | Design and implementation of database structure | SQL / Diagram | Developers |
| **API Documentation** | Comprehensive documentation of system APIs | Markdown | Developers |
| **User Manuals** | Guides for Members, Treasurers, Chairpersons, and Admins | PDF / Markdown | Developers |
| **Test Cases & Results** | Documentation for unit, integration, and UAT testing | Document | Developers |
| **Training Materials** | Resources for training administrators and users | Document | Developers |

---

# Conclusion

This Scope of Work (SOW) defines the expected features, responsibilities, exclusions, and deliverables for the **FundLoop Chama Management System**. It establishes a shared understanding between stakeholders and the development team regarding project expectations, boundaries, and outcomes.
```
