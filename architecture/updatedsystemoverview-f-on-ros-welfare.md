
---

### FundLoop: Simplified System Overview (ROSCA & Welfare Focus)

#### What is FundLoop?
FundLoop is a digital Chama Management System. For this scope, we focus on two specific Kenyan Chama models:

1.  **ROSCA (Merry-Go-Round):** A rotational savings group where members contribute fixed amounts, and one member takes the full "pot" each cycle.
2.  **Welfare Chama:** An emergency support system where members contribute to a fund used for unforeseen events (e.g., funeral, medical emergency).

#### Core Problem We Solve
Manual methods (ledger books, WhatsApp, spreadsheets) for these chamas lead to:
- **Errors** in tracking payouts and contributions.
- **Opaque records** (who gets the next pot? how much welfare is left?).
- **Delayed reporting** and **eroded trust**.

#### Key Actors
- **Member:** Participates in the chama.
- **Chama Treasurer:** Manages contributions and payouts.
- **Chama Chairman:** Oversees governance (e.g., approving welfare claims).
- **Chama Admin:** Manages membership and rules.

---

### Workflows for ROSCA & Welfare Chamas

#### 1. ROSCA Chama Workflow (Rotational Payout Engine)

**Core Flow:**
> Schedule created → Positions assigned → Contributions collected → Payout triggered → Next rotation begins

**Step-by-step:**
1.  **Setup:** Define cycle (e.g., weekly), contribution amount (e.g., 1,000 KES), and rotation order (fixed or lottery).
2.  **Contribution:** All members pay their fixed amount each cycle.
3.  **Payout:** The designated member for that cycle receives the full pot (e.g., 10,000 KES for 10 members).
4.  **Rotation:** The system automatically moves to the next member for the next cycle.
5.  **Handling Missing Contributions:** If a member misses a payment, they are skipped in the current cycle and penalized per rules (e.g., must pay double next time or lose their turn).

**Key Rules (ROSCA-specific):**
- Rotation method (fixed order / random lottery).
- Swapping rules (can members trade positions? requires 2/3 approval).
- Penalty for missed contribution (e.g., skipped in next cycle, fined).

---

#### 2. Welfare Chama Workflow (Safety Net System)

**Core Flow:**
> Member applies → Verification → Approval → Disbursement → Closure

**Step-by-step:**
1.  **Contribution:** Members contribute regularly to a shared "welfare pool."
2.  **Emergency Application:** A member files a claim (e.g., medical bill, funeral expense).
3.  **Verification:** Treasurer or Chairman validates the emergency (e.g., requires document proof).
4.  **Approval:** Requires a vote or chairman approval (e.g., 70% member approval or simple majority).
5.  **Disbursement:** Money is paid out from the welfare pool to the member (no repayment required).
6.  **Closure:** Request is logged as closed.

**Key Rules (Welfare-specific):**
- What qualifies as an emergency (e.g., medical, funeral, disaster only).
- Maximum payout (e.g., 30% of total welfare pool per incident).
- Approval process (e.g., requires 70% member vote or treasurer + chairman sign-off).

---

### Money Flows (Simplified for ROSCA & Welfare)

| Flow Type | ROSCA Chama | Welfare Chama |
| :--- | :--- | :--- |
| **Inflow** | Member contributions (fixed amounts) | Member contributions (regular amounts) |
| **Outflow** | Payout to rotating member | Emergency payout to claimant |
| **Transfer** | Member wallet → ROSCA pool → Member wallet | Member wallet → Welfare pool → Claimant wallet |
| **Risk Flow** | Not applicable (pure savings rotation) | Yes (many members pool risk to cover one member's emergency) |

**Key Insight:**
- **ROSCA** is a **circular redistribution flow** (money goes from many → one → many over time).
- **Welfare** is a **risk coverage flow** (money leaves the system permanently to cover a loss).

---
