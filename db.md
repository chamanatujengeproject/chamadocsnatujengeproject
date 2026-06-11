but i have divided it into packages.. cause they are alot.. used postgre
{package1
USERS
 |
 +---- MEMBERS
 |
 +---- CHAMAS(created_by)

MEMBERS
 |
 +---- CHAMA_MEMBERS
 |
 +---- MEMBER_ROLES
 |
 +---- MEMBER_BENEFICIARIES
 |
 +---- MEMBER_EXIT_REQUESTS

CHAMAS
 |
 +---- CHAMA_MEMBERS
 |
 +---- MEMBER_ROLES
 |
 +---- MEMBER_INVITATIONS
 |
 +---- CYCLES

CONSTITUTIONS
 |
 +---- CONSTITUTION_VERSIONS
 |
 +---- CHAMAS}

{   
package2

FINANCE
├── contributions
├── transactions
├── transaction_reversals
├── fines
├── audit_logs

ROSCA
├── payout_schedules
├── position_swaps
├── shortfall_records

LOANS
├── loans
├── repayment_schedules
├── repayments
├── guarantors
├── loan_approvals
├── loan_penalties}

{
package3


HYBRID ENGINE
├── wallets
├── wallet_allocations
├── split_transactions
├── wallet_transfers

GOVERNANCE ENGINEERING 
├── motions
├── votes
├── vote_conflicts
├── motion_executions

WELFARE ENGINE
├── welfare_event_types
├── welfare_applications
├── welfare_disbursements

}


{
Package4

NOTIFICATIONS ENGINE
├── notifications
├── notification_templates

PAYMENTS ENGINE
├── payment_requests
├── payment_callbacks
├── payment_reconciliations

DOCUMENTS ENGINE
├── attachments

DIVIDENDS ENGINE
├── dividend_runs
├── dividend_distributions

SYSTEM CONFIG
├── configurations

AUTOMATION (TRIGGERS READY SUPPORT)

}







{
posteGre

1{




-- =====================================================
-- FUNDLOOP CORE FOUNDATION SCHEMA
-- Package 1
-- =====================================================

CREATE EXTENSION IF NOT EXISTS "pgcrypto";

-- =====================================================
-- ENUMS
-- =====================================================

CREATE TYPE member_status_enum AS ENUM (
    'invited',
    'active',
    'suspended',
    'defaulted',
    'exited'
);

CREATE TYPE chama_status_enum AS ENUM (
    'draft',
    'active',
    'suspended',
    'closed'
);

CREATE TYPE chama_type_enum AS ENUM (
    'rosca',
    'table_banking',
    'hybrid'
);

CREATE TYPE invitation_status_enum AS ENUM (
    'pending',
    'accepted',
    'declined',
    'expired'
);

CREATE TYPE exit_request_status_enum AS ENUM (
    'pending',
    'approved',
    'rejected',
    'paid'
);

-- =====================================================
-- USERS
-- =====================================================

CREATE TABLE users (
    user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    username VARCHAR(100) NOT NULL UNIQUE,

    email VARCHAR(255) NOT NULL UNIQUE,

    phone VARCHAR(30) UNIQUE,

    password_hash VARCHAR(500) NOT NULL,

    is_active BOOLEAN DEFAULT TRUE,

    last_login TIMESTAMP,

    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,

    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email
ON users(email);

CREATE INDEX idx_users_phone
ON users(phone);

-- =====================================================
-- ROLES
-- =====================================================

CREATE TABLE roles (
    role_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    role_name VARCHAR(100) NOT NULL UNIQUE,

    description TEXT,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- =====================================================
-- PERMISSIONS
-- =====================================================

CREATE TABLE permissions (
    permission_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    permission_code VARCHAR(150) UNIQUE NOT NULL,

    description TEXT,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- =====================================================
-- ROLE PERMISSIONS
-- =====================================================

CREATE TABLE role_permissions (

    role_permission_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    role_id UUID NOT NULL,

    permission_id UUID NOT NULL,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_rp_role
        FOREIGN KEY (role_id)
        REFERENCES roles(role_id),

    CONSTRAINT fk_rp_permission
        FOREIGN KEY (permission_id)
        REFERENCES permissions(permission_id),

    CONSTRAINT uq_role_permission
        UNIQUE(role_id, permission_id)
);

-- =====================================================
-- CONSTITUTIONS
-- =====================================================

CREATE TABLE constitutions (

    constitution_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    constitution_name VARCHAR(255) NOT NULL,

    description TEXT,

    active_version_id UUID,

    created_by UUID,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- =====================================================
-- CONSTITUTION VERSIONS
-- =====================================================

CREATE TABLE constitution_versions (

    version_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    constitution_id UUID NOT NULL,

    version_number INTEGER NOT NULL,

    content JSONB NOT NULL,

    effective_date DATE NOT NULL,

    approved_by UUID,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_cv_constitution
        FOREIGN KEY (constitution_id)
        REFERENCES constitutions(constitution_id),

    CONSTRAINT uq_constitution_version
        UNIQUE(constitution_id, version_number)
);

-- =====================================================
-- CHAMAS
-- =====================================================

CREATE TABLE chamas (

    chama_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    name VARCHAR(255) NOT NULL,

    type chama_type_enum NOT NULL,

    constitution_id UUID,

    created_by UUID NOT NULL,

    status chama_status_enum
        DEFAULT 'active',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_chama_constitution
        FOREIGN KEY (constitution_id)
        REFERENCES constitutions(constitution_id),

    CONSTRAINT fk_chama_creator
        FOREIGN KEY (created_by)
        REFERENCES users(user_id)
);

CREATE INDEX idx_chama_type
ON chamas(type);

CREATE INDEX idx_chama_status
ON chamas(status);

-- =====================================================
-- MEMBERS
-- =====================================================

CREATE TABLE members (

    member_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    user_id UUID NOT NULL,

    first_name VARCHAR(100) NOT NULL,

    last_name VARCHAR(100),

    national_id VARCHAR(50),

    phone VARCHAR(30) NOT NULL,

    email VARCHAR(255),

    joining_date DATE,

    status member_status_enum
        DEFAULT 'active',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_member_user
        FOREIGN KEY (user_id)
        REFERENCES users(user_id)
);

CREATE INDEX idx_member_user
ON members(user_id);

CREATE INDEX idx_member_status
ON members(status);

CREATE UNIQUE INDEX uq_member_national_id
ON members(national_id);

-- =====================================================
-- CHAMA MEMBERS
-- =====================================================

CREATE TABLE chama_members (

    chama_member_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    member_id UUID NOT NULL,

    joined_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    is_active BOOLEAN DEFAULT TRUE,

    CONSTRAINT fk_cm_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_cm_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT uq_chama_member
        UNIQUE(chama_id, member_id)
);

-- =====================================================
-- MEMBER ROLES
-- =====================================================

CREATE TABLE member_roles (

    member_role_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,

    chama_id UUID NOT NULL,

    role_id UUID NOT NULL,

    assigned_by UUID,

    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_mr_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_mr_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_mr_role
        FOREIGN KEY (role_id)
        REFERENCES roles(role_id),

    CONSTRAINT fk_mr_assigned_by
        FOREIGN KEY (assigned_by)
        REFERENCES members(member_id),

    CONSTRAINT uq_member_chama_role
        UNIQUE(member_id, chama_id, role_id)
);

-- =====================================================
-- MEMBER INVITATIONS
-- =====================================================

CREATE TABLE member_invitations (

    invitation_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    invited_by UUID NOT NULL,

    invitee_name VARCHAR(255),

    invitee_phone VARCHAR(30),

    invitee_email VARCHAR(255),

    invitation_token VARCHAR(255)
        NOT NULL UNIQUE,

    status invitation_status_enum
        DEFAULT 'pending',

    expires_at TIMESTAMP,

    accepted_at TIMESTAMP,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_invitation_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_invitation_sender
        FOREIGN KEY (invited_by)
        REFERENCES members(member_id)
);

-- =====================================================
-- MEMBER BENEFICIARIES
-- =====================================================

CREATE TABLE member_beneficiaries (

    beneficiary_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,

    full_name VARCHAR(255) NOT NULL,

    relationship VARCHAR(100),

    phone VARCHAR(30),

    percentage_share NUMERIC(5,2),

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_beneficiary_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id)
);

-- =====================================================
-- MEMBER EXIT REQUESTS
-- =====================================================

CREATE TABLE member_exit_requests (

    exit_request_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,

    chama_id UUID NOT NULL,

    reason TEXT,

    requested_at TIMESTAMP
        DEFAULT CURRENT_TIMESTAMP,

    approved_by UUID,

    approved_at TIMESTAMP,

    settlement_amount NUMERIC(18,2),

    status exit_request_status_enum
        DEFAULT 'pending',

    CONSTRAINT fk_exit_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_exit_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_exit_approver
        FOREIGN KEY (approved_by)
        REFERENCES members(member_id)
);

-- =====================================================
-- CYCLES
-- =====================================================

CREATE TABLE cycles (

    cycle_id UUID PRIMARY KEY
        DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    cycle_name VARCHAR(100),

    cycle_type VARCHAR(50),

    start_date DATE NOT NULL,

    end_date DATE NOT NULL,

    status VARCHAR(30) NOT NULL,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_cycle_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id)
);

CREATE INDEX idx_cycle_chama
ON cycles(chama_id);

CREATE INDEX idx_cycle_dates
ON cycles(start_date, end_date);

-- =====================================================
-- CONSTITUTION ACTIVE VERSION FK
-- =====================================================

ALTER TABLE constitutions
ADD CONSTRAINT fk_constitution_active_version
FOREIGN KEY (active_version_id)
REFERENCES constitution_versions(version_id);}











2{


-- =====================================================
-- FUNDLOOP FINANCIAL ENGINE SCHEMA
-- Package 2
-- =====================================================

-- Requires pgcrypto from Package 1
-- =====================================================

-- =====================================================
-- ENUMS (FINANCE)
-- =====================================================

CREATE TYPE transaction_type_enum AS ENUM (
    'contribution',
    'loan_disbursement',
    'loan_repayment',
    'fine_payment',
    'welfare_disbursement',
    'wallet_transfer',
    'refund',
    'adjustment'
);

CREATE TYPE contribution_status_enum AS ENUM (
    'pending',
    'partial',
    'complete',
    'overdue'
);

CREATE TYPE fine_status_enum AS ENUM (
    'assessed',
    'paid',
    'waived'
);

CREATE TYPE audit_event_type_enum AS ENUM (
    'payment_received',
    'payout_released',
    'loan_approved',
    'loan_defaulted',
    'fine_assessed',
    'member_exit',
    'vote_cast',
    'wallet_updated',
    'cycle_closed'
);

-- =====================================================
-- CONTRIBUTIONS
-- =====================================================

CREATE TABLE contributions (

    contribution_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,
    chama_id UUID NOT NULL,
    cycle_id UUID NOT NULL,

    amount_expected NUMERIC(18,2) NOT NULL,
    amount_paid NUMERIC(18,2) DEFAULT 0,

    status contribution_status_enum DEFAULT 'pending',

    paid_at TIMESTAMP,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_contribution_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_contribution_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_contribution_cycle
        FOREIGN KEY (cycle_id)
        REFERENCES cycles(cycle_id),

    CONSTRAINT uq_contribution_cycle_member
        UNIQUE(member_id, chama_id, cycle_id)
);

CREATE INDEX idx_contribution_cycle
ON contributions(cycle_id);

CREATE INDEX idx_contribution_status
ON contributions(status);

-- =====================================================
-- TRANSACTIONS (IMMUTABLE LEDGER)
-- =====================================================

CREATE TABLE transactions (

    txn_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,
    member_id UUID,

    type transaction_type_enum NOT NULL,

    amount NUMERIC(18,2) NOT NULL,

    channel VARCHAR(50),

    reference VARCHAR(255) UNIQUE,

    idempotency_key VARCHAR(255) UNIQUE,

    metadata JSONB,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_txn_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_txn_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id)
);

CREATE INDEX idx_txn_chama
ON transactions(chama_id);

CREATE INDEX idx_txn_type
ON transactions(type);

-- =====================================================
-- TRANSACTION REVERSALS (IMMUTABILITY SUPPORT)
-- =====================================================

CREATE TABLE transaction_reversals (

    reversal_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    original_txn_id UUID NOT NULL,

    reversed_by UUID,

    reason TEXT NOT NULL,

    reversed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_reversal_txn
        FOREIGN KEY (original_txn_id)
        REFERENCES transactions(txn_id),

    CONSTRAINT fk_reversal_member
        FOREIGN KEY (reversed_by)
        REFERENCES members(member_id)
);

-- =====================================================
-- FINES
-- =====================================================

CREATE TABLE fines (

    fine_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,
    chama_id UUID NOT NULL,

    trigger_event VARCHAR(255),

    amount NUMERIC(18,2) NOT NULL,

    status fine_status_enum DEFAULT 'assessed',

    waived_by UUID,

    waiver_reason TEXT,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_fine_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_fine_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_fine_waived_by
        FOREIGN KEY (waived_by)
        REFERENCES members(member_id)
);

CREATE INDEX idx_fine_member
ON fines(member_id);

CREATE INDEX idx_fine_status
ON fines(status);

-- =====================================================
-- AUDIT LOG (IMMUTABLE SYSTEM EVENTS)
-- =====================================================

CREATE TABLE audit_logs (

    log_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID,

    actor_id UUID,

    event_type audit_event_type_enum NOT NULL,

    entity_id UUID,

    before_state JSONB,

    after_state JSONB,

    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_audit_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_audit_actor
        FOREIGN KEY (actor_id)
        REFERENCES members(member_id)
);

CREATE INDEX idx_audit_chama
ON audit_logs(chama_id);

CREATE INDEX idx_audit_event
ON audit_logs(event_type);

-- =====================================================
-- ROSCA: PAYOUT SCHEDULE
-- =====================================================

CREATE TABLE payout_schedules (

    schedule_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,
    member_id UUID NOT NULL,

    cycle_number INT NOT NULL,

    position INT NOT NULL,

    payout_date DATE NOT NULL,

    status VARCHAR(30) DEFAULT 'upcoming',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_payout_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_payout_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id)
);

CREATE INDEX idx_payout_chama_cycle
ON payout_schedules(chama_id, cycle_number);

-- =====================================================
-- POSITION SWAPS (ROSCA EDGE CASE)
-- =====================================================

CREATE TABLE position_swaps (

    swap_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    requester_id UUID NOT NULL,
    target_id UUID NOT NULL,

    approved_by UUID,

    reason TEXT,

    status VARCHAR(30) DEFAULT 'pending',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_swap_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_swap_requester
        FOREIGN KEY (requester_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_swap_target
        FOREIGN KEY (target_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_swap_approver
        FOREIGN KEY (approved_by)
        REFERENCES members(member_id)
);

-- =====================================================
-- SHORTFALL RECORDS (ROSCA DEFAULT HANDLING)
-- =====================================================

CREATE TABLE shortfall_records (

    shortfall_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    member_id UUID NOT NULL,

    amount NUMERIC(18,2) NOT NULL,

    recovery_status VARCHAR(50) DEFAULT 'pending',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_shortfall_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_shortfall_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id)
);

-- =====================================================
-- LOANS
-- =====================================================

CREATE TABLE loans (

    loan_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,
    chama_id UUID NOT NULL,

    principal NUMERIC(18,2) NOT NULL,

    interest_rate NUMERIC(5,2),

    interest_model VARCHAR(50),

    duration_months INT,

    status VARCHAR(30) DEFAULT 'applied',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_loan_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_loan_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id)
);

CREATE INDEX idx_loan_member
ON loans(member_id);

-- =====================================================
-- REPAYMENT SCHEDULES
-- =====================================================

CREATE TABLE repayment_schedules (

    schedule_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    loan_id UUID NOT NULL,

    instalment_no INT NOT NULL,

    amount_due NUMERIC(18,2),

    due_date DATE,

    status VARCHAR(30) DEFAULT 'upcoming',

    CONSTRAINT fk_rs_loan
        FOREIGN KEY (loan_id)
        REFERENCES loans(loan_id)
);

-- =====================================================
-- REPAYMENTS
-- =====================================================

CREATE TABLE repayments (

    repayment_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    loan_id UUID NOT NULL,

    schedule_id UUID,

    amount_paid NUMERIC(18,2),

    paid_at TIMESTAMP,

    status VARCHAR(30) DEFAULT 'pending',

    CONSTRAINT fk_repayment_loan
        FOREIGN KEY (loan_id)
        REFERENCES loans(loan_id),

    CONSTRAINT fk_repayment_schedule
        FOREIGN KEY (schedule_id)
        REFERENCES repayment_schedules(schedule_id)
);

-- =====================================================
-- GUARANTORS
-- =====================================================

CREATE TABLE guarantors (

    guarantor_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    loan_id UUID NOT NULL,

    member_id UUID NOT NULL,

    liability_amount NUMERIC(18,2),

    status VARCHAR(30) DEFAULT 'invited',

    accepted_at TIMESTAMP,

    CONSTRAINT fk_guarantor_loan
        FOREIGN KEY (loan_id)
        REFERENCES loans(loan_id),

    CONSTRAINT fk_guarantor_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT uq_loan_guarantor
        UNIQUE(loan_id, member_id)
);

-- =====================================================
-- LOAN APPROVALS
-- =====================================================

CREATE TABLE loan_approvals (

    approval_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    loan_id UUID NOT NULL,

    approved_by UUID NOT NULL,

    vote_id UUID,

    decision VARCHAR(30),

    reason TEXT,

    decided_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_loan_approval_loan
        FOREIGN KEY (loan_id)
        REFERENCES loans(loan_id),

    CONSTRAINT fk_loan_approval_member
        FOREIGN KEY (approved_by)
        REFERENCES members(member_id)
);

-- =====================================================
-- LOAN PENALTIES
-- =====================================================

CREATE TABLE loan_penalties (

    penalty_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    loan_id UUID NOT NULL,

    repayment_id UUID,

    amount NUMERIC(18,2),

    assessed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    status VARCHAR(30) DEFAULT 'active',

    CONSTRAINT fk_penalty_loan
        FOREIGN KEY (loan_id)
        REFERENCES loans(loan_id),

    CONSTRAINT fk_penalty_repayment
        FOREIGN KEY (repayment_id)
        REFERENCES repayments(repayment_id)
);
}













3. {


-- =====================================================
-- FUNDLOOP OPERATIONS ENGINE SCHEMA
-- Package 3
-- =====================================================

-- Requires Package 1 + Package 2
-- =====================================================

-- =====================================================
-- ENUMS
-- =====================================================

CREATE TYPE wallet_type_enum AS ENUM (
    'savings',
    'loan_pool',
    'welfare',
    'investment',
    'fines',
    'admin'
);

CREATE TYPE motion_type_enum AS ENUM (
    'wallet_reallocation',
    'investment',
    'member_removal',
    'constitution_change',
    'loan_approval_rule',
    'dividend_distribution'
);

CREATE TYPE vote_enum AS ENUM (
    'yes',
    'no',
    'abstain'
);

CREATE TYPE motion_status_enum AS ENUM (
    'open',
    'passed',
    'rejected',
    'executed',
    'archived'
);

CREATE TYPE transfer_status_enum AS ENUM (
    'pending',
    'approved',
    'rejected',
    'completed'
);

CREATE TYPE welfare_status_enum AS ENUM (
    'pending',
    'approved',
    'rejected',
    'disbursed'
);

-- =====================================================
-- WALLETS
-- =====================================================

CREATE TABLE wallets (

    wallet_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    wallet_type wallet_type_enum NOT NULL,

    balance NUMERIC(18,2) DEFAULT 0,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_wallet_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT uq_wallet_type_per_chama
        UNIQUE(chama_id, wallet_type)
);

CREATE INDEX idx_wallet_chama
ON wallets(chama_id);

-- =====================================================
-- WALLET ALLOCATIONS
-- =====================================================

CREATE TABLE wallet_allocations (

    allocation_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    wallet_id UUID NOT NULL,

    percentage NUMERIC(5,2) NOT NULL,

    effective_from DATE NOT NULL,

    created_by UUID,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_allocation_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_allocation_wallet
        FOREIGN KEY (wallet_id)
        REFERENCES wallets(wallet_id),

    CONSTRAINT fk_allocation_creator
        FOREIGN KEY (created_by)
        REFERENCES members(member_id),

    CONSTRAINT uq_wallet_allocation
        UNIQUE(chama_id, wallet_id, effective_from)
);

-- =====================================================
-- SPLIT TRANSACTIONS
-- =====================================================

CREATE TABLE split_transactions (

    split_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    parent_txn_id UUID NOT NULL,

    wallet_id UUID NOT NULL,

    amount NUMERIC(18,2) NOT NULL,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_split_txn
        FOREIGN KEY (parent_txn_id)
        REFERENCES transactions(txn_id),

    CONSTRAINT fk_split_wallet
        FOREIGN KEY (wallet_id)
        REFERENCES wallets(wallet_id)
);

CREATE INDEX idx_split_wallet
ON split_transactions(wallet_id);

-- =====================================================
-- WALLET TRANSFERS
-- =====================================================

CREATE TABLE wallet_transfers (

    transfer_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    from_wallet_id UUID NOT NULL,

    to_wallet_id UUID NOT NULL,

    amount NUMERIC(18,2) NOT NULL,

    reason TEXT,

    approved_by UUID,

    vote_id UUID,

    status transfer_status_enum DEFAULT 'pending',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_transfer_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_transfer_from_wallet
        FOREIGN KEY (from_wallet_id)
        REFERENCES wallets(wallet_id),

    CONSTRAINT fk_transfer_to_wallet
        FOREIGN KEY (to_wallet_id)
        REFERENCES wallets(wallet_id),

    CONSTRAINT fk_transfer_approver
        FOREIGN KEY (approved_by)
        REFERENCES members(member_id)
);

-- =====================================================
-- MOTIONS (GOVERNANCE CORE)
-- =====================================================

CREATE TABLE motions (

    motion_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    type motion_type_enum NOT NULL,

    description TEXT NOT NULL,

    proposed_by UUID NOT NULL,

    quorum_required NUMERIC(5,2) DEFAULT 60.00,

    status motion_status_enum DEFAULT 'open',

    result VARCHAR(30),

    closed_at TIMESTAMP,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_motion_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_motion_proposer
        FOREIGN KEY (proposed_by)
        REFERENCES members(member_id)
);

CREATE INDEX idx_motion_chama
ON motions(chama_id);

-- =====================================================
-- VOTES
-- =====================================================

CREATE TABLE votes (

    vote_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    motion_id UUID NOT NULL,

    member_id UUID NOT NULL,

    vote vote_enum NOT NULL,

    voted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_vote_motion
        FOREIGN KEY (motion_id)
        REFERENCES motions(motion_id),

    CONSTRAINT fk_vote_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT uq_vote_unique
        UNIQUE(motion_id, member_id)
);

CREATE INDEX idx_vote_motion
ON votes(motion_id);

-- =====================================================
-- VOTE CONFLICTS (ETHICS CONTROL)
-- =====================================================

CREATE TABLE vote_conflicts (

    conflict_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    motion_id UUID NOT NULL,

    member_id UUID NOT NULL,

    reason TEXT NOT NULL,

    flagged_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_conflict_motion
        FOREIGN KEY (motion_id)
        REFERENCES motions(motion_id),

    CONSTRAINT fk_conflict_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id)
);

-- =====================================================
-- MOTION EXECUTIONS
-- =====================================================

CREATE TABLE motion_executions (

    execution_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    motion_id UUID NOT NULL,

    executed_by UUID,

    execution_result TEXT,

    executed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_execution_motion
        FOREIGN KEY (motion_id)
        REFERENCES motions(motion_id),

    CONSTRAINT fk_execution_member
        FOREIGN KEY (executed_by)
        REFERENCES members(member_id)
);

-- =====================================================
-- WELFARE EVENT TYPES
-- =====================================================

CREATE TABLE welfare_event_types (

    event_type_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    name VARCHAR(100) NOT NULL,

    max_amount NUMERIC(18,2),

    is_active BOOLEAN DEFAULT TRUE,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_event_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT uq_event_type
        UNIQUE(chama_id, name)
);

-- =====================================================
-- WELFARE APPLICATIONS
-- =====================================================

CREATE TABLE welfare_applications (

    application_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,

    chama_id UUID NOT NULL,

    event_type_id UUID NOT NULL,

    description TEXT,

    requested_amount NUMERIC(18,2),

    status welfare_status_enum DEFAULT 'pending',

    approved_by UUID,

    requested_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    decided_at TIMESTAMP,

    CONSTRAINT fk_welfare_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_welfare_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_welfare_event
        FOREIGN KEY (event_type_id)
        REFERENCES welfare_event_types(event_type_id),

    CONSTRAINT fk_welfare_approver
        FOREIGN KEY (approved_by)
        REFERENCES members(member_id)
);

CREATE INDEX idx_welfare_member
ON welfare_applications(member_id);

-- =====================================================
-- WELFARE DISBURSEMENTS
-- =====================================================

CREATE TABLE welfare_disbursements (

    disbursement_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    application_id UUID NOT NULL,

    amount NUMERIC(18,2) NOT NULL,

    paid_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    transaction_ref VARCHAR(255),

    CONSTRAINT fk_welfare_application
        FOREIGN KEY (application_id)
        REFERENCES welfare_applications(application_id)
);



}

















4. {


-- =====================================================
-- FUNDLOOP SYSTEM INFRASTRUCTURE SCHEMA
-- Package 4 (FINAL LAYER)
-- =====================================================

-- Requires Package 1, 2, 3
-- =====================================================

-- =====================================================
-- ENUMS
-- =====================================================

CREATE TYPE notification_channel_enum AS ENUM (
    'sms',
    'push',
    'email'
);

CREATE TYPE notification_status_enum AS ENUM (
    'pending',
    'sent',
    'failed',
    'read'
);

CREATE TYPE payment_status_enum AS ENUM (
    'initiated',
    'pending',
    'successful',
    'failed',
    'reversed'
);

CREATE TYPE dividend_status_enum AS ENUM (
    'calculated',
    'distributed',
    'failed'
);

-- =====================================================
-- NOTIFICATION TEMPLATES
-- =====================================================

CREATE TABLE notification_templates (

    template_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    name VARCHAR(150) NOT NULL UNIQUE,

    subject VARCHAR(255),

    body TEXT NOT NULL,

    channel notification_channel_enum NOT NULL,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- =====================================================
-- NOTIFICATIONS
-- =====================================================

CREATE TABLE notifications (

    notification_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID,

    member_id UUID,

    template_id UUID,

    title VARCHAR(255),

    message TEXT NOT NULL,

    channel notification_channel_enum NOT NULL,

    status notification_status_enum DEFAULT 'pending',

    sent_at TIMESTAMP,

    read_at TIMESTAMP,

    metadata JSONB,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_notification_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id),

    CONSTRAINT fk_notification_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_notification_template
        FOREIGN KEY (template_id)
        REFERENCES notification_templates(template_id)
);

CREATE INDEX idx_notification_member
ON notifications(member_id);

CREATE INDEX idx_notification_status
ON notifications(status);

-- =====================================================
-- PAYMENT REQUESTS (INITIATION LAYER)
-- =====================================================

CREATE TABLE payment_requests (

    request_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    member_id UUID NOT NULL,
    chama_id UUID NOT NULL,

    amount NUMERIC(18,2) NOT NULL,

    purpose VARCHAR(100),

    provider VARCHAR(50) DEFAULT 'mpesa',

    external_reference VARCHAR(255),

    idempotency_key VARCHAR(255) UNIQUE,

    status payment_status_enum DEFAULT 'initiated',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_payment_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id),

    CONSTRAINT fk_payment_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id)
);

CREATE INDEX idx_payment_member
ON payment_requests(member_id);

-- =====================================================
-- PAYMENT CALLBACKS (GATEWAY RESPONSE)
-- =====================================================

CREATE TABLE payment_callbacks (

    callback_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    request_id UUID NOT NULL,

    provider VARCHAR(50),

    payload JSONB NOT NULL,

    status VARCHAR(50),

    received_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_callback_request
        FOREIGN KEY (request_id)
        REFERENCES payment_requests(request_id)
);

-- =====================================================
-- PAYMENT RECONCILIATIONS
-- =====================================================

CREATE TABLE payment_reconciliations (

    reconciliation_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    request_id UUID NOT NULL,

    transaction_id UUID,

    matched BOOLEAN DEFAULT FALSE,

    discrepancy_amount NUMERIC(18,2),

    reconciled_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_recon_request
        FOREIGN KEY (request_id)
        REFERENCES payment_requests(request_id)
);

-- =====================================================
-- ATTACHMENTS (DOCUMENT STORAGE METADATA)
-- =====================================================

CREATE TABLE attachments (

    attachment_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    entity_type VARCHAR(100) NOT NULL,

    entity_id UUID NOT NULL,

    file_name VARCHAR(255),

    file_url TEXT NOT NULL,

    file_type VARCHAR(50),

    uploaded_by UUID,

    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_attachment_uploader
        FOREIGN KEY (uploaded_by)
        REFERENCES members(member_id)
);

CREATE INDEX idx_attachment_entity
ON attachments(entity_type, entity_id);

-- =====================================================
-- DIVIDEND RUNS
-- =====================================================

CREATE TABLE dividend_runs (

    run_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID NOT NULL,

    total_amount NUMERIC(18,2),

    status dividend_status_enum DEFAULT 'calculated',

    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    distributed_at TIMESTAMP,

    CONSTRAINT fk_dividend_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id)
);

-- =====================================================
-- DIVIDEND DISTRIBUTIONS
-- =====================================================

CREATE TABLE dividend_distributions (

    distribution_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    run_id UUID NOT NULL,

    member_id UUID NOT NULL,

    amount NUMERIC(18,2),

    paid BOOLEAN DEFAULT FALSE,

    paid_at TIMESTAMP,

    CONSTRAINT fk_div_distribution_run
        FOREIGN KEY (run_id)
        REFERENCES dividend_runs(run_id),

    CONSTRAINT fk_div_distribution_member
        FOREIGN KEY (member_id)
        REFERENCES members(member_id)
);

CREATE INDEX idx_dividend_member
ON dividend_distributions(member_id);

-- =====================================================
-- SYSTEM CONFIGURATION
-- =====================================================

CREATE TABLE configurations (

    config_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    chama_id UUID,

    key VARCHAR(150) NOT NULL,

    value JSONB NOT NULL,

    description TEXT,

    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT uq_config
        UNIQUE(chama_id, key),

    CONSTRAINT fk_config_chama
        FOREIGN KEY (chama_id)
        REFERENCES chamas(chama_id)
);

-- =====================================================
-- AUDIT SUPPORT INDEX (PERFORMANCE HARDENING)
-- =====================================================

CREATE INDEX idx_transactions_reference
ON transactions(reference);

CREATE INDEX idx_loans_status
ON loans(status);

CREATE INDEX idx_contributions_member_cycle
ON contributions(member_id, cycle_id);

-- =====================================================
-- IDENTITY & IDEMPOTENCY SAFETY RULES (DOCUMENTED)
-- =====================================================

-- NOTE:
-- These constraints enforce system-level guarantees:
-- 1. payment_requests.idempotency_key prevents duplicate payments
-- 2. transactions.reference ensures immutable ledger traceability
-- 3. notifications.status ensures delivery tracking integrity

-- =====================================================
-- END OF PACKAGE 4
-- =====================================================


}that all nime comeup with

}