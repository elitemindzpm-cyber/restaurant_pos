# 10_Edge_Cases_02_Module_Edge_Cases.md

# Restaurant POS Enterprise Product Bible
## Enterprise Edge Case Repository – Part 2 (Module Scenarios)

**Module ID:** EC-002

---

# Purpose

This document expands module-level edge cases with expected behavior, recovery strategy, logging requirements, and validation guidance.

---

# POS Edge Cases

### POS-006 Order edited after KOT generation
Expected:
- Existing KOT remains immutable.
- Delta KOT generated for new items.
- Removed prepared items require approval.

### POS-007 Table merge while one bill is partially paid
Expected:
- Reject merge.
- Prompt settlement before merge.

### POS-008 Split bill after receipt printed
Expected:
- Manager approval required.
- Original receipt voided and audited.

### POS-009 Waiter session expires while saving order
Expected:
- Preserve draft locally.
- Force re-authentication.

### POS-010 Duplicate order submission from double-click
Expected:
- Idempotency key prevents duplicate orders.

---

# Kitchen Edge Cases

### KIT-004 Station printer offline
- Queue print jobs.
- Continue KDS workflow.

### KIT-005 Course sequencing conflict
- Hold next course until dependency completes.

### KIT-006 Chef marks Ready before Accept
- Reject invalid state transition.

### KIT-007 KDS reconnect after outage
- Replay pending events in order.

### KIT-008 Kitchen reassignment
- Supervisor transfer only.
- Audit ownership change.

---

# Inventory Edge Cases

### INV-004 Simultaneous transfers of same stock
- Optimistic locking.
- Recalculate availability.

### INV-005 Stock count started during transfer
- Freeze affected SKUs.
- Delay transfer completion.

### INV-006 Barcode duplicated
- Reject duplicate mapping.

### INV-007 FEFO batch depleted mid-pick
- Reallocate next eligible batch.

### INV-008 Valuation recalculation interrupted
- Roll back transaction.
- Retry background job.

---

# Procurement Edge Cases

### PUR-003 Supplier credit limit exceeded
- Block approval or require override.

### PUR-004 GRN without PO
- Allow only if policy enabled.

### PUR-005 Invoice currency mismatch
- Validate exchange rate policy.

### PUR-006 Supplier becomes inactive during approval
- Suspend workflow pending review.

### PUR-007 Landed cost posted twice
- Detect duplicate allocation.

---

# Payment Edge Cases

### PAY-006 Card approved, callback delayed
- Poll gateway before retry.

### PAY-007 UPI callback arrives after manual retry
- Reconcile by transaction reference.

### PAY-008 Receipt printer failure
- Mark receipt pending reprint.

### PAY-009 Shift closes during payment
- Complete transaction before closure.

### PAY-010 Refund requested for split tender
- Allocate refund proportionally.

---

# Multi-Branch Edge Cases

- Branch offline during sync.
- Duplicate order numbers across branches.
- Cross-branch inventory transfer conflict.
- Timezone mismatch in reports.
- Branch disabled with active sessions.

---

# Offline Sync Edge Cases

1. Local order edited after server update.
2. Duplicate offline payment upload.
3. Offline inventory adjustment conflict.
4. Lost WebSocket events.
5. Device clock drift.

Resolution:
- Version checks
- Conflict log
- Manual merge where required

---

# Performance Edge Cases

- 10,000 active orders
- 1M ledger records
- Peak lunch concurrency
- Printer queue overflow
- Slow analytics query

Expected:
- Graceful degradation
- Queueing
- Timeouts with retry

---

# Recovery Validation

For every edge case verify:
- No data loss
- Audit entry created
- User informed
- System consistent
- Recovery repeatable

---

# QA Matrix

| Area | Automated | Integration | UAT |
|---|:---:|:---:|:---:|
| POS | ✔ | ✔ | ✔ |
| Kitchen | ✔ | ✔ | ✔ |
| Inventory | ✔ | ✔ | ✔ |
| Payments | ✔ | ✔ | ✔ |
| Procurement | ✔ | ✔ | ✔ |

---

# Next

11_QA_Test_Cases_01_Functional_Test_Cases.md
