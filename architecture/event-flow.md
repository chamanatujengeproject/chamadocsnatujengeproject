# architecture/event-flow.md

# FUNDLOOP EVENT FLOW ARCHITECTURE

## 🧠 Core Principle

The system is not a set of modules.

It is a **continuous chain of events** where:

> Every action triggers a state change, which triggers another event.

Think of it as a **money + decision + trust machine**.

---

# 1. USER ENTRY FLOW (Identity → Membership Creation)

            ```text
            User Registers
                ↓
            Verification Token Created
                ↓
            User Verified (Email/Phone)
                ↓
            User Logs In (Session Created)
                ↓
            Role Assigned (Member/Admin)
                ↓
            Joins or Creates Chama
                ↓
            Member Profile Activated
                ↓
            Audit Log Written
                ↓
            Notification Sent


CHAMA SETUP FLOW (System Initialization)



                Chama Created
                    ↓
                Chama Type Selected
                    ↓
                Constitution Defined
                    ↓
                Governance Rules Configured
                    ↓
                Wallet Structure Initialized
                    ↓
                Member Roles Assigned
                    ↓
                Default Contribution Rules Set
                    ↓
                Audit Event Logged


. CONTRIBUTION FLOW (Money Ingress Engine)


            Contribution Cycle Created
                ↓
            Member Receives Payment Request
                ↓
            Member Sends Money (M-Pesa/Card)
                ↓
            Payment Gateway Confirms Transaction
                ↓
            Wallet Receives Funds
                ↓
            Contribution Record Updated
                ↓
            Member Balance Adjusted
                ↓
            Cycle Progress Recalculated
                ↓
            Notification Sent (Success/Reminder)
                ↓
                


    


WALLET ALLOCATION FLOW (Money Distribution Engine)

            Contribution Received
                ↓
            Allocation Rules Evaluated
                ↓
            Split Funds Into Wallet Buckets:
                - Savings Wallet
                - Loan Pool
                - Welfare Fund
                - Investment Pool
                ↓
            Wallet Balances Updated
                ↓
            Ledger Entries Created
                ↓
            Audit Log Recorded




LOAN ELIGIBILITY FLOW (Credit Readiness Engine)

        Contribution Updates
            ↓
        System Evaluates Member Score
            ↓
        Checks:
            - Contribution consistency
            - Outstanding loans
            - Penalties/fines
            - Wallet balance
            ↓
        Eligibility Status Updated
            ↓
        Member Notified (Eligible / Not Eligible)




LOAN APPLICATION FLOW (Credit Request Lifecycle)


        Member Submits Loan Request
            ↓
        Loan Application Created
            ↓
        System Performs Pre-checks:
            - Eligibility
            - Wallet liquidity
            ↓
        Guarantors Assigned
            ↓
        Guarantor Notifications Sent
            ↓
        Guarantors Accept/Reject
            ↓
        Governance Vote (Optional)
            ↓
        Loan Approved / Rejected
            ↓
        Audit Log Recorded


LOAN DISBURSEMENT FLOW (Money Outflow Engine)


        Loan Approved
            ↓
        Loan Record Activated
            ↓
        Repayment Schedule Generated
            ↓
        Wallet Funds Allocated
            ↓
        Payment Gateway Initiates Transfer
            ↓
        Member Receives Money
            ↓
        Loan Balance Activated
            ↓
        Notifications Sent
            ↓
        Audit Logged




. LOAN REPAYMENT FLOW (Credit Closure Engine)

            Repayment Due Date Triggered
                ↓
            Member Sends Payment
                ↓
            Payment Gateway Confirms
                ↓
            Loan Balance Reduced
                ↓
            Interest Calculated (if applicable)
                ↓
            Repayment Schedule Updated
                ↓
            Loan Status Updated:
                - Active → Completed / Default
                ↓
            Penalty Applied (if late)
                ↓
            Audit Log Recorded




ROSCA ROTATION FLOW (Merry-Go-Round Engine)
                Cycle Created
                    ↓
                Members Contribute
                    ↓
                Wallet Pool Accumulates Funds
                    ↓
                Rotation Order Determined
                    ↓
                Member Selected for Payout
                    ↓
                Payout Approved
                    ↓
                Funds Disbursed
                    ↓
                Next Rotation Scheduled
                    ↓
                Cycle Repeats




WELFARE FLOW (Emergency Support Engine)
            Member Submits Welfare Request
                ↓
            Request Validated
                ↓
            Category Assigned (Medical, Funeral, etc.)
                ↓
            Governance Approval (Optional)
                ↓
            Funds Checked in Welfare Wallet
                ↓
            Payment Disbursed
                ↓
            Request Marked Completed
                ↓
            Audit Logged





11. INVESTMENT FLOW (Wealth Creation Engine)
            Investment Proposal Created
                ↓
            Members Vote / Approve
                ↓
            Funds Allocated from Wallet
                ↓
            Asset Purchased
                ↓
            Asset Recorded in Portfolio
                ↓
            Asset Value Updated Over Time
                ↓
            Profit Generated
                ↓
            Profit Distributed or Reinvested
                ↓
            Audit Logged




12. FINE & DISCIPLINE FLOW (Rule Enforcement Engine)
                Violation Detected (e.g. missed meeting)
                    ↓
                Fine Rule Evaluated
                    ↓
                Fine Created
                    ↓
                Member Notified
                    ↓
                Payment Requested
                    ↓
                Member Pays or Requests Waiver
                    ↓
                Governance Reviews Waiver (if needed)
                    ↓
                Fine Closed
                    ↓
                Audit Recorded



13. GOVERNANCE VOTING FLOW (Decision Engine)
                Motion Created
                    ↓
                Members Notified
                    ↓
                Voting Period Opens
                    ↓
                Votes Cast
                    ↓
                Quorum Checked
                    ↓
                Result Calculated
                    ↓
                Decision Enforced:
                    - Loan approval
                    - Rule change
                    - Welfare approval
                    ↓
                Audit Logged





14. PAYMENT PROCESSING FLOW (External Money Bridge)


            Payment Initiated
                ↓
            Gateway Selected (M-Pesa / Card)
                ↓
            Transaction Sent
                ↓
            Gateway Response Received
                ↓
            Success / Failure Determined
                ↓
            Wallet Updated
                ↓
            Downstream Module Triggered:
                - Contribution update
                - Loan repayment
                - Welfare payout
                ↓
            Audit Log Recorded




15. NOTIFICATION FLOW (Event Propagation Engine)
                    System Event Triggered
                ↓
            Event Captured (Domain Event)
                ↓
            Notification Rule Evaluated
                ↓
            Message Created
                ↓
            Queue (SMS / Email / Push)
                ↓
            Delivery Attempt
                ↓
            Delivery Status Updated
                ↓
            User Acknowledges (optional)


16. AUDIT FLOW (TRUTH LAYER - ALWAYS RUNNING)

            Any System Action Occurs
                ↓
            Before State Captured
                ↓
            Action Executed
                ↓
            After State Captured
                ↓
            Audit Record Created
                ↓
            Stored Immutable Log
                ↓
            Available for:
                - Compliance
                - Dispute resolution
                - Analytics



                