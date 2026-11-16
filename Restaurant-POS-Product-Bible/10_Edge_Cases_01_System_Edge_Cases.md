# 10_Edge_Cases_01_System_Edge_Cases.md

# Restaurant POS Enterprise Product Bible
## Enterprise Edge Case Repository – Part 1

**Module ID:** EC-001

---

# 1. Purpose

This repository documents critical edge cases across the Restaurant POS platform. Each scenario defines trigger conditions, expected behavior, recovery strategy, audit requirements, and QA expectations.

---

# 2. Edge Case Classification

| Prefix | Domain |
|---|---|
| POS | Orders & Tables |
| PAY | Payments |
| INV | Inventory |
| KIT | Kitchen |
| PUR | Procurement |
| SEC | Security |
| SYS | Platform |
| NET | Network |
| DB | Database |

---

# 3. POS Edge Cases

### POS-001
**Scenario:** Two waiters open the same table simultaneously.

Expected:
- First request succeeds.
- Second receives `409 TABLE_ALREADY_OCCUPIED`.
- Audit entry created.

### POS-002
**Scenario:** Item deleted from menu while present in draft order.

Expected:
- Existing draft remains editable.
- New additions blocked.

### POS-003
**Scenario:** Table transfer during payment.

Expected:
- Transfer blocked until payment completes.

### POS-004
**Scenario:** Bill generated twice.

Expected:
- Existing bill returned.
- Duplicate generation prevented.

### POS-005
**Scenario:** Incremental KOT requested with no new items.

Expected:
- No KOT generated.
- Informational response returned.

---

# 4. Payment Edge Cases

### PAY-001
Gateway timeout after authorization.

Expected:
- Poll gateway.
- Prevent duplicate payment.
- Idempotency enforced.

### PAY-002
Duplicate callback received.

Expected:
- Ignore duplicate.
- Preserve original transaction.

### PAY-003
Split payment exceeds bill amount.

Expected:
- Reject request.
- Keep payment pending.

### PAY-004
Cash drawer unavailable.

Expected:
- Block cash settlement.
- Allow electronic methods.

### PAY-005
Refund requested after accounting period close.

Expected:
- Require finance approval.

---

# 5. Kitchen Edge Cases

### KIT-001
Chef accepts same KOT from two devices.

Expected:
- Optimistic locking.
- One acceptance succeeds.

### KIT-002
Station goes offline during preparation.

Expected:
- Queue retained.
- Supervisor alerted.

### KIT-003
Ready event published twice.

Expected:
- Ignore duplicate.

---

# 6. Inventory Edge Cases

### INV-001
Negative stock requested.

Expected:
- Reject unless configuration allows.

### INV-002
Expired batch selected.

Expected:
- Block issue.

### INV-003
Transfer destination disabled.

Expected:
- Cancel transfer.

---

# 7. Procurement Edge Cases

### PUR-001
Duplicate supplier invoice.

Expected:
- Reject posting.

### PUR-002
Partial receipt exceeds ordered quantity.

Expected:
- Validate against tolerance.

---

# 8. Security Edge Cases

### SEC-001
Expired JWT.

Expected:
- Return 401.

### SEC-002
Permission removed during active session.

Expected:
- Re-evaluate RBAC immediately.

---

# 9. System Edge Cases

### SYS-001
Server restart during transaction.

Expected:
- Transaction rollback.
- No partial commits.

### SYS-002
Clock skew across services.

Expected:
- Use UTC timestamps.
- Correlation IDs preserved.

### SYS-003
Feature flag disabled mid-session.

Expected:
- Respect rollout policy.

---

# 10. Network Edge Cases

### NET-001
Internet lost during KOT publish.

Expected:
- Queue request.
- Retry after reconnect.

### NET-002
Offline POS reconnects with stale data.

Expected:
- Version conflict detection.

---

# 11. Database Edge Cases

### DB-001
Deadlock detected.

Expected:
- Automatic retry.

### DB-002
Unique constraint violation.

Expected:
- Return 409 Conflict.

---

# 12. QA Traceability

Every edge case must include:
- Automated test
- Integration test
- UAT scenario
- Audit verification
- Recovery validation

---

# Next

10_Edge_Cases_02_Module_Edge_Cases.md
