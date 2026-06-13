


System overview 
What is FundLoop?
Chamas are grassroots financial vehicles deeply embedded in Kenyan culture, serving as savings circles, investment clubs, welfare funds, and lending pools. They operate across income levels, from informal neighbourhood groups to professionally managed investment consortia.

However, most Chama operations today depend on physical ledger books, WhatsApp messages, and manual spreadsheets. These approaches introduce errors, opaque record-keeping, delayed reporting, and eroded trust.

FundLoop — a digital Chama Management System (CMS) designed to digitise, automate, and govern the full spectrum of Chama operations in the Kenyan market. The system is specifically engineered to support all major Chama models found in Kenya, including rotational savings (ROSCA / Merry-Go-Round), table banking, welfare funds, investment clubs, share-based groups, and hybrid combinations thereof.


Who are the actors?
memebers 
chama treasureer 
chama chairman
chama admin
gurator




What problem are we solving?
 most Chama operations today depend on physical ledger books, WhatsApp messages, and manual spreadsheets. These approaches introduce errors, opaque record-keeping, delayed reporting, and eroded trust.

What are the core workflows?
           1. System Entry Workflow (Identity → Access → Multi-Chama Context)
            Flow

            User registers → verifies identity → logs in → joins/creates chama → gets role-based access

            Cross-module interaction
            Auth Module: User, Role, Session, Tokens
            Member Module: MemberProfile, MemberRole, Membership
            Chama Module: Chama access 
                            


             2. Chama Formation & Governance Setup Workflow
                Flow

             Create chama → define type → set constitution → configure rules → assign admin structu
                




                
                
                
                3.Member Lifecycle Workflow
                        Flow

                        Invite member → accept → join → participate → exit / suspend


                4.Money Inflow Workflow (Contributions Engine)
                    Flow

                    Contribution cycle created → members contribute → partial payments tracked → balances updated → cycle closes


                5.Rotational Payout Workflow (ROSCA Engine)
                Flow

                Rotation schedule created → positions assigned → contributions collected → payout triggered → next rotation begins

                Cross-module interaction
                ROSCA module
                Contributions (input dependency)
                Payments (disbursement)
                Notifications (payout alerts)
                Governance (swap rules)



                6.Loan Lifecycle Workflow (Credit Engine)
                    Flow

                    Apply → review → approve → disburse → repay → close/default

                    Cross-module interaction
                    Loan module
                    Guarantors (security layer)
                    Payments (repayment tracking)
                    Fines (penalties)
                    Governance (approval voting if needed)


                7. Guarantee & Recovery Workflow (Risk Containment Layer)
                    Flow

                    Borrower requests loan → guarantor assigned → guarantor accepts → default triggers recovery → liability enforced

                    Cross-module interaction
                    Guarantor module
                    Loan module
                    Audit (liability tracking)
                    Payments (recovery flow)
                    State transition
                    Pending Acceptance → Active Guarantee → Triggered Liability → Recovery Closed
                    Key idea

                    This is your trust enforcement mechanism


                8. Welfare Emergency Workflow (Safety Net System)
                    Flow

                    Member applies → verification → approval → disbursement → closure

                    Cross-module interaction
                    Welfare module
                    Governance (approval flow)
                    Payments
                    Notifications
                    Audit
                    State transition
                    Requested → Under Review → Approved → Paid → Closed








                    9. Investment & Wealth Growth Workflow
                    Flow

                    Pool funds → acquire assets → value tracking → profit distribution → exit

                    Cross-module interaction
                    Investment module
                    Wallets
                    Share module
                    Reporting
                    State transition
                    Funded → Active Asset → Valued → Profitable → Distributed
                    Core insight

                    This is the wealth multiplication layer












                    10. Share Ownership Workflow (Capital Structure Engine)
                    Flow

                    Shares issued → owned → transferred → dividends distributed → redeemed

                    Cross-module interaction
                    Share module
                    Investment module
                    Governance (capital calls)
                    Wallets
                    State transition
                    Issued → Held → Transferred → Rewarded → Redeemed
                    11. Wallet & Money Segregation Workflow
                    Flow

                    Money enters system → split into wallets → tracked independently → transferred under rules → reconciled

                    Cross-module interaction
                    Wallet module
                    Contributions
                    Payments
                    Investments
                    Audit
                    State transition
                    Incoming → Allocated → Isolated Ledger → Transferred → Reconciled
                    Core insight

                    This is your financial boundary enforcement system








                    12. External Payment Workflow (Money Gateway Layer)
                    Flow

                    User pays → gateway processes → confirmation → system records → updates downstream modules

                    Cross-module interaction
                    Payment module
                    Contributions / Loans / Welfare / ROSCA
                    Notifications
                    Audit
                    State transition
                    Initiated → Pending → Confirmed → Failed → Reconciled








                    13. Governance Decision Workflow (System Brain)
                    Flow

                    Motion created → discussion → vote → quorum check → outcome enforced

                    Cross-module interaction
                    Governance module
                    Loans (approval)
                    Chama rules
                    Notifications
                    Audit
                    State transition
                    Proposed → Voting → Approved/Rejected → Enforced
                    Key insight

                    This is your distributed decision engine












                    14. Meetings Workflow (Coordination Layer)
                    Flow

                    Schedule meeting → notify members → attendance tracking → minutes recorded → fines applied

                    Cross-module interaction
                    Meetings
                    Members
                    Fines
                    Notifications
                    Governance
                    State transition
                    Scheduled → Held → Documented → Closed










                    15. Fines & Discipline Workflow (Behavior Enforcement)
                    Flow

                    Violation detected → fine issued → payment or waiver → resolution

                    Cross-module interaction
                    Fines module
                    Meetings (absenteeism)
                    Payments
                    Governance (waivers)
                    State transition
                    Issued → Pending → Paid/Waived → Closed















                    16. Notifications Workflow (Event Propagation System)
                    Flow

                    Event happens anywhere → notification created → queued → delivered → logged

                    Cross-module interaction

                    ALL modules emit events into this system

                    State transition
                    Event → Queued → Delivered → Acknowledged
















                    17. Reporting & Analytics Workflow (System Intelligence Layer)
                    Flow

                    Data collected → aggregated → analyzed → exported → consumed

                    Cross-module interaction
                    All modules feed data
                    Audit logs
                    Financial systems
                    Output types
                    Financial reports
                    Risk reports
                    Contribution performance
                    Member behavior analytics

















                    18. Audit & Compliance Workflow (Immutable Truth Layer)
                    Flow

                    Every action → logged → stored immutably → queried for compliance → used for disputes

                    Cross-module interaction

                    EVERY module writes here

                    State transition
                    Action → Logged → Immutable Record → Audit Query
















                    19. Support Workflow (User Assistance Layer)
                    Flow

                    Issue raised → ticket created → assigned → resolved → closed

                    Cross-module interaction
                    Support module
                    Notifications
                    Audit logs
                    User system

















                    20. Admin / Platform Workflow (System Control Plane)
                    Flow

                    Platform config → monitoring → onboarding chama → billing → enforcement

                    Cross-module interaction

                    ALL modules indirectly controlled





What kinds of chamas exist?

                        . Rotating Savings Chama (ROSCA / Merry-Go-Round)
                        Core idea

                        Everyone contributes fixed amounts, and one member takes the full pot in turns.

                        Flow
                        Members contribute weekly/monthly
                        One member receives payout per cycle
                        Rotation continues until everyone has received once


                        Accumulation Savings Chama (ASCA)
                        Core idea

                        Money is pooled, saved, and later lent out or invested.

                        Flow
                        Members contribute regularly
                        Funds accumulate
                        Group issues loans or invests
                        Profits are redistributed
                        Key property

                        Money grows inside the group




                        Investment Chama
                        Core idea

                        Members pool money specifically to invest in assets.

                        Flow
                        Contributions → pooled capital
                        Investment in land, stocks, businesses
                        Returns distributed or reinvested
                        Key property

                        Focus is wealth creation, not lending




                        Credit / Lending Chama
                        Core idea

                        Acts like a small internal bank.

                        Flow
                        Members contribute capital
                        Members borrow at agreed interest
                        Loans are repaid with interest
                        Interest becomes group income
                        Key property

                        Core engine is credit creation



                        Welfare Chama
                        Core idea

                        Emergency support system.

                        Flow
                        Members contribute regularly
                        Funds used for emergencies (funerals, illness, accidents)
                        No repayment required
                        Key property

                        Pure risk-sharing + social safety net




                        Hybrid Chama
                        Core idea

                        Combines multiple models.

                        Flow
                        Savings + loans + investment + welfare all coexist
                        Funds are split into “buckets”
                        Key property

                        Most modern digital chama systems (like your FundLoop idea) fall here




                        Investment + ROSCA Hybrid
                        Core idea

                        Rotation exists, but funds are also invested between cycles.

                        Flow
                        Contributions collected
                        Part used for ROSCA payout
                        Remaining invested
                        Key property

                        Balances liquidity + growth




                        SACCO-like Chama (Formalized Chama)
                        Core idea

                        Highly structured, regulated cooperative.

                        Flow
                        Members contribute shares
                        Can take loans based on shares
                        Strict governance + audits
                        Key property

                        Bridges informal chama → formal financial institution




                        Project-Based Chama
                        Core idea

                        Built for a single goal.

                        Flow
                        Contributions until target is reached
                        Funds used for specific project
                        Chama dissolves or resets
                        Example
                        Buying land together
                        Starting a business
                        Building a house





                        Digital / Platform Chama (Modern Evolution)
                        Core idea

                        Chama exists inside software with automation.

                        Flow
                        Smart rules handle contributions, penalties, loans
                        Automated payouts, reminders, analytics
                        Governance via digital voting
                        Key property

                        This is what your FundLoop system is enabling




                        1. Save → ROSCA, ASCA
                        2. Lend → Credit chama
                        3. Share risk → Welfare chama
                        4. Invest → Investment chama

                        Everything else is just a combination.




What money flows exist?



                . Incoming Flow (Inflow → System Entry Money)
                What it is

                Money entering the system from outside.

                Sources
                Member contributions
                Loan repayments (external borrowers if any)
                Investment returns (external markets)
                Capital injections (admins / sponsors)
                State change
                Outside → System Wallet
                Key idea

                This is the only way the system “gets richer” from the outside world.

                2. Internal Transfer Flow (Within System Movement)
                What it is

                Money moving between internal accounts without leaving the system.

                Examples
                Member wallet → ROSCA pool
                Wallet → Loan disbursement pool
                Chama general fund → welfare fund
                Investment pool → operational wallet
                State change
                Wallet A → Wallet B (same system boundary)
                Key idea

                No new value is created—just reallocation of existing liquidity.

                3. Outgoing Flow (System Exit Money)
                What it is

                Money leaving the system entirely.

                Examples
                Loan disbursement to member (if external bank/mobile money)
                Welfare payouts
                ROSCA payouts to members
                Investment purchases (land, stocks, assets outside system)
                State change
                System → External world
                Key idea

                This is where liquidity becomes real-world value

                4. Redistribution Flow (Circular Money Movement)
                What it is

                Money cycling back to members in structured patterns.

                Examples
                ROSCA rotation payouts
                Dividend distributions
                Profit sharing
                Interest returns
                State pattern
                Member A → Pool → Member B → Pool → Member C
                Key idea

                This is the core “social engine” of chamas

                5. Credit Flow (Borrow → Repay Cycle)
                What it is

                Money temporarily leaving the system with obligation to return.

                Flow
                Loan issued
                Member receives funds
                Repayment over time
                Interest added
                State change
                Pool → Member → Pool (+interest)
                Key idea

                This is time-shifted money flow (future liquidity borrowing)

                6. Risk Coverage Flow (Welfare / Insurance Flow)
                What it is

                Money pooled to absorb shocks.

                Flow
                Members contribute regularly
                Emergency triggers payout
                No repayment required
                State pattern
                Many → Pool → One (in distress)
                Key idea

                This is risk redistribution, not wealth creation

                7. Investment Growth Flow (Value Multiplication Flow)
                What it is

                Money converted into assets that grow.

                Flow
                Pool → Asset purchase
                Asset → appreciation / profit
                Profit → reinvested or distributed
                State change
                Cash → Asset → Increased Value → Cash
                Key idea

                This is the only true wealth-generation flow

                8. Loss / Leakage Flow (Negative Flow)
                What it is

                Money leaving without return or value creation.

                Examples
                Loan default
                Penalties unpaid
                Fraud or mismanagement
                Fees (platform or transaction costs)
                State change
                System → Loss sink
                Key idea

                This is the health indicator of your system

                9. Allocation Flow (Splitting Flow)
                What it is

                One inflow being split into multiple destinations.

                Example

                A contribution of 1000 KES:

                500 → savings pool
                200 → loan pool
                100 → welfare fund
                200 → investment fund
                State pattern
                One → Many
                Key idea

                This is how hybrid chamas operate internally

                10. Reconciliation Flow (Balancing Flow)
                What it is

                Adjusting records to match reality.

                Examples
                Payment correction
                Failed M-Pesa retry
                Ledger adjustment
                Audit fixes
                State change
                Recorded ≠ Actual → Corrected state
                Key idea

                This is the truth-restoration layer of money systems

                🔥 FINAL SIMPLIFIED MODEL (MOST IMPORTANT)

                All money flows reduce to 4 core primitives:

                1. Inflow

                Money enters system

                2. Outflow

                Money leaves system

                3. Transfer

                Money moves inside system

                4. Transformation

                Money changes form (loan → repayment, contribution → investment, etc.)


                the rest is combination
                