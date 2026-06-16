

# What a business rules document contains

Usually:

```text id="2k5y2y"
1. Membership Rules
2. ROSCA Rules
3. Welfare Rules
4. Finance Rules
5. Governance Rules
6. Validation Rules
7. Security/Authorization Rules
8. Exception Rules
```

---

# 1. Membership Rules

Rules about joining and participating.

Example:

### BR-MEM-001: Membership Required

Rule:

```text id="3z9jri"
Only registered members of a chama
can contribute, vote, or receive payouts.
```

Reason:

Prevents outsiders.

---

### BR-MEM-002: Single Membership Record

Rule:

```text id="gs6n8m"
A member cannot join the same chama twice.
```

Constraint:

```text id="3pzyw8"
(memberId, chamaId)
must be unique
```

---

### BR-MEM-003: Role Permissions

Rule:

```text id="m5tduz"
Only Treasurer can approve
financial disbursements.
```

Example:

```text id="0jk6ml"
Member ❌
Chairman ❌
Treasurer ✅
```

---

### BR-MEM-004: Inactive Members

Rule:

```text id="0z9c7l"
A suspended member cannot:
- contribute
- vote
- receive payout
```

---

# 2. ROSCA Rules

The heart of merry-go-round logic.

---

### BR-ROSCA-001: Fixed Contribution

Rule:

```text id="dx6u5m"
Every member contributes the same
amount per cycle.
```

Example:

```text id="7rvz7r"
KES 1,000 each
```

Prevents:

```text id="bjlwmz"
unfair payout distribution
```

---

### BR-ROSCA-002: Full Contribution Before Payout

Rule:

```text id="4kmy0r"
A payout can only occur
after required contributions
are collected.
```

Example:

10 members:

```text id="n1u8e4"
10 × 1000 = 10,000
```

Then:

```text id="my7jgo"
release payout
```

---

### BR-ROSCA-003: Rotation Order

Rule:

```text id="k8e8z5"
Members receive payout
according to assigned rotation order.
```

Methods:

```text id="h1s7cw"
FIXED_ORDER
LOTTERY
```

---

### BR-ROSCA-004: Missed Contribution Penalty

Rule:

```text id="w10zku"
A member who misses contribution
is skipped for payout eligibility.
```

Penalty:

```text id="mn8m3y"
fine
double contribution
loss of turn
```

---

### BR-ROSCA-005: One Payout Per Cycle

Rule:

```text id="k9ydbu"
Only one payout
may exist per cycle.
```

Constraint:

```text id="4e4v5e"
unique(cycleId)
```

Prevents:

```text id="rjv1x2"
double payouts
```

---

### BR-ROSCA-006: Position Swap Approval

Rule:

```text id="gcjmls"
Members may swap payout positions
only after approval.
```

Approval:

```text id="u85rlk"
2/3 majority vote
```

---

# 3. Welfare Rules

Emergency assistance logic.

---

### BR-WEL-001: Valid Emergency Types

Rule:

```text id="z0m6qk"
Only approved emergencies
qualify for welfare support.
```

Allowed:

```text id="7j0fr0"
medical
funeral
disaster
```

Not allowed:

```text id="2if4jt"
vacation
business investment
shopping
```

---

### BR-WEL-002: Active Membership Required

Rule:

```text id="8we83x"
Only active members
can submit claims.
```

---

### BR-WEL-003: Maximum Claim Amount

Rule:

```text id="d2f06d"
Claim amount cannot exceed
30% of welfare pool balance.
```

Prevents:

```text id="6wqzlh"
bankrupting welfare fund
```

---

### BR-WEL-004: Approval Threshold

Rule:

```text id="ry5wkc"
Claim requires
70% approval vote.
```

Example:

10 voters:

```text id="3d4i5i"
7 approvals required
```

---

### BR-WEL-005: Claim Evidence

Rule:

```text id="szkr14"
Emergency claim
must include evidence.
```

Example:

```text id="a9dgtg"
hospital bill
death certificate
report
```

---

### BR-WEL-006: One Active Claim

Rule:

```text id="f2s2eg"
A member cannot have
multiple active welfare claims.
```

Prevents fraud.

---

# 4. Finance Rules

Money correctness.

These are critical.

---

### BR-FIN-001: No Negative Wallet

Rule:

```text id="gf0j6m"
Wallet balance
cannot go below zero.
```

---

### BR-FIN-002: Immutable Transactions

Rule:

```text id="5s8l6s"
Completed transactions
cannot be edited.
```

Instead:

```text id="nhgs9v"
reversal transaction
```

---

### BR-FIN-003: Idempotent Payments

Rule:

```text id="s5e7u4"
Duplicate payment requests
must not create duplicate payments.
```

Mechanism:

```text id="vy5knm"
idempotency key
```

---

### BR-FIN-004: Atomic Transfer

Rule:

```text id="m4wh7x"
Debit and credit
must succeed together.
```

Example:

```text id="8ffjv7"
Member wallet
    ↓
Chama wallet
```

Must be atomic.

Never:

```text id="0dqu94"
money removed
but not received
```

---

### BR-FIN-005: Ledger Integrity

Rule:

```text id="6kjd8n"
Every money movement
must have transaction record.
```

Auditability.

---

# 5. Governance Rules

How decisions happen.

---

### BR-GOV-001: Treasurer Authority

Rule:

```text id="1j8j7y"
Only treasurer
can initiate payout.
```

---

### BR-GOV-002: Chairman Oversight

Rule:

```text id="63jzvc"
Chairman can veto
invalid welfare claim.
```

---

### BR-GOV-003: Rule Change Voting

Rule:

```text id="cty0h8"
Rule changes require
majority approval.
```

Example:

```text id="tp4hkg"
50% + 1
```

---

# 6. Validation Rules

Input correctness.

Example:

### BR-VAL-001

Rule:

```text id="b1dbj9"
Contribution amount
must be positive.
```

---

### BR-VAL-002

Rule:

```text id="jlwm4n"
Phone number
must be unique.
```

---

### BR-VAL-003

Rule:

```text id="x9b14d"
Claim amount
must be greater than zero.
```

---

# 7. Authorization Rules

Who can do what.

Example:

```text id="r1gw4u"
Member:
view own data

Treasurer:
manage contributions

Chairman:
approve claims

Admin:
manage chama
```

---

# 8. Exception Rules

What happens in edge cases.

Example:

### Missing contribution

Rule:

```text id="4x5r1e"
If contribution deadline passes:
status → MISSED
penalty applied
```

---

### Empty welfare fund

Rule:

```text id="h3zw4h"
If balance insufficient:
claim status → PENDING_FUNDS
```

---

A 
