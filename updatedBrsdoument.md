FUNDLOOP
Chama Management System

Business Requirements Document (BRD)



Table of Contents
1.  Introduction3
2.  Problem Statement4
3.  Proposed Solution5
4.  Chama Types Supported6
5.  Business Objectives12
6.  Stakeholders13
7.  Business Requirements14
8.  User Personas and Use Cases24
9.  Functional Requirements26
10.  Non-Functional Requirements31
11.  Assumptions35
12.  Constraints36

1. Introduction
Chamas are grassroots financial vehicles deeply embedded in Kenyan culture, serving as savings circles, investment clubs, welfare funds, and lending pools. They operate across income levels, from informal neighbourhood groups to professionally managed investment consortia.

However, most Chama operations today depend on physical ledger books, WhatsApp messages, and manual spreadsheets. These approaches introduce errors, opaque record-keeping, delayed reporting, and eroded trust.

This document defines the business requirements for FundLoop — a digital Chama Management System (CMS) designed to digitise, automate, and govern the full spectrum of Chama operations in the Kenyan market. The system is specifically engineered to support all major Chama models found in Kenya, including rotational savings (ROSCA / Merry-Go-Round), table banking, welfare funds, investment clubs, share-based groups, and hybrid combinations thereof.

2. Problem Statement
2.1 Manual Record Keeping
Heavy reliance on physical ledger books and paper receipts for tracking contributions.
High risk of arithmetic errors and data loss.
No audit trail for disputed transactions.

2.2 Inefficient Financial Monitoring
Difficulty tracking member contributions, loan balances, and interest in real time.
Lack of centralised financial visibility for all chama types.
No mechanism to automatically calculate rotational payout order or investment share allocation.

2.3 Communication Gaps
No automated reminders for contributions, loan repayments, or meetings.
Critical information spread across informal channels (WhatsApp, phone calls).

2.4 Reporting Challenges
Financial reports are generated manually, are error-prone, and lack consistency.
No real-time visibility of fund balances across wallet types (savings, loans, welfare, investment).

2.5 Trust and Accountability Issues
Limited transparency of financial records breeds distrust among members.
No robust governance mechanism for loan approvals, member removal, or constitutional changes.
Guarantor obligations are informally tracked, creating liability ambiguity.

2.6 Administrative Burden
Treasurers and Chairpersons spend excessive time on clerical tasks.
Hybrid Chamas with multiple fund types are virtually impossible to manage manually.

3. Proposed Solution
FundLoop is a centralised digital Chama Management Platform that will:

Digitise all Chama records and financial transactions across all supported Chama models.
Support multiple Chama types: ROSCA, Table Banking, Welfare, Investment, Share-Based, and Hybrid configurations.
Automate contribution tracking, loan management, interest calculation, payout scheduling, and reconciliation.
Provide multi-wallet architecture for Hybrid Chamas (Savings, Loan, Welfare, Investment wallets).
Enable secure digital payments via M-Pesa, Visa, and Mastercard.
Enforce governance via role-based access, voting workflows, and configurable Chama constitutions.
Deliver real-time notifications, reminders, and alerts for all financial and administrative events.
Generate comprehensive reports and analytics across all fund types.
Support role-based access for Chairpersons, Treasurers, Members, Guarantors, and Platform Administrators.

4. Chama Types Supported
FundLoop is designed to support all major Chama models found in Kenya. Each type has distinct operational rules, financial flows, and system logic requirements. The following seven Chama types are supported natively within the platform.

1
Rotational Payout (ROSCA / Merry-Go-Round)
Most Common Model in Kenya


Members contribute a fixed amount each cycle (weekly, fortnightly, or monthly).


One member receives the entire pooled amount each cycle in a defined rotation.


The cycle completes when every member has received the payout exactly once.


System Configuration: Fixed or lottery-based rotation order; position swapping with Treasurer approval.


Key Business Rules:


  → Members who have already received payout remain obligated to continue contributing.


  → A member who exits after receiving payout triggers a shortfall recovery process.


  → Delayed contributions generate automatic fines and block payout release.


  → Payout is only released when all contributions for the cycle are confirmed.


System Entities: Members, Contribution Cycles, Payout Schedule, Positions, Fines, Shortfall Records.


2
Table Banking / Loan Chama
Extremely Common — Operates as a Mini-Bank


Members contribute regularly into a central loan fund that stays pooled (not rotated out).


Members may borrow from the pool with defined terms: principal, interest, duration, repayment schedule.


Loan interest earned is distributed back to members as dividend or retained for group growth.


Key Business Rules:


  → Loans require Treasurer or majority approval depending on Chama constitution.


  → Maximum borrowable amount is configurable (e.g., 3× member's total contributions).


  → Guarantors are required above a threshold amount; guarantors are liable on default.


  → Defaulted loans trigger penalty calculation and may restrict member's future borrowing.


  → Interest model is configurable: flat-rate, reducing balance, or fixed monthly rate.


System Entities: Loan Fund, Loans, Repayments, Guarantors, Interest Records, Penalties, Loan Approvals.


3
Welfare / Emergency Chama
Very Common — Especially in Churches and Families


Members contribute into a welfare fund used to support members during emergencies.


Disbursements are grants (non-repayable) or loans (repayable) depending on Chama constitution.


Common qualifying events: bereavement, hospitalisation, school fees crisis, family emergencies.


Key Business Rules:


  → The Chama constitution must define qualifying emergency categories.


  → Disbursement approval requires Chairperson sign-off or committee vote.


  → Maximum welfare disbursement per event is configurable.


  → Members must have a defined minimum contribution tenure to qualify.


  → System tracks welfare disbursement history per member.


System Entities: Welfare Fund, Welfare Applications, Disbursements, Qualifying Events, Approval Workflow.


4
Investment Chama
Very Common — Especially Among Professionals


Members pool capital to invest collectively in assets: land, property, stocks, farming, SACCOs, businesses.


Profits and losses are distributed based on configured ownership model (equal shares or contribution-weighted).


Key Business Rules:


  → Ownership model is set at group creation: equal shares or contribution-proportional shares.


  → Share percentages are recalculated dynamically as new contributions are made.


  → Investment decisions require a quorum vote; voting weight is configurable.


  → Member exit triggers share valuation and payout calculation based on current asset value.


  → Investment assets are tracked as portfolio entries with current valuations.


  → Profit distributions are logged as separate transactions per member.


System Entities: Investment Fund, Investment Assets, Shares, Votes, Profit Distributions, Exit Records.


5
Share-Based Chama
Exists — Typically Investment-Focused Groups


Members purchase or accumulate shares over time; ownership is strictly proportional to shares held.


Shares may be acquired through contributions, lump-sum purchases, or secondary transfers.


Dividends, profits, and asset distributions are allocated strictly by share percentage.


Key Business Rules:


  → Share register is maintained with issuance date, price, and holder for each share lot.


  → Shares may be transferred between members with Chairperson approval.


  → Share buyback or redemption at exit is calculated at current book value or agreed valuation.


  → New share issuance (capital calls) requires member vote.


System Entities: Share Register, Share Ledger, Dividends, Capital Calls, Transfer Records.


6
Hybrid Chama
Most Realistic Everyday Model in Kenya


A single Chama operates multiple fund types simultaneously under one membership.


Each fund type (Savings, Loans, Welfare, Investment) operates as a separate wallet/ledger.


A single monthly contribution is split across wallets per a pre-defined breakdown rule.


Example: KES 5,000/month → KES 2,000 Savings | KES 1,500 Loans | KES 1,000 Welfare | KES 500 Admin


Key Business Rules:


  → Wallet allocation percentages are defined in the Chama constitution and can be amended by vote.


  → Funds do not cross wallets unless explicitly authorised by a governance vote.


  → Each wallet has independent reporting, balance tracking, and access controls.


  → A member's total balance view aggregates contributions across all active wallets.


  → Fines collected are directed to a designated fine wallet or redistributed per constitution.


System Entities: All entities from types 1–5, plus Wallet Ledgers, Wallet Allocation Rules, Cross-Wallet Transfers.


7
Fines, Governance and Discipline Module
Universal — Present in Almost Every Serious Chama


A transversal module that applies to all Chama types.


Fines are automatically assessed for: late contributions, missed meetings, late loan repayment, rule violations.


Governance workflows manage: loan approvals, new member admission, member removal, investment decisions, constitutional amendments.


Key Business Rules:


  → Fine amounts are configurable: fixed KES amount or percentage of obligation.


  → Fines may be auto-applied or require Treasurer confirmation depending on configuration.


  → Leaders (Chairperson/Treasurer) may waive fines with a justification record.


  → Votes support equal voting power or contribution-weighted power per constitution.


  → Quorum requirements are configurable (e.g., minimum 60% of members must vote).


  → All governance decisions are recorded as immutable audit log entries.


System Entities: Fine Rules, Fine Transactions, Votes, Motions, Quorum Config, Audit Log.


4.8 Chama Type Summary

Chama Type
Primary Operations
Prevalence in Kenya
ROSCA / Merry-Go-Round
Contribution tracking, payout rotation, position management
Very High
Table Banking / Loan Chama
Loan issuance, repayment tracking, interest calculation
Extremely High
Welfare / Emergency Fund
Emergency disbursements, welfare applications
Very High
Investment Chama
Asset portfolio, profit distribution, voting
High (Professional Groups)
Share-Based Chama
Share register, dividends, capital calls
Medium
Hybrid Chama
Multi-wallet management, contribution splitting
Very High
Fines & Governance
Fine automation, voting, audit log
Universal


5. Business Objectives
5.1 Market Adoption
Enable seamless onboarding of multiple Chamas across all seven supported types.
Support single-group and multi-group configurations (one member across several Chamas).

5.2 Process Automation
Automate contribution tracking, loan issuance, interest calculation, payout scheduling, and reconciliation.
Automate fine assessments, welfare application routing, and voting workflows.

5.3 Member Engagement
Send proactive, contextual notifications for:
Contribution deadlines and outstanding balances.
Rotational payout schedule and upcoming payout dates.
Loan repayment reminders and overdue alerts.
Welfare application status updates.
Investment portfolio updates and profit distribution events.
Upcoming meetings and agenda publication.

5.4 Financial Visibility
Provide real-time balance views across all wallet types per Chama and per member.
Generate actionable financial insights, dashboards, and downloadable reports.

5.5 Monetisation
Subscription fees per active Chama on the platform.
Transaction fees on digital payments processed via the platform.
Premium features: advanced analytics, API access, bulk SMS, and priority support.

5.6 Security and Compliance
Maintain secure financial processing with full audit trails.
Ensure compliance with Kenyan financial regulations and data protection requirements.
Enforce role-based access so members only see what they are authorised to see.

6. Stakeholders

Stakeholder
Role
Description
Meliora
System Owner
Organisation responsible for developing, hosting, and managing FundLoop.
System Administrators
Platform Management
Manage system security, configurations, onboarding, and platform health.
Chama Chairperson
Governance
Oversees Chama policy, approves governance decisions, manages membership.
Chama Treasurer
Financial Administration
Manages contributions, loans, wallets, fines, and transaction verification.
Chama Secretary
Administration
Manages meeting scheduling, minutes, communication, and member records.
Chama Members
End Users
Make contributions, apply for loans/welfare, vote on motions, view balances.
Guarantors
Loan Assurance
Vouch for borrowers; liable for loan recovery on default.


7. Business Requirements
7.1 Chama Setup and Configuration
The system shall:
Allow creation of a Chama specifying its type: ROSCA, Table Banking, Welfare, Investment, Share-Based, or Hybrid.
Present type-specific configuration fields upon creation (e.g., rotation order for ROSCA; interest model for Table Banking; wallet breakdown for Hybrid).
Allow a Chama constitution to be defined digitally, capturing key governance rules.
Support soft deletion of Chamas with full data retention for compliance purposes.
Allow Chama editing with an audit trail of all changes.

7.2 Contribution Management
The system shall:
7.2.1 Contribution Cycles
Support customisable contribution cycles: weekly, fortnightly, monthly, or custom periods.
For Hybrid Chamas, automatically split each contribution across configured wallet allocations.

7.2.2 Contribution Tracking
Automatically calculate expected contributions, received contributions, and remaining balances per member and per cycle.
Track contribution history at both individual and group level.
Support partial payments with balance carry-forward to the next cycle.
Prevent overpayments beyond the configured cycle amount.
Maintain an immutable audit trail for every financial transaction.

7.2.3 Notifications
Notify members of upcoming contribution deadlines.
Notify Treasurers of missed, partial, or late contributions.
Auto-generate fines for late contributions per configured fine rules.

7.3 ROSCA / Rotational Payout Management
The system shall:
Maintain a payout schedule defining the rotation order and cycle date for each member's turn.
Support fixed rotation order (pre-set) and lottery-based order assignment.
Allow position swapping between members with Treasurer approval and audit record.
Hold payout release until all member contributions for the active cycle are confirmed.
Track members who have received their payout and enforce continued contribution obligation.
Flag and manage shortfall scenarios where a member exits after receiving payout.
Notify the next-in-rotation member 7 days and 1 day before their payout date.

7.4 Loan Management
The system shall:
7.4.1 Loan Lifecycle
Support loan application by members, including requested amount, purpose, and proposed guarantors.
Route loan applications through an approval workflow (Treasurer approval or member vote per constitution).
Disburse approved loans directly to member M-Pesa or registered bank account.
Track loan status: Applied, Approved, Disbursed, Active, Repaid, Defaulted.

7.4.2 Loan Terms
Record: principal amount, interest rate and model (flat, reducing balance, fixed monthly), duration, repayment schedule.
Support configurable maximum loan multiplier (e.g., member may borrow up to 3× total contributions).
Calculate and present full repayment schedule to borrower prior to acceptance.

7.4.3 Guarantor System
Allow one or more guarantors to be assigned to a loan above a configured threshold amount.
Notify guarantors of the loan they are guaranteeing and request explicit digital acceptance.
On default, calculate guarantor liability proportionally and generate recovery requests.
Track guarantor exposure (total guaranteed amount outstanding) per member.

7.4.4 Repayment and Default
Send repayment reminders 7 days, 3 days, and 1 day before each instalment due date.
Automatically mark instalments as overdue and apply configured penalty rates.
Track active, partially repaid, fully repaid, and defaulted loans separately.

7.5 Welfare and Emergency Fund Management
The system shall:
Maintain a welfare fund wallet separate from savings and loan wallets.
Allow members to submit welfare applications specifying the qualifying event type and amount requested.
Route welfare applications through an approval workflow per constitution (Chairperson sign-off or committee vote).
Enforce minimum contribution tenure eligibility for welfare access.
Configure maximum disbursement amounts per event category.
Track whether disbursements are grants (non-repayable) or welfare loans (repayable).
Maintain full disbursement history per member and per event type.

7.6 Investment Management
The system shall:
Maintain an investment portfolio ledger tracking all assets acquired by the group.
Support configurable ownership model: equal shares per member or contribution-proportional shares.
Record asset entries: asset name, acquisition date, cost, current valuation, and valuation date.
Calculate each member's ownership percentage dynamically based on the configured model.
Record and distribute profits to members proportionally per ownership at distribution date.
Support voting on investment decisions with configurable quorum requirements.
Calculate member payout on exit based on their share of current portfolio valuation.

7.7 Share-Based Ownership Management
The system shall:
Maintain a share register with share issuance date, price, quantity, and holder.
Support share transfers between members with Chairperson approval.
Support capital calls (new share issuance) subject to member vote.
Calculate dividend distributions strictly by share percentage at each distribution event.
Support share redemption or buyback at exit based on agreed valuation methodology.

7.8 Hybrid Chama Multi-Wallet Management
The system shall:
Create and maintain separate wallet ledgers for each active fund type within a Hybrid Chama.
Apply wallet allocation rules to split each contribution across designated wallets automatically.
Maintain wallet independence: funds do not cross wallets without an authorised cross-wallet transfer.
Provide aggregated and per-wallet balance views for each member and for the group.
Support governance votes to amend wallet allocation percentages.
Direct fine collections to the designated fine wallet per constitution.

7.9 Fines and Discipline Management
The system shall:
Allow Treasurers to configure fine rules: trigger event, fine amount (fixed KES or % of obligation), and grace period.
Automatically calculate and record fines on trigger events (late contribution, missed meeting, late repayment).
Support manual fine assessment by Treasurers for rule violations not covered by automation.
Allow fine waiver by Chairperson or Treasurer with a mandatory justification note (audit-logged).
Maintain a fine ledger per member showing assessed, waived, and collected fines.

7.10 Voting and Governance
The system shall:
Support creation of motions by Chairperson or Treasurer for member voting.
Motion types: Loan approval, New member admission, Member removal, Investment decision, Constitutional amendment, Share issuance.
Configure voting power model: equal votes per member or contribution-weighted votes.
Configure quorum threshold (minimum participation % for a vote to be valid).
Notify all eligible voters upon motion creation with a defined voting deadline.
Record each member's vote and generate outcome report upon deadline.
Archive all votes, outcomes, and supporting records in an immutable audit log.

7.11 Member Exit and Withdrawal
The system shall:
Allow a member to submit an exit request subject to Chairperson approval.
Block exit if the member has an outstanding loan balance or active guarantor obligation.
Calculate exit payout: total contributions, share of investment portfolio, accrued dividends, minus outstanding obligations and penalties.
Generate a detailed exit settlement statement for Treasurer and exiting member.
Archive the member's full contribution, loan, and transaction history after exit.

7.12 Payment Management
The system shall:
Support M-Pesa, Visa, and Mastercard as payment channels.
Allow full and partial payments for contributions and loan repayments.
Prevent overpayments beyond the configured obligation amount.
Confirm each payment with a real-time notification to the member and Treasurer.
Store immutable transaction records for all payments processed through the platform.

7.13 Meeting Management
The system shall:
Allow scheduling of meetings with: agenda, date, time, and physical or virtual location.
Notify all members of upcoming meetings with configurable lead time.
Record meeting attendance and link it to fine automation for absentees.
Maintain a history of meeting minutes uploaded or recorded in the system.

7.14 Reporting and Analytics
The system shall generate downloadable reports including:
Contribution reports: individual and group-wide, per cycle, per wallet.
Loan reports: active loans, repayment status, defaulted loans, guarantor exposure.
ROSCA payout schedule and completion status.
Investment portfolio report: asset valuations, profit distributions, member share %.
Welfare fund report: disbursements by event type, fund balance, eligibility status.
Fine report: assessed, waived, and collected fines per member.
Cash flow summary, balance sheet, and financial statements for the group.
Reports filterable by: weekly, fortnightly, monthly, quarterly, custom period.

7.15 Notifications
The system shall deliver automated notifications for:
Contribution: deadlines, late payments, partial payments, outstanding balances.
ROSCA: upcoming payout, payout released, contribution confirmation.
Loans: application received, approved/rejected, disbursement, repayment reminders, overdue alerts.
Welfare: application received, approved/rejected, disbursement confirmed.
Investment: profit distribution, portfolio update, vote required.
Governance: new motion, vote deadline reminder, outcome announced.
Meetings: upcoming meeting, minutes published.
Fines: fine assessed, fine waived, fine outstanding.

7.16 Member Management
The system shall:
Allow Chairpersons and Treasurers to add, edit, and deactivate members.
Store member data: full name, phone, email, national ID, joining date, contribution history, loan records.
Support bulk member uploads via CSV or Excel files.
Support a member invitation flow where an invited member completes registration independently.

7.17 Customer Support
Provide in-app customer support chat for members to communicate with Meliora support staff.
Support a FAQ / knowledge base accessible from within the platform.

8. User Personas and Use Cases

Persona
Primary Responsibilities
Chairperson
Create and configure Chama, define constitution, manage governance, approve member exits, oversee voting.
Treasurer
Manage contributions, loans, wallets, fines, payment verification, report generation.
Secretary
Schedule meetings, record minutes, manage member communications.
General Member
Make contributions, apply for loans/welfare, repay loans, cast votes, view balances.
Guarantor
Accept or decline guarantee requests, track guaranteed exposure, respond to default recovery.
System Administrator
Onboard Chamas, manage platform health, resolve escalations, configure system settings.


9. Functional Requirements
9.1 Authentication and Authorisation
User registration with phone number and email verification.
Secure login with JWT-based authentication.
Role-Based Access Control (RBAC): System Admin, Chairperson, Treasurer, Secretary, Member, Guarantor.
Multi-Chama membership: one user account may belong to multiple Chamas with independent roles per group.
Session management with configurable timeout.

9.2 Chama Configuration Engine
Chama type selector at creation: ROSCA, Table Banking, Welfare, Investment, Share-Based, Hybrid.
Type-specific configuration wizard presenting only relevant fields for the chosen type.
Constitution builder: define contribution amounts, fine rules, loan limits, voting rules, wallet allocations.
Chama type can be extended (e.g., a Table Banking group adding a Welfare wallet) with Chairperson approval.

9.3 ROSCA Engine
Payout schedule generator: creates a full rotation cycle upon Chama setup.
Position swap workflow with dual-party confirmation and audit record.
Payout lock: blocks release until all cycle contributions are confirmed.
Shortfall handler: auto-triggers recovery process when a post-payout member defaults.

9.4 Loan Engine
Loan application form with guarantor nomination and purpose statement.
Approval workflow: Treasurer single-approval or member vote based on constitution.
Interest calculator: flat-rate, reducing balance, or fixed monthly — schedule displayed before disbursement.
Guarantor acceptance flow with digital acknowledgement.
Automated repayment reminders and overdue penalty calculator.
Loan default handling: penalty accrual, guarantor liability apportionment, recovery tracking.

9.5 Welfare Engine
Welfare application form with event type, description, amount, and supporting document upload.
Eligibility checker: validates minimum tenure and fund availability before allowing submission.
Approval workflow with configurable approver roles.
Grant vs. welfare loan designation with repayment tracking for loans.

9.6 Investment and Share Engine
Asset registry: create, update, and retire investment assets with valuations.
Ownership calculator: real-time share percentages per ownership model (equal or contribution-weighted).
Profit distribution engine: calculates per-member distribution at each event, records transactions.
Share register CRUD: issuance, transfer, redemption with full audit trail.
Exit valuation calculator: member payout on withdrawal based on share of portfolio.

9.7 Multi-Wallet Engine (Hybrid)
Wallet ledger per fund type within a Hybrid Chama.
Contribution splitter: routes incoming payment to wallets per allocation rules at time of receipt.
Cross-wallet transfer authorisation workflow.
Aggregated balance dashboard and per-wallet drill-down.

9.8 Fines and Governance Engine
Fine rule builder: define trigger, amount, grace period, and target wallet.
Automated fine assessment on trigger events.
Fine waiver workflow with mandatory justification field.
Motion builder: create, publish, vote, close, and archive governance motions.
Voting engine: equal or weighted votes, quorum validation, outcome calculation.

9.9 Payment Processing
M-Pesa STK Push integration for mobile-initiated payments.
M-Pesa C2B integration for manual paybill payments.
Visa and Mastercard card payment integration.
Partial payment support with balance carry-forward.
Overpayment prevention with real-time validation.
Payment confirmation notifications (member and Treasurer).

9.10 Notifications Engine
SMS notifications via integrated bulk SMS provider.
In-app push notifications.
Email notifications.
Configurable notification preferences per member (opt-out of specific channels).
Notification log viewable by the member.

9.11 Member Management
Member profile management by Chairperson and Treasurer.
Member invitation via SMS/email link.
Bulk member upload via CSV/Excel.
Member exit workflow with settlement calculation.

9.12 Reporting and Analytics
Report generation for all fund types, filterable by date range.
Downloadable formats: PDF and Excel.
Analytics dashboard: active loans, contribution rates, fund balances, ROSCA progress.
Treasurer's reconciliation view: expected vs. received per cycle.

9.13 Meeting Management
Meeting scheduler with agenda builder.
Attendance register linked to fine automation.
Minutes upload and historical meeting archive.

9.14 Audit Log
Immutable audit log for all financial transactions, governance decisions, member changes, and admin actions.
Audit log searchable by actor, date, and event type.
Audit log accessible to Chairperson, Treasurer, and System Administrators.

9.15 Customer Support
In-app live chat with Meliora support team.
In-app FAQ and knowledge base.
Ticket logging for escalated issues.

10. Non-Functional Requirements
10.1 Security
JWT-based authentication with access token and refresh token model.
Password hashing using bcrypt with a minimum work factor of 12.
All data in transit encrypted using TLS 1.2 or higher.
All sensitive data at rest encrypted using AES-256.
Role-Based Access Control (RBAC): members access only their own data; Treasurers access group financials; Admins access platform-wide data.
API rate limiting to prevent abuse.
Input validation and sanitisation on all endpoints to prevent injection attacks.
Multi-wallet data isolation: wallet ledgers are access-controlled independently.
Guarantor data is visible only to the loan applicant, assigned guarantors, and Treasurer.

10.2 Availability
Target uptime: 98% SLA, aiming for 99% operational reliability.
24/7/365 availability with planned maintenance windows communicated in advance.
Average API response time: under 200ms for standard operations.
Graceful degradation: offline mode for read-only data when network is unavailable.

10.3 Performance
API endpoints: average response time below 2 seconds under normal load.
Contribution split calculations (Hybrid Chamas): completed within 500ms.
Report generation for up to 12 months of data: completed within 10 seconds.
System must handle peak load during end-of-month contribution deadlines (expected 10× normal traffic).
Frequently accessed data (member balances, payout schedules, loan status) shall be cached for performance.

10.4 Scalability
Architecture shall support up to 10× traffic growth without redesign.
Vertical scaling for initial growth; horizontal (distributed) scaling when data exceeds 100GB.
Multi-wallet and multi-Chama-type support must scale independently per module.
Define and publish SLA, SLO, and SLI for each critical service (contributions, loans, payments).

10.5 Reliability
99% operational reliability target.
Automated daily database backups with point-in-time recovery.
Disaster recovery objective: system restoration within 4 hours of critical failure.
Financial transaction engine must guarantee idempotency: duplicate payment submissions must not create duplicate records.
Testing coverage: unit tests, integration tests, and User Acceptance Testing (UAT) before each release.

10.6 Usability
Simple, intuitive interfaces designed for varying levels of technical literacy.
ROSCA payout schedules, loan repayment schedules, and wallet balances must be presented in plain-language summaries, not raw data.
Chama type setup wizard guides non-technical Chairpersons through configuration without ambiguity.
Mobile-first responsive design for Android and iOS users.
Support for both English and Swahili interface languages.

10.7 Compliance
Comply with Kenya's Data Protection Act (2019) for personal data handling.
Comply with Central Bank of Kenya (CBK) regulations applicable to digital payment facilitation.
Maintain audit trails sufficient to support regulatory inspection.
Tax computation and reporting support for chamas with taxable income.
Terms and Conditions and Privacy Policy prominently accessible within the platform.

10.8 Backup and Recovery
Automated daily full backups; transaction log backups every 15 minutes.
Backups stored in at least two geographically distinct locations.
Backup integrity verified weekly via automated restore tests.
Recovery Time Objective (RTO): less than 4 hours.
Recovery Point Objective (RPO): less than 15 minutes of data loss.

10.9 Observability
Application performance monitoring (APM) covering all API endpoints and background jobs.
Structured logging for: system events, errors, financial transactions, and governance actions.
Real-time alerting for: payment failures, error rate spikes, latency degradation, and scheduled job failures.
Usage analytics: active Chamas, transaction volumes, notification delivery rates.

10.10 Modular Architecture
System shall be built as independently deployable modules: Auth, ROSCA Engine, Loan Engine, Welfare Engine, Investment Engine, Share Engine, Wallet Engine, Fines Engine, Governance Engine, Payments, Notifications, Reporting.
Module boundaries reduce the risk of cross-component compromise.
New Chama type modules can be added without modifying existing modules.

11. Assumptions
Chamas already have established membership structures and internal governance rules at the point of onboarding.
Users have access to internet or mobile data connectivity for core platform operations.
Users own Android or iOS smartphones capable of running the FundLoop mobile application.
M-Pesa is the primary payment channel for the majority of Kenyan Chama members.
Chama Chairpersons are the primary onboarding actors responsible for initial Chama configuration.
Financial data entered into the platform by Treasurers is accurate at the time of entry.
Chamas operating as Investment or Share-Based groups accept responsibility for tracking asset valuations externally and inputting them into the system.

12. Constraints
12.1 Technical Literacy
Some users, particularly in rural or less digitally connected communities, may have limited technical skills. The UX must accommodate low-literacy users through simplified flows, iconography, and Swahili language support.

12.2 Payment Methods
Supported payment channels are limited to M-Pesa, Visa, and Mastercard. No cryptocurrency or informal payment methods are supported in the initial release.

12.3 Connectivity
The platform must gracefully handle unstable internet connectivity. Core contribution and balance views shall be accessible in a cached offline mode. Transactions will queue and sync when connectivity is restored.

12.4 Asset Valuation
For Investment and Share-Based Chamas, the platform does not provide automated external asset valuations (e.g., property or stock market feeds) in the initial release. Treasurers manually input updated valuations at configured intervals.

12.5 Legal Enforceability
Governance decisions (votes, motions) recorded in the platform serve as internal group records. FundLoop does not provide legal services; the enforceability of Chama decisions is subject to Kenyan law and the group's own constitution.
