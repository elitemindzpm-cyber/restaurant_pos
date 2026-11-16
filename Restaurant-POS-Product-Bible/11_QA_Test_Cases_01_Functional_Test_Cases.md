# 11_QA_Test_Cases_01_Functional_Test_Cases.md

# Restaurant POS Enterprise Product Bible
## QA Repository – Functional Test Cases

**Module ID:** QA-001

---

# 1. Purpose

Defines the standard functional QA approach for validating every module of the Restaurant POS platform.

---

# 2. Test Case Template

| Field | Description |
|---|---|
| Test Case ID | Unique identifier |
| Module | Functional area |
| Priority | Critical/High/Medium/Low |
| Preconditions | Required setup |
| Test Data | Input values |
| Steps | Execution sequence |
| Expected Result | Pass criteria |
| Automation | Yes/No |
| Traceability | Business Rule/API |

---

# 3. Authentication

### QA-AUTH-001 Successful Login

**Priority:** Critical

Preconditions
- Active user
- Correct password

Steps
1. Open login screen
2. Enter credentials
3. Click Login

Expected
- JWT generated
- Dashboard displayed
- Audit log created

---

### QA-AUTH-002 Invalid Password

Expected
- Error displayed
- Login denied
- Failed attempt logged

---

# 4. POS

### QA-POS-001 Create Dine-In Order

Steps
1. Open table
2. Create order
3. Add items
4. Save

Expected
- Order number generated
- Totals calculated
- Draft stored

Traceability
- ORD-001
- POST /orders

---

### QA-POS-002 Generate KOT

Expected
- KOT created
- Kitchen notified
- Duplicate prevented

---

### QA-POS-003 Complete Payment

Expected
- Payment posted
- Receipt generated
- Order closed
- Table released

---

# 5. Kitchen

### QA-KIT-001 Accept KOT

Expected
- Status changes to Accepted
- SLA timer starts

### QA-KIT-002 Mark Ready

Expected
- Waiter notified
- Duplicate ready ignored

---

# 6. Inventory

### QA-INV-001 Receive Stock

Expected
- Ledger updated
- Stock increased
- Audit entry written

### QA-INV-002 Transfer Stock

Expected
- Source decreases
- Destination increases
- Transfer completed

---

# 7. Procurement

### QA-PUR-001 Approve PO

Expected
- Status Approved
- GRN enabled

### QA-PUR-002 Duplicate Invoice

Expected
- Validation error
- Invoice rejected

---

# 8. Payments

### QA-PAY-001 Split Tender

Expected
- Sum equals bill
- Receipt generated

### QA-PAY-002 Refund

Expected
- Refund posted
- Audit recorded

---

# 9. Reporting

### QA-RPT-001 Sales Report

Expected
- Totals reconcile
- Filters applied

### QA-RPT-002 Export PDF

Expected
- PDF generated
- Export logged

---

# 10. Regression Suite

Critical
- Login
- Order lifecycle
- Payment
- KOT
- Inventory posting
- Reporting

---

# 11. Automation Candidates

- Authentication
- Order lifecycle
- Payments
- Inventory
- Reporting
- RBAC

---

# 12. Exit Criteria

- 100% Critical tests pass
- No open Critical defects
- High defects accepted or fixed
- UAT sign-off complete

---

# Next

11_QA_Test_Cases_02_Integration_and_Regression.md
