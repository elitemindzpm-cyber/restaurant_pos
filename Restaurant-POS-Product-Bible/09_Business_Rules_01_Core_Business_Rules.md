# 09_Business_Rules_01_Core_Business_Rules.md

# Restaurant POS Enterprise Product Bible
## Core Business Rules Repository

**Module ID:** BR-001

---

# 1. Purpose

This document defines the implementation-level business rules governing every functional module of the Restaurant POS platform. These rules are the authoritative reference for Product, Engineering, QA, Support, and Operations.

---

# 2. Rule Classification

| Prefix | Domain |
|---|---|
| ORD | Orders |
| TAB | Tables |
| KOT | Kitchen |
| PAY | Payments |
| INV | Inventory |
| PUR | Procurement |
| ADM | Administration |
| SEC | Security |
| RPT | Reporting |

---

# 3. Order Rules

### ORD-001
Exactly one active order may exist per active table session.

### ORD-002
An order starts in **Draft** and must transition through the approved state machine.

### ORD-003
Paid orders are immutable except through approved refund workflows.

### ORD-004
Deleted orders are soft-deleted and retained for audit.

### ORD-005
Order number must be unique within the enterprise.

### ORD-006
Additional items after KOT create incremental KOTs only.

### ORD-007
Cancelled prepared items require manager approval.

### ORD-008
Taxes are calculated after item discounts and before bill-level rounding.

### ORD-009
Order totals are recalculated after every mutation.

### ORD-010
Every order mutation generates an audit event.

---

# 4. Table Rules

TAB-001 Available tables may be opened.

TAB-002 Occupied tables cannot be reopened.

TAB-003 Table merge preserves original audit trail.

TAB-004 Table transfer moves ownership without changing order history.

TAB-005 Closed tables automatically return to Available after housekeeping completion.

---

# 5. Kitchen Rules

KOT-001 Only confirmed items generate KOT.

KOT-002 Incremental KOT contains only unsent items.

KOT-003 Kitchen cannot edit commercial pricing.

KOT-004 Ready requires Preparing state.

KOT-005 SLA breach generates notification event.

---

# 6. Payment Rules

PAY-001 Sum of tenders must equal bill total.

PAY-002 Duplicate payment requests are prevented using idempotency.

PAY-003 Refund amount cannot exceed original payment.

PAY-004 Complimentary bills require approval.

PAY-005 Receipt generated only after successful payment.

---

# 7. Inventory Rules

INV-001 Inventory ledger is append-only.

INV-002 Negative stock prohibited unless explicitly configured.

INV-003 Batch depletion follows FEFO where enabled.

INV-004 Approved purchase updates inventory.

INV-005 Physical count freezes inventory movement.

---

# 8. Procurement Rules

PUR-001 Supplier must be active.

PUR-002 Purchase Order approval required before GRN.

PUR-003 Duplicate supplier invoice blocked.

PUR-004 Returns reverse inventory movement.

PUR-005 Closed purchase documents are immutable.

---

# 9. Administration Rules

ADM-001 All configuration changes are versioned.

ADM-002 System roles cannot be deleted.

ADM-003 Feature flag rollout is auditable.

ADM-004 Secrets are encrypted at rest.

ADM-005 Configuration rollback restores last published version.

---

# 10. Security Rules

SEC-001 JWT required for protected APIs.

SEC-002 Password history prevents reuse.

SEC-003 Account locks after configured failures.

SEC-004 RBAC evaluated on every request.

SEC-005 All privileged actions are audited.

---

# 11. Reporting Rules

RPT-001 Reports honor branch permissions.

RPT-002 Scheduled reports execute asynchronously.

RPT-003 Export actions are logged.

RPT-004 Dashboard widgets use cached data when available.

---

# 12. Cross-Module Rules

1. Payment completion closes the order.
2. Order closure releases the table.
3. Purchase approval updates inventory only after receipt.
4. Configuration changes never alter historical transactions.
5. Every state transition publishes a domain event.

---

# 13. Decision Table Example

| Condition | Action |
|---|---|
| Discount > Waiter Limit | Manager Approval |
| Stock < Reorder Level | Trigger Reorder |
| Payment Failed | Keep Bill Open |
| Kitchen SLA Breach | Notify Manager |

---

# 14. Exception Handling

- Retry transient failures.
- Reject invalid state transitions.
- Record every exception in audit logs.
- Preserve transactional consistency using rollback.

---

# 15. QA Traceability

Every business rule must map to:
- Functional test case
- Integration test
- UAT scenario
- Audit verification

---

# Next

09_Business_Rules_02_Order_Pricing_Rules.md
