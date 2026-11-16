# Restaurant POS & Inventory Management System
## Master Product Bible — Single Reference Document

> **Version:** 1.0 | **Status:** Consolidated Reference | **Source:** 43 Product Bible Documents
>
> This master document consolidates all 43 markdown files from the Restaurant POS Product Bible into a single comprehensive reference for design, development, QA, UAT, and operations.

---

## Table of Contents

1. [Product Vision & Requirements](#1-product-vision--requirements)
2. [User Personas](#2-user-personas)
3. [Information Architecture](#3-information-architecture)
4. [Navigation Hierarchy](#4-navigation-hierarchy)
5. [User Journeys](#5-user-journeys)
6. [Screen Specifications](#6-screen-specifications)
7. [Database Design](#7-database-design)
8. [API Specifications](#8-api-specifications)
9. [Business Rules](#9-business-rules)
10. [Edge Cases](#10-edge-cases)
11. [QA Test Cases](#11-qa-test-cases)
12. [UAT Scenarios](#12-uat-scenarios)
13. [Deployment & Operations](#13-deployment--operations)

---

# 1. Product Vision & Requirements

## 1.1 Document Purpose

This document defines the business vision, functional scope, product goals, user personas, business assumptions, high-level architecture, functional modules, success metrics, and guiding principles for the Restaurant POS platform. It is the **foundation** for all subsequent documentation.

## 1.2 Product Vision

Build a cloud-native, scalable Restaurant POS and Operations platform that enables restaurants to manage sales, dine-in operations, takeaway, kitchen execution, inventory, purchasing, reporting, and administration from a single application.

## 1.3 Business Objectives

### Primary Objectives
- Complete billing within 30 seconds
- Reduce kitchen communication delays
- Improve inventory accuracy / Reduce food wastage
- Provide complete auditability
- Enable multi-branch expansion
- Support offline-first operations
- Deliver near real-time dashboards

### Success Metrics

| KPI | Target |
|------|--------|
| Billing Time | <30 sec |
| KOT Delivery | <2 sec |
| Table Turnaround | Improve by 20% |
| Inventory Variance | <2% |
| System Availability | 99.9% |
| Shift Closing Time | <10 min |

## 1.4 Product Scope

### Included
Authentication, RBAC, Menu Management, Dine-In, Takeaway, KOT, Kitchen Display, Payments, Cashier Shift, Purchase, Inventory, Reports, Dashboard, Audit Trail

### Future Phases
QR Ordering, Loyalty, CRM, Delivery Aggregators, Recipe/BOM, AI Forecasting

## 1.5 Stakeholders

| Stakeholder | Responsibility |
|---|---|
| Owner | Configuration, profitability, reports, approvals |
| Restaurant Manager | Daily operations, staff, inventory, approvals |
| Cashier | Billing, payments, shift closing |
| Waiter | Table management and customer orders |
| Kitchen Staff | Food preparation through KOT |
| Stock Controller | Purchases, stock count, adjustments |
| Accountant | Financial reconciliation and exports |

## 1.6 Core Product Principles

1. Minimal Clicks | 2. Fast POS | 3. Offline-first | 4. Secure by Design
5. Event-driven Architecture | 6. Modular Services | 7. Audit Everything
8. API-first | 9. Multi-Branch Ready | 10. Extensible

## 1.7 Functional Modules

**Authentication:** Login, PIN, Session, Password Policy, Device Registration

**POS:** Billing, Discounts, Payments, Hold/Recall, Receipt. Dependencies: Menu, Tables, Kitchen, Payments

**Kitchen:** Receive KOT, Update Status, Reprint, Cancel Approval

**Inventory:** Stock Ledger, Counts, Variance, Valuation
- Formula: `Available = Opening + Purchases` | `Used = Available - Physical` | `Gross Profit = Sales - Consumption Cost`

## 1.8 Business Assumptions

- One active order per table
- Paid bills cannot be edited
- Every financial action is auditable
- Discounts beyond limit require approval
- Inventory period is locked after approval
- KOT cancellation after preparation requires manager approval
- Sequential numbering is branch specific

## 1.9 High-Level Architecture

```
Browser/Tablet/POS --> API Gateway
API Gateway --> Auth Service
           --> POS Service --> PostgreSQL
           --> Kitchen Service
           --> Inventory Service --> PostgreSQL
           --> Reporting --> PostgreSQL
           --> Notification --> Email / WhatsApp
```

## 1.10 Risks & Mitigation

| Risk | Mitigation |
|---|---|
| Printer Failure | Offline queue, Retry |
| Internet Loss | Audit logs, Offline queue |
| Incorrect Stock Counts | Approval workflows |
| Concurrent Billing | Optimistic locking |
| Card Terminal Failure | Automatic recovery |

## 1.11 Non-Functional Requirements

- **Performance:** API <300ms average, POS interaction <2 seconds
- **Security:** JWT, RBAC, Encryption
- **Scalability:** 500+ outlets, Horizontal scaling
- **Availability:** 99.9%
- **Backup:** Daily backup, Point-in-time recovery

## 1.12 Acceptance Criteria

Support Dine-In and Takeaway, record every financial transaction, maintain inventory automatically, produce audit logs, export reports, support multiple branches, support configurable permissions.

---

# 2. User Personas

## 2.1 Persona Matrix

| Persona | Primary Goal | Primary Module |
|---|---|---|
| Owner | Business profitability | Dashboard |
| Restaurant Manager | Operations | Dashboard / Inventory |
| Cashier | Fast billing | POS |
| Waiter | Order management | Table / POS |
| Kitchen Staff | Food preparation | KDS |
| Stock Controller | Inventory accuracy | Inventory |
| Accountant | Financial reports | Reports |
| System Administrator | Configuration | Admin |

## 2.2 Owner

**Objectives:** Monitor daily sales, review profitability, approve critical transactions, monitor branches, configure business settings.

**KPIs:** Revenue, Gross Profit, Food Cost %, Inventory Variance, Average Order Value, Table Turnover

**Primary Screens:** Dashboard, Sales Reports, Inventory Reports, Approval Queue, User Management, Settings

**Daily Journey:** Login -> Dashboard -> Sales -> Inventory -> Approvals -> Reports -> Logout

**Business Rules:** Can access all branches. Can reopen locked inventory periods. Can override manager approvals. Can disable users immediately.

**Notifications:** High refund value, Inventory variance, Low stock, Cash mismatch, Failed backups

## 2.3 Restaurant Manager

**Responsibilities:** Shift supervision, staff allocation, discount approvals, refund approvals, inventory review

**Journey:** Login -> Open Shift -> Monitor Tables -> Approve Discount -> Stock Review -> Close Day

**KPIs:** Shift Sales, Kitchen SLA, Customer Wait Time, Refund %, Wastage %

## 2.4 Cashier

**Responsibilities:** Billing, Payment, Receipt printing, Shift opening, Shift closing

**Workflow:** Open Shift -> POS -> Payment -> Receipt -> Next Customer -> Close Shift

**Business Rules:**
- Cannot edit paid bills | Cannot exceed discount limit | Refund requires approval | Duplicate payment prevented

## 2.5 Waiter

**Responsibilities:** Allocate tables, capture orders, send KOT, serve food, generate bill request

**Journey:** Login -> Table Layout -> Open Table -> Take Order -> Send KOT -> Serve -> Bill Request

**Permissions:** Create order, Add items, Add remarks, Transfer table (if allowed)

**Cannot:** Refund, Void, Delete KOT after preparation

## 2.6 Kitchen Staff

**States:** New -> Preparing -> Ready -> Served

**KPIs:** Average preparation time, Delayed orders, Cancelled items

## 2.7 Stock Controller

**Workflow:** Purchase -> Stock Ledger -> Physical Count -> Variance -> Approval -> Close Period

**Business Rules:** Negative stock blocked unless configured. Approved count locks the period.

## 2.8 Accountant

**Reports Used:** Sales, Purchases, Inventory, Profitability, Cashier Shift

## 2.9 System Administrator

**Responsibilities:** User management, Role management, Branch setup, Printer configuration, Security policies

**Critical Rules:** Every action audited. Password policy enforced. Session timeout configurable.

## 2.10 Notification Matrix

| Event | Recipient |
|---|---|
| Order Ready | Waiter |
| Refund Pending | Manager |
| High Discount | Manager |
| Low Stock | Stock Controller |
| Cash Difference | Manager |
| Inventory Closed | Owner |
| Shift Closed | Accountant |

---

# 3. Information Architecture

## 3.1 Core Modules

1. Authentication | 2. Dashboard | 3. POS | 4. Table Management | 5. Takeaway
6. Kitchen Display | 7. Payments | 8. Cashier Shift | 9. Purchases
10. Inventory | 11. Reports | 12. Administration | 13. Notifications | 14. Audit Logs

## 3.2 Screen Inventory

| Module | Screens |
|---|---|
| Authentication | Login, Forgot Password, Session Lock |
| Dashboard | Executive, Operations, Kitchen Dashboard |
| POS | Quick Billing, Dine-In, Takeaway, Hold Orders, Recall Orders, Payment, Receipt Preview |
| Tables | Floor Layout, Transfer, Merge, Split |
| Kitchen | New Orders, Preparing, Ready, Completed |
| Inventory | Stock Ledger, Stock Count, Variance, Adjustments, Valuation |
| Purchases | Purchase Entry, Supplier, Purchase Returns |
| Reports | Sales, Inventory, Purchase, Cashier, GP Report |
| Admin | Users, Roles, Branches, Taxes, Printers, Settings |

## 3.3 Navigation Rules

- Maximum three clicks to operational screens
- Dashboard always accessible
- RBAC controlled menu visibility
- Breadcrumb on every page

## 3.4 Module Dependencies

| Module | Depends On |
|---|---|
| POS | Auth, Menu, Tables |
| Kitchen | POS |
| Inventory | Purchases |
| Reports | POS, Inventory |
| Admin | Auth |

---

# 4. Navigation Hierarchy

## 4.1 Navigation Principles

- Maximum 3 clicks to operational screens
- Dashboard is the primary landing page after login
- Context-sensitive navigation based on role
- Breadcrumbs on all management screens
- Frequently used actions accessible from shortcuts
- Destructive actions require confirmation

## 4.2 Role-Based Navigation

| Role | Default Landing Page | Accessible Modules |
|------|----------------------|--------------------|
| Owner | Executive Dashboard | All |
| Manager | Operations Dashboard | POS, Kitchen, Inventory, Reports |
| Cashier | POS | POS, Payments, Shift |
| Waiter | Table Layout | Tables, POS |
| Kitchen | Kitchen Display | Kitchen |
| Stock Controller | Inventory | Purchases, Inventory |
| Accountant | Reports | Reports, Exports |
| Admin | Admin Dashboard | Configuration |

## 4.3 Navigation Guards

| Action | Validation |
|--------|------------|
| Open POS | Active Shift Required |
| Payment | Order Exists |
| Inventory Count | Permission Required |
| Reopen Count | Admin Approval |
| Refund | Manager Approval |
| Admin Settings | Admin Role |

## 4.4 Deep Links

`/dashboard` | `/pos` | `/pos/dine-in` | `/pos/takeaway` | `/inventory` | `/reports/sales` | `/admin/users`

Authentication mandatory. Role validation before page load. Unauthorized access redirects to Access Denied.

## 4.5 Error Navigation

| Error | Navigation |
|---|---|
| Session Expired | Login |
| Printer Offline | Retry / Continue Without Print |
| Network Offline | Offline Queue |
| Permission Denied | Access Denied Screen |

---

# 5. User Journeys

## 5.1 Journey Catalog

| ID | Journey |
|----|---------:|
| UJ-001 | Restaurant Opening |
| UJ-002 | Dine-In Order |
| UJ-003 | Additional KOT |
| UJ-004 | Table Transfer |
| UJ-005 | Merge Tables |
| UJ-006 | Split Bill |
| UJ-007 | Takeaway |
| UJ-008 | Payment |
| UJ-009 | Refund |
| UJ-010 | Shift Closing |
| UJ-011 | Purchase Entry |
| UJ-012 | Stock Count |
| UJ-013 | Inventory Closing |

## 5.2 UJ-001 Restaurant Opening

Flow: Login -> Open Shift -> Cash Drawer -> Kitchen Online -> POS Ready -> Business Open

**Business Rules:** Previous shift must be closed unless override. Cashier enters opening cash. Kitchen printers/KDS health check before opening.

**Edge Cases:** Previous shift not closed, Printer unavailable, Network offline, Cash mismatch

## 5.3 UJ-002 Dine-In Order

**Flow:** Customer Arrives -> Select Table -> Create Order -> Add Items -> Send KOT -> Kitchen -> Preparing -> Ready -> Serve -> Bill -> Payment -> Receipt -> Close Table

**Detailed Business Logic:**
1. Waiter selects available table
2. System creates Order (Draft)
3. Items added with modifiers
4. Validation: Item active, Price available, Kitchen section mapped
5. KOT generated only for unsent items
6. Additional items generate incremental KOT
7. Payment locks order
8. Receipt generated
9. Table status changes to Available after successful payment

**APIs:** `GET /tables` | `GET /menu` | `POST /orders` | `POST /orders/{id}/items` | `POST /kot` | `POST /payments`

**Audit Events:** Order Created, Item Added, KOT Sent, Discount Approved, Payment Completed, Receipt Printed

**Recovery:** Retry KOT, Save draft locally, Resume unpaid order

## 5.4 UJ-003 Additional KOT

Flow: Existing Order -> Add Items -> Send Incremental KOT -> Kitchen Receives Only New Items

Rules: Previous KOT immutable. Deleted prepared items require approval.

## 5.5 UJ-004 Table Transfer

Rules: Destination table must be available. Audit source and destination. Preserve order history.

## 5.6 UJ-005 Merge Tables

Rules: Same branch. Both unpaid. Taxes recalculated. KOT history preserved.

## 5.7 UJ-006 Split Bill

**Split Methods:** By Item | By Quantity | By Amount | Equal Split

**Rules:** Total payable remains unchanged. Split receipts linked to original order.

## 5.8 UJ-007 Takeaway

Flow: Quick Order -> Token -> KOT -> Prepare -> Payment -> Pickup

Rules: Table not required. Token generated. Pickup status tracked.

## 5.9 UJ-008 Payment

**Payment Types:** Cash | Card | UPI | Mixed

**Rules:** Duplicate transaction prevention. Card reference stored. Balance calculated automatically.

## 5.10 UJ-009 Refund

**Prerequisites:** Original bill exists. Manager approval. Refund reason mandatory.

**Audit:** Before value, After value, Approver, Timestamp

## 5.11 UJ-010 Shift Closing

Manager verifies: Cash, Card, Refunds, Voids, Drawer Difference. If variance > tolerance -> Approval required.

## 5.12 UJ-011 Purchase Entry

Flow: Supplier -> Invoice -> Items -> Save -> Stock Ledger Update

Validation: Supplier active, Duplicate invoice check, Positive quantity

## 5.13 UJ-012 Stock Count

Flow: Opening -> Purchases -> Available -> Physical Count -> Variance -> Approval -> Lock Period

**Formula:** `Available = Opening + Purchases` | `Used = Available - Physical` | `Closing = Physical`

## 5.14 UJ-013 Inventory Closing

Business Logic: Freeze period -> Generate valuation -> Calculate GP -> Create opening for next period

## 5.15 Common Validation Rules

- No edit after payment | Negative quantity blocked | Inactive menu hidden
- Duplicate payment rejected | Closed inventory locked

## 5.16 Common Events

OrderCreated | KOTGenerated | PaymentCompleted | RefundApproved | StockAdjusted | InventoryClosed

---

# 6. Screen Specifications

## 6.1 Login (SCR-AUTH-001)

**Purpose:** Secure entry point. Authenticates users, identifies role, loads branch-specific configuration, routes to appropriate landing page.

**Wireframe:**
```
+------------------------------------------------------+
| Restaurant Logo                                      |
|------------------------------------------------------|
| Username / Employee ID                               |
| PIN / Password                                       |
| Branch (Auto / Dropdown)                             |
| Remember Device []                                   |
| [ Login ]                                            |
| Forgot Password?                                     |
+------------------------------------------------------+
```

**UI Components:**

| Component | Type | Mandatory |
|----------|------|-----------|
| Username | Text | Yes |
| Password/PIN | Password | Yes |
| Branch | Dropdown | Configurable |
| Login Button | Primary CTA | Yes |
| Forgot Password | Link | Optional |

**Validation Rules:** Username mandatory. Password mandatory. Password masked. Maximum 5 failed attempts. Locked account after configurable threshold. Inactive users cannot login.

**Role Routing:**

| Role | Landing Page |
|------|--------------|
| Owner | Executive Dashboard |
| Manager | Operations Dashboard |
| Cashier | POS |
| Waiter | Table Layout |
| Kitchen | Kitchen Display |
| Stock Controller | Inventory |
| Accountant | Reports |
| Admin | Admin Dashboard |

**API - POST /auth/login:**
```json
Request:  { "username": "cashier01", "password": "********", "branchId": 1 }
Success:  { "token": "jwt", "role": "Cashier", "landingPage": "/pos" }
Errors:   401 Invalid credentials | 403 User disabled | 423 Account locked
```

**Database Tables:** users | roles | branches | user_branch | user_sessions | audit_logs

**Acceptance Criteria:** Login completes in <2 seconds. JWT issued on success. RBAC loaded correctly. Landing page determined by role. All attempts audited. Sensitive data never logged.

---

## 6.2 Dashboard (SCR-DASH-001)

**Purpose:** Operational home screen. Provides real-time KPIs, shortcuts, alerts, pending approvals, and operational visibility based on role.

**Supported Roles:**

| Role | Dashboard Type |
|------|----------------|
| Owner | Executive Dashboard |
| Restaurant Manager | Operations Dashboard |
| Cashier | Shift Dashboard |
| Waiter | Floor Dashboard |
| Kitchen Staff | Kitchen Dashboard |
| Stock Controller | Inventory Dashboard |
| Accountant | Finance Dashboard |
| Admin | System Dashboard |

**Wireframe:**
```
+-----------------------------------------------------------------------------------+
| Logo | Branch | Date | Shift | Notifications | Profile                            |
+-----------------------------------------------------------------------------------+
| Today Sales | Orders | Avg Bill | Open Tables | Pending KOT | Inventory Alerts    |
+-----------------------------------------------------------------------------------+
| Quick Actions: [New Dine-In] [Takeaway] [Purchase] [Stock Count] [Reports]        |
+-----------------------------------------------------------------------------------+
| Charts: Sales Trend | Payment Mix | Top Selling Items | Hourly Orders             |
+-----------------------------------------------------------------------------------+
| Alerts & Approvals: Refund Approval | Low Stock | Cash Difference                 |
+-----------------------------------------------------------------------------------+
```

**KPI Cards:** Gross Sales, Net Sales, Orders Today, Average Order Value, Open Tables, Pending Kitchen Orders, Inventory Variance, Low Stock Items

**Charts:** Hourly Sales, Payment Mode Distribution, Top Selling Categories, Top Selling Items, Inventory Consumption, Purchase Trend

**Business Logic:**
1. Load dashboard based on user role
2. Fetch branch-specific metrics
3. Display today operational KPIs
4. Refresh every 30 seconds (configurable)
5. Display pending approvals / Highlight critical alerts

**APIs:** `GET /dashboard/summary` | `GET /dashboard/charts` | `GET /dashboard/alerts` | `GET /dashboard/approvals`

**Performance:** Initial load <3 seconds. Widget refresh <1 second. Cached fallback when reporting service unavailable.

**Acceptance Criteria:** Correct dashboard shown by role. KPIs match reports. Alerts displayed within refresh interval. Unauthorized widgets hidden.

---

## 6.3 POS Quick Billing (SCR-POS-001)

**Purpose:** Primary operational screen for Cashiers and Waiters to create orders, send KOTs, collect payments, and complete transactions in under 30 seconds.

**Roles:** Cashier (Full) | Waiter (Create Orders, Send KOT) | Manager (Full + Override) | Owner (View/Override)

**Wireframe:**
```
+---------------------------------------------------------------------------------+
| Branch | Shift | Cashier | Search Item                                           |
+---------------------------------------------------------------------------------+
| Categories    | Menu Grid                    | Current Cart                     |
| Breakfast     | Burger | Pizza | Tea         | Item  Qty  Price                  |
| Lunch         | Rice   | Juice | Dessert     | Modifier  / Remarks               |
+---------------------------------------------------------------------------------+
| Customer | Table | Order Type | Discount | Tax | Total | Payment | Send KOT      |
+---------------------------------------------------------------------------------+
| Hold | Recall | Split Bill | Split Payment | Print | Cancel | Complete Order     |
+---------------------------------------------------------------------------------+
```

**Pricing Engine:**
```
Net Price   = Base Price + Modifier Price - Item Discount + Tax
Grand Total = Sum(All Items) - Bill Discount + Service Charge + Tax +/- Rounding
```

**KOT Logic:** Only unsent items generate KOT. New items generate incremental KOT. Cancelled prepared items require manager approval.

**Order State Machine:**
`[*] -> Draft -> Confirmed -> KOTSent -> Preparing -> Ready -> Billing -> Paid -> Closed -> [*]`

**Validation Rules:**
- Table mandatory for Dine-In. Token mandatory for Takeaway
- Inactive items hidden. Zero quantity blocked. Negative price blocked
- Paid order immutable. Discount > limit requires approval
- Payment must equal payable amount

**APIs:** `GET /menu` | `GET /tables` | `POST /orders` | `POST /orders/{id}/items` | `POST /kot` | `POST /payments` | `GET /receipts/{id}`

**Performance:** Menu load <2 sec | Item search <300 ms | Payment <2 sec | KOT dispatch <2 sec

**Acceptance Criteria:** Cashier completes standard bill within 30 seconds. KOT generated only once per item. Payment cannot exceed outstanding amount. All financial actions auditable.

---

## 6.4 Table Layout & Floor Management (SCR-TBL-001)

**Purpose:** Real-time visual representation of restaurant floor. Enables table allocation, occupancy monitoring, transfer, merge, split bills, and dining duration tracking.

**Actors:**

| Role | Permissions |
|---|---|
| Waiter | Open assigned tables, create orders |
| Cashier | View tables, generate bills |
| Manager | Transfer, merge, override, reserve |
| Owner | View all |
| Admin | Configure layout |

**Wireframe:**
```
+------------------------------------------------------------------------+
| Floor: Ground    Refresh   Legend                                       |
+------------------------------------------------------------------------+
| Available   Occupied   Reserved   Billing   Cleaning                   |
+------------------------------------------------------------------------+
| T01   T02   T03   T04   T05   VIP1  VIP2  BAR1  BAR2                   |
+------------------------------------------------------------------------+
| Selected: T08 | Waiter: Rahul | Guests: 4 | Running Time: 00:43         |
| [Open] [Transfer] [Merge] [Split] [Reserve] [Generate Bill]            |
+------------------------------------------------------------------------+
```

**Table States:** `[*] -> Available -> Reserved -> Occupied -> Billing -> Cleaning -> Available`

**Business Logic:**
- **Open Table:** Table must be Available. Creates draft order. Assign waiter.
- **Transfer Table:** Destination must be Available. Entire order transferred. Audit both.
- **Merge Tables:** Same branch. Both unpaid. Preserve KOT history.
- **Split Bill:** By Item | By Quantity | By Percentage | Equal Split

**Validation Rules:** Table number unique. Capacity > 0. Only one active order per table. Paid tables cannot merge.

**WebSocket Events:** TableOpened | TableClosed | TableTransferred | TableMerged | TableReserved | TableReleased

**Concurrency:** Two waiters on same table: First acquires optimistic lock. Second receives "Table currently occupied by another user."

**UAT Acceptance Criteria:** Staff identifies table status instantly. Transfer completes without data loss. Merge preserves KOT history. Table status synchronizes across all terminals.

---

## 6.5 Dine-In Order Management (SCR-DINE-001)

**Purpose:** Complete lifecycle of a restaurant table order - from guest seating to order completion.

**Business Objectives:**
- Reduce order-taking time to under 2 minutes
- Eliminate duplicate orders | Ensure accurate KOT routing
- Track table occupancy and service time

**Screen Layout:**
```
+--------------------------------------------------------------------------------------+
| Table: T12 | Waiter: Rahul | Guests: 4 | Duration: 00:21                             |
+--------------------------------------------------------------------------------------+
| Categories | Menu | Search | Cart | Running Total | Remarks | Modifier | Seat No.    |
+--------------------------------------------------------------------------------------+
| Add Item | Send KOT | Hold | Recall | Transfer | Merge | Split | Bill | Payment      |
+--------------------------------------------------------------------------------------+
```

**Modifier Engine:** Supports Spice Level, Cooking Preference, Add-ons, Ingredient Removal, Portion Size. Modifier price updates item total instantly.

**KOT Rules:** Only unsent items in next KOT. Previous KOT immutable. Edited items after KOT require manager approval. Cancelled prepared items generate Cancel KOT.

**Pricing Engine:**
```
Item Total = (Base Price + Modifier Cost) x Quantity
Bill Total = Item Totals - Item Discounts - Bill Discount + Service Charge + Tax +/- Rounding
```

**Order State Machine:**
`[*] -> Draft -> Ordered -> KOTSent -> Preparing -> Ready -> Served -> Billing -> Paid -> Closed`

**Events:** OrderCreated | ItemAdded | ModifierApplied | KOTGenerated | KitchenAccepted | FoodReady | BillRequested | PaymentCompleted | TableReleased

**UAT Acceptance Criteria:** Waiter completes order entry in under 2 minutes. Kitchen receives only new items in additional KOT. Totals accurate after modifiers and discounts. Complete audit trail available.

---

## 6.6 Takeaway Order Management (SCR-TKO-001)

**Purpose:** Rapid order creation for non-dining customers. Manages token generation, kitchen routing, payment policy, packaging, pickup.

**Business Objectives:**
- Complete order entry in under 60 seconds | Generate unique pickup token
- Support pay-before and pay-after workflows | Prevent duplicate token issuance

**Wireframe:**
```
+----------------------------------------------------------------------------------+
| Token: Auto | Customer Name | Mobile | ETA | Search Menu                          |
+----------------------------------------------------------------------------------+
| Categories | Menu Grid | Cart | Qty | Modifiers | Remarks                         |
+----------------------------------------------------------------------------------+
| Total | Discount | Tax | Payment | Print Token | Send KOT | Complete              |
+----------------------------------------------------------------------------------+
| Queue Status | Preparing | Ready | Handed Over                                   |
+----------------------------------------------------------------------------------+
```

**Queue State Machine:**
`[*] -> Draft -> Token Issued -> Preparing -> Ready -> Awaiting Pickup -> Handed Over -> Closed`

**Payment Policy Configuration:** (1) Payment Before Preparation | (2) Payment After Preparation

**Validation Rules:** At least one item required. Token unique. Active menu items only. Payment mandatory before handover.

**APIs:** `GET /menu` | `POST /takeaway/orders` | `POST /takeaway/token` | `POST /kot` | `PATCH /takeaway/status` | `POST /payments` | `POST /pickup/complete`

**UAT Acceptance Criteria:** Token generated within 1 second. KOT to kitchen within 2 seconds. Duplicate token impossible. Queue status visible in real time.

---

## 6.7 Payment & Settlement (SCR-PAY-001)

**Purpose:** Finalizes transactions by securely collecting payments, generating fiscal receipts, updating cashier shifts, and closing orders.

**Supported Payment Methods:** Cash | Credit Card | Debit Card | UPI/QR | Digital Wallet | Gift Voucher | Mixed Payment | Complimentary (Manager Approval)

**Wireframe:**
```
+---------------------------------------------------------------------------------+
| Bill #10245   Table: T12   Customer: Walk-in                                    |
| Net Amount: 1,250.00 | Discount: 100.00 | Tax: 135.00 | Grand Total: 1,285.00   |
| Payment Mode: [Cash][Card][UPI][Wallet][Mixed]                                  |
| Cash Received: ___ | Card Amount: ___ Ref No: ___ | Change Return: ___          |
| [Collect Payment] [Split Payment] [Refund] [Print Receipt]                      |
+---------------------------------------------------------------------------------+
```

**Business Logic:**
- **Cash:** Calculate balance, update cash drawer
- **Card:** Capture terminal reference. Retry on timeout. Prevent duplicate authorization.
- **UPI/Wallet:** Store transaction ID. Validate callback before completion.
- **Mixed Payment:** Sum of tenders = Bill Amount. Remaining displayed in real time.
- **Complimentary:** Requires Manager approval + Reason + Audit log.

**Payment State Machine:**
`[*] -> Pending -> Processing -> Paid -> Refunded/Partially Refunded -> Closed`

**Refund Workflow:** Locate Bill -> Validate Eligibility -> Manager Approval -> Refund Mode -> Complete Refund -> Audit

**APIs:** `POST /payments` | `POST /payments/split` | `POST /payments/refund` | `GET /payments/{id}` | `POST /payments/reprint` | `POST /cashdrawer/update`

**Example:**
```json
{ "orderId": 10245, "paymentMode": "CARD", "amount": 1285.00, "reference": "TXN998812" }
```

**Validation Rules:**
- Outstanding amount cannot be negative. Paid bill cannot be paid again.
- Refund <= original payment. Card reference mandatory for card payments.
- Mixed payment must equal total. Shift must be open.

**UAT Acceptance Criteria:** All payment modes complete successfully. Duplicate payment impossible. Receipt generated for every successful payment. Cash drawer balances with shift report.

---

## 6.8 Kitchen Display System (SCR-KDS-001)

**Purpose:** Receives KOTs, routes to preparation stations, tracks preparation progress, monitors SLA, communicates food readiness to POS.

**Actors:** Chef | Kitchen Staff | Beverage Counter | Dessert Counter | Kitchen Supervisor | Restaurant Manager

**KOT Lifecycle:**
`[*] -> New -> Accepted -> Preparing -> Ready -> Served -> Closed` (also Preparing -> Cancelled -> Closed)

**Wireframe:**
```
+---------------------------------------------------------------------------------+
| Kitchen: Main | Refresh | SLA Timer | Active Orders: 18                         |
| NEW | PREPARING | READY | COMPLETED | CANCELLED                                 |
| KOT#101 | Table T12 | Waiter Rahul | 12:30 PM | ETA 08 min                      |
| 2x Veg Burger [No Onion] | 1x Fries | 2x Coke                                  |
| [Accept] [Prepare] [Ready] [Delay]                                              |
+---------------------------------------------------------------------------------+
```

**Station Routing:**

| Item Category | Station |
|---|---|
| Grill | Grill Counter |
| Pizza | Pizza Oven |
| Beverage | Beverage Counter |
| Dessert | Dessert Counter |
| Tea/Coffee | Beverage Station |

**SLA Rules:**

| Item Type | Target |
|---|---:|
| Tea/Coffee | 3 min |
| Beverage | 2 min |
| Starter | 8 min |
| Main Course | 15 min |
| Dessert | 5 min |

Alert at 80% SLA consumption.

**APIs:** `GET /kds/orders` | `GET /kds/station/{id}` | `POST /kds/accept` | `POST /kds/prepare` | `POST /kds/ready` | `POST /kds/cancel` | `GET /kds/history`

**WebSocket Events:** KOTCreated | KOTAccepted | PreparationStarted | ItemReady | KOTCancelled | WaiterNotified

**UAT Acceptance Criteria:** Correct station receives correct items. Waiter notified within 1 second of Ready. Incremental KOT contains only new items. All state transitions audited.

---

## 6.9 Purchase Entry & Procurement (SCR-PUR-001)

**Purpose:** Complete procurement lifecycle from supplier invoice capture through inventory update and financial posting.

**Procurement Workflow:** Supplier -> Create PO -> Approval -> Receive Goods -> Invoice Entry -> Quality Check -> Inventory Update -> Accounting -> Close Purchase

**Wireframe:**
```
+--------------------------------------------------------------------------------+
| Supplier | Invoice No | Invoice Date | Warehouse | Branch                       |
| Item | Qty | UOM | Unit Cost | Tax | Discount | Batch | Expiry                  |
| Freight | Other Charges | Landed Cost | Net Total                              |
| [Save Draft] [Submit] [Approve] [Print GRN] [Attach Invoice]                   |
+--------------------------------------------------------------------------------+
```

**Landed Cost Formula:**
```
Landed Cost = Purchase Cost + Freight + Handling + Import Duty + Other Charges
Weighted Avg Cost = ((Existing Qty x Existing Avg Cost) + (Received Qty x Landed Cost)) / Total Qty
```

**Purchase State Machine:**
`[*] -> Draft -> Submitted -> Approved -> Stock Updated -> Posted -> Closed`

**Approval Rules:**

| Condition | Approval Required |
|-----------|-----------|
| Invoice > Configured Limit | Manager |
| Backdated Entry | Manager |
| Negative Margin Purchase | Owner |
| Supplier On Hold | Owner |

**APIs:** `GET /suppliers` | `GET /inventory/items` | `POST /purchases` | `PATCH /purchases/{id}` | `POST /purchases/{id}/approve` | `POST /inventory/ledger`

**UAT Acceptance Criteria:** Duplicate invoices prevented. Inventory reflects approved purchases only. Weighted average cost recalculated correctly. Complete audit trail maintained.

---

## 6.10 Inventory Management (MOD-INV-001)

**Sub-Modules:** Inventory Dashboard | Stock Ledger | Stock In/Out | Internal Consumption | Purchase Receipt | Stock Transfer | Stock Adjustment | Physical Stock Count | Batch & Expiry | Reorder Engine | Valuation | Inventory Reports

**Inventory Formulae:**
```
Available Stock = Opening + Purchases + Transfers In - Transfers Out - Sales Consumption +/- Adjustments
Weighted Average Cost = ((Existing Qty x Existing Avg Cost) + (New Qty x New Cost)) / Total Qty
Inventory Value = Available Qty x Average Cost
Variance = Physical Count - System Quantity
```

**APIs:** `GET /inventory/dashboard` | `GET /inventory/ledger` | `POST /inventory/receipt` | `POST /inventory/transfer` | `POST /inventory/adjustment` | `POST /inventory/count` | `POST /inventory/count/approve` | `GET /inventory/valuation`

**Notification Rules:** Low stock threshold reached | Out of stock | Expiring within X days | Transfer awaiting receipt | Count approval pending | High variance detected

**Database Tables:** inventory_items | inventory_ledger | warehouses | warehouse_bins | stock_batches | stock_transfers | transfer_items | stock_adjustments | stock_counts | stock_count_items | inventory_valuation | reorder_rules | audit_logs

**UAT Acceptance Criteria:** Inventory always matches approved transactions. Stock valuation recalculated correctly. Variances require approval. Complete movement history available.

---

## 6.11 Reporting & Analytics (MOD-RPT-001)

**Report Catalog:**

| Category | Reports |
|---|---|
| Sales | Daily, Monthly, Hourly, Category, Item, Waiter Performance |
| Financial | Payment Summary, Tax Summary, Discounts, Refunds, Complimentary Bills |
| Kitchen | KOT SLA, Delayed Orders, Preparation Time, Station Performance |
| Inventory | Stock Ledger, Valuation, Variance, Consumption, Low Stock, Expiry |
| Procurement | Purchases, Supplier Performance, Price Variance |
| Audit | Login History, Overrides, Refund Log, Configuration Changes |

**KPI Definitions:**

| KPI | Formula |
|------|---------|
| Gross Sales | Sum(Order Total) |
| Net Sales | Gross - Discounts |
| Average Bill | Net Sales / Bills |
| Food Cost % | Consumption / Net Sales |
| Table Turnover | Closed Tables / Operating Hours |
| Refund % | Refund Amount / Net Sales |

**Business Logic:** Reports filtered by RBAC. Cached reports refreshed every 5 minutes. Large reports processed asynchronously.

**Export Formats:** PDF | Excel | CSV. Log every export. Watermark confidential reports.

**APIs:** `GET /reports/dashboard` | `GET /reports/sales` | `GET /reports/payments` | `GET /reports/inventory` | `GET /reports/kitchen` | `GET /reports/purchases` | `GET /reports/audit` | `POST /reports/export` | `POST /reports/schedule`

**UAT Acceptance Criteria:** KPIs match transactional data. Reports export successfully. Drill-down works without data loss. Unauthorized reports hidden. Scheduled reports delivered successfully.

---

## 6.12 Administration & Settings (MOD-ADM-001)

**Functional Areas:**

| Area | Capabilities |
|---|---|
| User Management | Create/Edit/Deactivate, Password reset, Device/Branch assignment |
| RBAC | Roles, Permissions, Screen access, API access, Approval levels |
| Branches | Branch master, Warehouse mapping, Operating hours, Currency, Timezone |
| Menu Masters | Categories, Menu Items, Modifiers, Combo Meals, Pricing |
| Kitchen | Station mapping, Printer mapping, SLA defaults |
| Taxes | GST/VAT profiles, Inclusive/Exclusive tax, Service charge |
| Payments | Payment modes, Gateway credentials, Refund policy, Tip policy |
| Notifications | Email, SMS, WhatsApp, Push, Webhooks |
| Security | Password policy, Session timeout, IP restrictions |

**Approval Matrix:**

| Action | Approval |
|---|---|
| Delete User | Owner |
| Change Tax | Owner |
| Change Payment Gateway | Owner |
| Disable Branch | Owner |
| Publish Feature Flag | Admin |

**APIs:** `GET /admin/users` | `POST /admin/users` | `PATCH /admin/users/{id}` | `GET /admin/roles` | `POST /admin/roles` | `GET /admin/branches` | `POST /admin/taxes` | `POST /admin/printers` | `POST /admin/kitchen-stations` | `POST /admin/payment-modes` | `POST /admin/settings`

**UAT Acceptance Criteria:** RBAC enforced across all modules. Configuration changes versioned. Critical changes require approval. Audit logs immutable. Rollback restores previous configuration.

---

# 7. Database Design

## 7.1 ERD & Data Model

**Design Principles:** PostgreSQL 16+ | Third Normal Form (3NF) | UUID primary keys | Soft delete for business entities | Immutable financial records | Audit trail for all mutations | Multi-tenant ready (Branch-aware)

**Domain Model:**
```
BRANCH ||--o{ USER : contains
ROLE ||--o{ USER : assigns
USER ||--o{ SHIFT : opens
SHIFT ||--o{ ORDER : creates
TABLE_MASTER ||--o{ ORDER : seats
ORDER ||--|{ ORDER_ITEM : contains
ORDER ||--o{ PAYMENT : settles
ORDER ||--o{ KOT : generates
MENU_ITEM ||--o{ ORDER_ITEM : ordered
SUPPLIER ||--o{ PURCHASE : supplies
INVENTORY_ITEM ||--o{ INVENTORY_LEDGER : tracks
WAREHOUSE ||--o{ INVENTORY_LEDGER : stores
```

**Naming Standards:**
- Tables: snake_case plural | Columns: snake_case
- Primary Key: id (UUID) | Foreign Keys: <entity>_id
- Audit Columns: created_at, updated_at, deleted_at, created_by, updated_by

**Indexing Strategy:**

**Mandatory:** users(username) | orders(order_number) | payments(transaction_reference) | inventory_ledger(item_id, warehouse_id) | purchases(invoice_number, supplier_id) | audit_logs(created_at)

**Composite:** (branch_id, created_at) | (status, created_at) | (warehouse_id, item_id)

**Partitioning:** audit_logs, inventory_ledger, event_logs by month | order_items by year

**Archival:** Hot = current year | Warm = previous 5 years | Cold = >5 years (Read-only)

**Performance Targets:** Order lookup <100ms | Payment lookup <100ms | Inventory query <300ms | Dashboard aggregation <2sec

## 7.2 User & Security Tables

**Tables:** users | user_profiles | roles | permissions | role_permissions | user_roles | branches | user_branches | devices | user_sessions | login_history | password_history | api_keys | oauth_clients | feature_flags | security_policies

**Table: users**

| Column | Type | Null | Description |
|---|---|---|---|
| id | UUID | No | Primary Key |
| employee_code | VARCHAR(30) | No | Unique employee code |
| username | VARCHAR(100) | No | Login name |
| email | VARCHAR(255) | Yes | Email |
| phone | VARCHAR(20) | Yes | Mobile |
| password_hash | TEXT | No | Encrypted password |
| status | ENUM | No | Active/Inactive/Locked |
| default_branch_id | UUID | Yes | FK Branch |
| created_at | TIMESTAMP | No | Audit |

**Business Rules:** Username immutable after first login (configurable). Password stored only as salted hash. Locked users cannot authenticate.

**Permission Format:** `MODULE.ACTION` Examples: `POS.CREATE` | `POS.REFUND` | `PAYMENT.VOID` | `INVENTORY.ADJUST` | `REPORT.EXPORT` | `ADMIN.USER.CREATE`

**Permission Matrix:**

| Role | Create Order | Refund | Inventory | Users |
|---|---|---|---|---|
| Cashier | Yes | No | No | No |
| Manager | Yes | Yes | Yes | No |
| Owner | Yes | Yes | Yes | Yes |

## 7.3 POS Tables

**Domain ERD:**
```
MENU_CATEGORY ||--o{ MENU_ITEM : contains
MENU_ITEM ||--o{ MENU_PRICE : priced
TABLE_MASTER ||--o{ TABLE_SESSION : opens
TABLE_SESSION ||--o{ ORDER : owns
ORDER ||--|{ ORDER_ITEM : contains
ORDER ||--o{ KOT : generates
KOT ||--|{ KOT_ITEM : contains
ORDER ||--o{ PAYMENT : settles
ORDER ||--o{ RECEIPT : prints
PAYMENT ||--o{ REFUND : creates
```

**Key Tables:**
- **orders:** id (UUID), order_number (BIGINT UNIQUE), table_session_id, branch_id, shift_id, order_type, status, subtotal, discount, tax, service_charge, grand_total, created_by, created_at. State: `Draft->Confirmed->KOT->Preparing->Ready->Paid->Closed`
- **order_items:** id, order_id, menu_item_id, seat_no, quantity, unit_price, modifier_total, tax_amount, status. Immutable after payment. Versioned after KOT.
- **kot:** id, order_id, kot_number, station_id, status, generated_at. Incremental KOT. Only unsent items.
- **payments:** id, order_id, payment_mode, amount, gateway_reference, status, paid_at. gateway_reference UNIQUE.
- **menu_prices:** Supports Branch pricing, Happy hour, Date-effective pricing. Unique: (menu_item_id, branch_id, effective_from)

**Cross-table Rules:**
1. One active order per table session
2. Paid orders cannot be edited
3. Receipt generated only after successful payment
4. KOT created only for confirmed items
5. Refund <= payment amount
6. Menu item deletion prohibited when referenced

## 7.4 Inventory Tables

**Tables Covered:** inventory_categories | unit_of_measure | inventory_items | warehouses | warehouse_bins | inventory_ledger | stock_batches | stock_transfers | transfer_items | stock_adjustments | stock_count | stock_count_items | inventory_valuation | reorder_rules | recipe_bom (future) | consumption_history

**inventory_ledger (Immutable):**
- Columns: id, item_id, warehouse_id, transaction_type, reference_type, reference_id, quantity_in, quantity_out, running_balance, average_cost, transaction_date
- Rules: Append only. No updates after posting.

**stock_batches:** id, item_id, batch_number, manufacture_date, expiry_date, quantity, remaining_quantity. Expired batches blocked (configurable).

**stock_transfers States:** `Draft -> Approved -> Shipped -> Received -> Closed`

**stock_count States:** `Open -> Counting -> Submitted -> Approved -> Closed`

**Variance Formula:** `Variance = Physical - System`

**Cross-table Business Rules:**
1. Ledger is immutable | 2. Batch depletion follows FEFO if enabled
3. Warehouse transfer requires source availability | 4. Stock count freezes inventory
5. Reorder triggered when available <= minimum | 6. Consumption generated from completed orders

## 7.5 Purchase Tables

**Tables Covered:** suppliers | supplier_contacts | purchase_requests | purchase_request_items | purchase_orders | purchase_order_items | goods_receipts | goods_receipt_items | purchase_invoices | purchase_invoice_items | landed_costs | supplier_payments | purchase_returns | purchase_return_items | supplier_price_history | supplier_documents

**suppliers:** id, supplier_code (UNIQUE), supplier_name, gst_number, pan_number, payment_terms, credit_limit, status. Inactive suppliers cannot receive new POs.

**purchase_orders States:** `Draft -> Submitted -> Approved -> Partially Received -> Received -> Closed -> Cancelled`

**landed_costs Types:** Freight, Insurance, Customs, Local Transport, Handling. Allocation: By Value, Quantity, Weight, or Equal.

**Cross-table Rules:**
1. PO approval required before GRN | 2. GRN updates inventory
3. Invoice cannot exceed configurable tolerance | 4. Returns reverse inventory
5. Supplier payment only for approved invoices | 6. Closed PO immutable

## 7.6 Admin Tables

**Tables Covered:** setting_groups | system_settings | branches | business_profiles | tax_profiles | payment_modes | printers | printer_mappings | kitchen_stations | notification_templates | notification_channels | integrations | api_credentials | feature_flags | feature_history | configuration_versions

**tax_profiles:** Columns: id, tax_code, tax_name, tax_percentage, tax_type, effective_from, effective_to. Rule: Effective periods cannot overlap.

**kitchen_stations:** Columns: id, station_code, station_name, printer_id, sla_minutes, display_order.

**feature_flags:** Columns: feature_key, enabled, rollout_percentage, environment, effective_date. Supports: Gradual rollout, Branch rollout, A/B testing.

**Cross-table Rules:**
1. Tax profiles versioned | 2. Feature flag publish creates history
3. Printer mapping required before POS activation | 4. Payment mode inactive => hidden from POS
5. Notification template required before channel activation | 6. Configuration rollback restores previous values

## 7.7 Audit Tables

**Tables Covered:** audit_logs | audit_log_details | activity_timeline | event_logs | login_history | api_request_logs | api_response_logs | notification_logs | scheduler_logs | integration_logs | webhook_logs | error_logs | security_events | data_change_history | report_execution_logs | background_jobs

**audit_logs (Immutable):**

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary Key |
| entity_type | VARCHAR | Target entity |
| entity_id | UUID | FK to entity |
| action | VARCHAR | Action performed |
| user_id | UUID | Actor |
| branch_id | UUID | Branch context |
| before_snapshot | JSONB | State before |
| after_snapshot | JSONB | State after |
| reason | TEXT | Justification |
| ip_address | INET | Client IP |
| created_at | TIMESTAMP | UTC timestamp |

**Rules:** Append-only. Updates prohibited. Snapshots stored as JSONB.

**Data Retention:**

| Table | Online | Archive |
|--------|-------:|--------:|
| audit_logs | 5 yrs | 7 yrs |
| login_history | 1 yr | 3 yrs |
| api_request_logs | 90 d | 1 yr |
| error_logs | 180 d | 2 yrs |
| event_logs | 1 yr | 5 yrs |

**Partition Strategy:** Monthly partitions for audit_logs, api_request_logs, event_logs, notification_logs, error_logs

**Business Rules:**
1. Financial records never physically deleted
2. Audit logs immutable
3. Correlation ID mandatory for every request
4. Every approval action generates an audit record
5. Security events trigger alerts based on severity

---

# 8. API Specifications

## 8.1 OpenAPI Overview

**Standards:** RESTful APIs | JSON payloads | UTF-8 encoding | HTTPS only | JWT authentication | OpenAPI 3.1 | ISO-8601 timestamps | UUID identifiers

**URL Convention:** `/api/v1/{module}/{resource}`

**Authentication Headers:**
```
Authorization: Bearer <JWT>
X-Correlation-Id: uuid
X-Branch-Id: uuid
```

**Standard Response:**
```json
{ "success": true, "message": "Request completed", "data": {}, "meta": { "requestId": "uuid", "timestamp": "2026-01-01T10:00:00Z" } }
```

**Standard Error:**
```json
{ "success": false, "error": { "code": "ORDER_NOT_FOUND", "message": "Order not found" } }
```

**HTTP Status Codes:** 200 OK | 201 Created | 204 No Content | 400 Bad Request | 401 Unauthorized | 403 Forbidden | 404 Not Found | 409 Conflict | 422 Validation Error | 429 Rate Limited | 500 Internal Error

**Pagination:** `GET /orders?page=1&pageSize=20` Response includes: page, pageSize, totalRecords, totalPages

**Idempotency:** Required for Payments, Refunds, Purchase Posting. Header: `Idempotency-Key: uuid`

**Rate Limiting:** 100 requests/min/user | 1000 requests/min/service. Headers: X-RateLimit-Limit | X-RateLimit-Remaining | Retry-After

**Webhooks Events:** payment.completed | order.closed | inventory.low_stock | purchase.received | refund.completed. Retry: Exponential backoff with dead-letter queue.

## 8.2 Authentication API

**Endpoints:**
- `POST /api/v1/auth/login` - Authenticate user. Returns JWT + Refresh Token.
- `POST /api/v1/auth/logout` - Invalidate session. Returns 204.
- `POST /api/v1/auth/refresh` - Exchange refresh token for new JWT.
- `POST /api/v1/auth/forgot-password` - Send reset email.
- `POST /api/v1/auth/reset-password` - Reset with token.
- `POST /api/v1/auth/change-password` - Authenticated user changes own password.
- `GET /api/v1/auth/profile` - Returns User, Role, Branch, Permissions.
- `GET /api/v1/auth/sessions` - List active sessions.
- `DELETE /api/v1/auth/sessions/{id}` - Terminate session.
- `POST /api/v1/auth/device/register` - Register trusted device.

**Security Rules:** JWT expiry: 60 min | Refresh Token: 30 days | Account lock after configurable failures | Password history: last 5

**Error Catalog:**

| Code | Description |
|------|-------------|
| INVALID_CREDENTIALS | Wrong username/password |
| ACCOUNT_LOCKED | Too many failures |
| TOKEN_EXPIRED | JWT expired |
| TOKEN_INVALID | Invalid token |
| PASSWORD_WEAK | Complexity failed |
| PASSWORD_REUSED | History violation |
| SESSION_EXPIRED | Idle timeout |

## 8.3 POS API

**Menu APIs:** `GET /api/v1/menu` | `GET /api/v1/menu/{id}` | `GET /api/v1/menu/categories`

**Table APIs:** `GET /api/v1/tables` | `POST /api/v1/tables/{id}/open` | `POST /api/v1/tables/transfer` | `POST /api/v1/tables/merge` | `POST /api/v1/tables/split`

**Order APIs:** `POST /api/v1/orders` | `GET /api/v1/orders/{id}` | `PATCH /api/v1/orders/{id}` | `DELETE /api/v1/orders/{id}`

**Order Items:** `POST /api/v1/orders/{id}/items` | `PATCH /api/v1/orders/{id}/items/{itemId}` | `DELETE /api/v1/orders/{id}/items/{itemId}`

**Discount APIs:** `POST /api/v1/orders/{id}/discount` | `POST /api/v1/orders/{id}/discount/approve` (Manager approval above threshold)

**KOT APIs:** `POST /api/v1/kot` | `GET /api/v1/kot/{id}` | `POST /api/v1/kot/{id}/cancel`

**Billing APIs:** `POST /api/v1/orders/{id}/bill` | `POST /api/v1/orders/{id}/hold` | `POST /api/v1/orders/{id}/recall` | `POST /api/v1/orders/{id}/close`

**Receipt APIs:** `GET /api/v1/receipts/{id}` | `POST /api/v1/receipts/{id}/reprint`

**Error Codes:** ORDER_NOT_FOUND | TABLE_OCCUPIED | ITEM_INACTIVE | KOT_ALREADY_SENT | PAYMENT_PENDING | INVALID_DISCOUNT | RECEIPT_NOT_AVAILABLE

**Business Rules:**
1. One active order per table | 2. Paid orders immutable | 3. KOT immutable after generation
4. Bill generated once | 5. Receipt linked to payment | 6. Duplicate requests ignored via idempotency key

## 8.4 Kitchen API

**Flow:** POS -> POST /kot -> API -> KDS -> Chef Accept -> Start Preparation -> Mark Ready -> Notify Waiter

**Queue APIs:** `GET /api/v1/kitchen/queue` (Filters: stationId, status, priority) | `GET /api/v1/kitchen/orders/{id}`

**Station APIs:** `GET /api/v1/kitchen/stations` | `PATCH /api/v1/kitchen/stations/{id}/status` (Active/Busy/Offline/Maintenance)

**KOT APIs:** `POST /api/v1/kot` | `GET /api/v1/kot/{id}` | `POST /api/v1/kot/{id}/cancel`

**Preparation APIs:** `POST /api/v1/kitchen/orders/{id}/accept` | `/start` | `/ready` | `/served`

**SLA APIs:** `GET /api/v1/kitchen/sla` | `GET /api/v1/kitchen/performance`

**WebSocket Events:** kot.created | kot.accepted | preparation.started | preparation.ready | order.served | station.offline

**Error Codes:** KOT_NOT_FOUND | KOT_ALREADY_ACCEPTED | INVALID_STATE | STATION_OFFLINE | ORDER_ALREADY_READY | SLA_EXCEEDED

## 8.5 Inventory API

**Inventory Item APIs:** `GET/POST/PATCH/DELETE /api/v1/inventory/items` | `GET /api/v1/inventory/items/{id}`

**Warehouse APIs:** `GET /api/v1/warehouses` | `POST /api/v1/warehouses` | `GET /api/v1/warehouses/{id}/stock`

**Goods Receipt APIs:** `POST /api/v1/inventory/receipts` | `GET /api/v1/inventory/receipts/{id}`

**Stock Ledger APIs:** `GET /api/v1/inventory/ledger` | `GET /api/v1/inventory/ledger/{itemId}`

**Transfer APIs:** `POST /api/v1/inventory/transfers` | `/approve` | `/ship` | `/receive`

**Adjustment APIs:** `POST /api/v1/inventory/adjustments` (Reasons: Damage/Theft/Expiry/Wastage/Correction)

**Stock Count APIs:** `POST /api/v1/inventory/counts` | `/submit` | `/approve`

**Batch APIs:** `GET/POST/PATCH /api/v1/inventory/batches` (FEFO allocation supported)

**Valuation APIs:** `GET /api/v1/inventory/valuation` | `GET /api/v1/inventory/value-history`

**Reorder APIs:** `GET /api/v1/inventory/reorder` | `POST /api/v1/inventory/reorder/generate-po`

**Error Codes:** ITEM_NOT_FOUND | NEGATIVE_STOCK | BATCH_EXPIRED | WAREHOUSE_NOT_FOUND | TRANSFER_CONFLICT | COUNT_ALREADY_OPEN

## 8.6 Procurement API

**Supplier APIs:** `GET/POST/PATCH/DELETE /api/v1/suppliers`

**Purchase Request APIs:** `POST /api/v1/purchase-requests` | `GET /api/v1/purchase-requests` | `/submit`. States: `Draft -> Submitted -> Approved -> Converted`

**Purchase Order APIs:** `POST/GET/PATCH /api/v1/purchase-orders` | `/approve` | `/cancel`

**GRN APIs:** `POST /api/v1/goods-receipts` | `GET /api/v1/goods-receipts/{id}` | `/complete`

**Invoice APIs:** `POST/GET/PATCH /api/v1/purchase-invoices`

**Landed Cost APIs:** `POST /api/v1/landed-costs` (Allocation: By Value/Qty/Weight/Equal)

**Supplier Payment APIs:** `POST /api/v1/supplier-payments` | `GET /api/v1/supplier-payments`

**Purchase Return APIs:** `POST/GET /api/v1/purchase-returns` | `/approve`

**Error Codes:** SUPPLIER_NOT_FOUND | DUPLICATE_INVOICE | PO_NOT_APPROVED | GRN_ALREADY_POSTED | INVALID_APPROVAL | CREDIT_LIMIT_EXCEEDED

## 8.7 Reporting API

**Dashboard APIs:** `GET /api/v1/reports/dashboard` | `/widgets` | `/summary`

**KPI APIs:** `GET /api/v1/reports/kpi` (Gross Sales, Net Sales, Average Bill, Food Cost %, Table Turnover, Refund %, Inventory Value)

**Sales Report APIs:** `GET /api/v1/reports/sales` | `/hourly` | `/items` | `/categories` | `/waiters`

**Financial Report APIs:** `GET /api/v1/reports/payments` | `/taxes` | `/discounts` | `/refunds`

**Inventory Report APIs:** `GET /api/v1/reports/inventory` | `/ledger` | `/valuation` | `/expiry` | `/variance`

**Kitchen Report APIs:** `GET /api/v1/reports/kitchen` | `/sla` | `/stations`

**Procurement Report APIs:** `GET /api/v1/reports/purchases` | `/suppliers` | `/price-variance`

**Audit Report APIs:** `GET /api/v1/reports/audit` | `/security` | `/activity`

**Export APIs:** `POST /api/v1/reports/export` (Formats: PDF, Excel, CSV)

**Scheduled Report APIs:** `POST/GET/DELETE /api/v1/reports/schedule`

**Drill-down APIs:** `GET /api/v1/reports/drilldown/orders` | `/inventory` | `/payments`

**Error Codes:** REPORT_NOT_FOUND | INVALID_DATE_RANGE | EXPORT_FAILED | REPORT_TIMEOUT | PERMISSION_DENIED | SCHEDULE_NOT_FOUND

## 8.8 Admin API

**User APIs:** `GET/POST/PATCH/DELETE /api/v1/admin/users`

**Role & Permission APIs:** `GET/POST/PATCH /api/v1/admin/roles` | `GET /api/v1/admin/permissions` | `POST /api/v1/admin/roles/{id}/permissions`

**Branch APIs:** `GET/POST/PATCH /api/v1/admin/branches`

**System Settings APIs:** `GET/POST /api/v1/admin/settings` | `PATCH /api/v1/admin/settings/{key}`

**Tax APIs:** `GET/POST/PATCH/DELETE /api/v1/admin/taxes`

**Payment Mode APIs:** `GET/POST/PATCH /api/v1/admin/payment-modes`

**Printer & Kitchen APIs:** `GET/POST /api/v1/admin/printers` | `/admin/kitchen-stations`

**Notification APIs:** `GET/POST /api/v1/admin/notification-templates` | `POST /admin/notifications/test`

**Feature Flag APIs:** `GET/POST/PATCH /api/v1/admin/feature-flags` (Supports gradual, branch, and environment rollout)

**Integration APIs:** `GET/POST/PATCH /api/v1/admin/integrations` | `POST /admin/integrations/{id}/test`

**Configuration Version APIs:** `GET /api/v1/admin/configuration/versions` | `/publish` | `/rollback`

**Error Codes:** USER_ALREADY_EXISTS | ROLE_NOT_FOUND | BRANCH_LOCKED | INVALID_TAX_PROFILE | FEATURE_FLAG_CONFLICT | INTEGRATION_TEST_FAILED | CONFIGURATION_ROLLBACK_FAILED

**Business Rules:**
1. All admin APIs require elevated RBAC | 2. Every configuration change creates an audit record
3. Secrets never returned in API responses | 4. Feature flags are version-controlled
5. Rollback restores previous published configuration | 6. Integration tests never expose credentials

---

# 9. Business Rules

## 9.1 Core Business Rules

**Order Rules:**
- ORD-001: Exactly one active order per active table session
- ORD-002: Order starts in Draft and must transition through the approved state machine
- ORD-003: Paid orders are immutable except through approved refund workflows
- ORD-004: Deleted orders are soft-deleted and retained for audit
- ORD-005: Order number must be unique within the enterprise
- ORD-006: Additional items after KOT create incremental KOTs only
- ORD-007: Cancelled prepared items require manager approval
- ORD-008: Taxes are calculated after item discounts and before bill-level rounding
- ORD-009: Order totals are recalculated after every mutation
- ORD-010: Every order mutation generates an audit event

**Table Rules:**
- TAB-001: Available tables may be opened
- TAB-002: Occupied tables cannot be reopened
- TAB-003: Table merge preserves original audit trail
- TAB-004: Table transfer moves ownership without changing order history
- TAB-005: Closed tables automatically return to Available after housekeeping

**Kitchen Rules:**
- KOT-001: Only confirmed items generate KOT
- KOT-002: Incremental KOT contains only unsent items
- KOT-003: Kitchen cannot edit commercial pricing
- KOT-004: Ready requires Preparing state
- KOT-005: SLA breach generates notification event

**Payment Rules:**
- PAY-001: Sum of tenders must equal bill total
- PAY-002: Duplicate payment requests are prevented using idempotency
- PAY-003: Refund amount cannot exceed original payment
- PAY-004: Complimentary bills require approval
- PAY-005: Receipt generated only after successful payment

**Inventory Rules:**
- INV-001: Inventory ledger is append-only
- INV-002: Negative stock prohibited unless explicitly configured
- INV-003: Batch depletion follows FEFO where enabled
- INV-004: Approved purchase updates inventory
- INV-005: Physical count freezes inventory movement

**Procurement Rules:**
- PUR-001: Supplier must be active | PUR-002: PO approval required before GRN
- PUR-003: Duplicate supplier invoice blocked | PUR-004: Returns reverse inventory movement
- PUR-005: Closed purchase documents are immutable

**Administration Rules:**
- ADM-001: All configuration changes are versioned | ADM-002: System roles cannot be deleted
- ADM-003: Feature flag rollout is auditable | ADM-004: Secrets are encrypted at rest
- ADM-005: Configuration rollback restores last published version

**Security Rules:**
- SEC-001: JWT required for protected APIs | SEC-002: Password history prevents reuse
- SEC-003: Account locks after configured failures | SEC-004: RBAC evaluated on every request
- SEC-005: All privileged actions are audited

**Reporting Rules:**
- RPT-001: Reports honor branch permissions | RPT-002: Scheduled reports execute asynchronously
- RPT-003: Export actions are logged | RPT-004: Dashboard widgets use cached data when available

**Core Decision Table:**

| Condition | Action |
|---|---|
| Discount > Waiter Limit | Manager Approval |
| Stock < Reorder Level | Trigger Reorder |
| Payment Failed | Keep Bill Open |
| Kitchen SLA Breach | Notify Manager |

## 9.2 Order & Pricing Rules

**Pricing Calculation Pipeline:** Menu Price -> Branch Price -> Time Price -> Modifier Price -> Item Discount -> Bill Discount -> Service Charge -> Tax -> Round Off -> Grand Total

**Price Resolution Hierarchy:**

| Priority | Rule |
|---:|---|
| 1 | Manual manager override (if permitted) |
| 2 | Promotional/Happy Hour price |
| 3 | Branch-specific price |
| 4 | Default menu price |

**Pricing Rules:**
- PRI-001: Every menu item must have an active base price
- PRI-002: Branch-specific price overrides the default price
- PRI-003: Only one promotional price active for an item at a given time
- PRI-004: Expired promotional prices are ignored
- PRI-005: Future prices become effective only on their effective date

**Discount Rules:**
- DISC-001: Item discounts apply before bill discounts
- DISC-002: Highest-priority eligible discount wins unless stacking is enabled
- DISC-003: Manager approval required above configured threshold
- DISC-004: Discount reason mandatory when configured
- DISC-005: Paid bills cannot receive new discounts

**Tax Rules:**
- TAX-001: Taxes calculated after item discounts
- TAX-002: Inclusive and exclusive tax profiles supported
- TAX-003: Tax profile resolved from menu item
- TAX-004: Historical transactions retain original tax version

**Formula Library:**
```
Item Total    = (Base Price + Modifier Total) x Qty
Bill Subtotal = Sum of Item Totals
Grand Total   = Subtotal - Discounts + Service Charge + Taxes +/- Round Off
```

**Split Bill Rules:**
- SPLIT-001: Split by seat | SPLIT-002: Split by item
- SPLIT-003: Equal split | SPLIT-004: Sum of split bills must equal original bill

## 9.3 Inventory & Procurement Rules

**Stock Rules:** INV-001 Every movement creates immutable ledger entry | INV-002 Inventory balance is ledger-derived | INV-003 Negative stock blocked unless configured | INV-004 Reference document mandatory | INV-005 All inventory changes audited

**Batch Rules:** BAT-001 Batch required for tracked items | BAT-002 Expiry required where enabled | BAT-003 FEFO allocation supported | BAT-004 Expired stock blocked by policy | BAT-005 Empty batches retained

**Warehouse Rules:** WH-001 Source warehouse must have stock | WH-002 Destination warehouse must be active | WH-003 Transfer creates inbound/outbound ledger | WH-004 Receipt confirms ownership | WH-005 One default warehouse per branch

**Stock Count Rules:** CNT-001 One active count per zone | CNT-002 Count freezes stock | CNT-003 Variance auto-calculated | CNT-004 Large variance needs approval | CNT-005 Approved count updates inventory

Formula: `Variance = Physical Qty - System Qty`

**Valuation Rules:** VAL-001 Weighted Average default | VAL-002 FIFO future-ready | VAL-003 Historical valuation immutable | VAL-004 Landed cost updates average cost

**Reorder Rules:** REO-001 Trigger when available <= minimum | REO-002 Preferred supplier selected | REO-003 Duplicate auto PO prevented | REO-004 Safety stock configurable

**Procurement Rules:** PUR-001 Supplier active | PUR-002 PR requires line items | PUR-003 Approved PR -> PO | PUR-004 PO approval before GRN | PUR-005 Partial receipt supported | PUR-006 Duplicate invoice blocked | PUR-007 Closed PO immutable

## 9.4 Payment, Security & Admin Rules

**Payment Rules:**
- PAY-101: Cash received >= payable amount | PAY-102: Sum of split tenders = bill total
- PAY-103: Duplicate requests prevented by idempotency | PAY-104: Gateway success confirmed after callback
- PAY-105: Receipt generated only after settlement | PAY-106: Complimentary bills require manager approval
- PAY-107: Refund <= original settled amount | PAY-108: Void requires approval and audit

**Refund Rules:** REF-001 Reason mandatory | REF-002 Partial refunds supported | REF-003 Original payment reference required | REF-004 Creates immutable audit record

**Cash Drawer Rules:** CD-001 Opens only after authorized transaction | CD-002 Every open event logged | CD-003 Shift cannot close with unresolved variance | CD-004 Cash count variance above tolerance requires approval

**Shift Rules:** SHIFT-001 One active cashier shift per terminal | SHIFT-002 Opening balance mandatory | SHIFT-003 Close requires reconciliation | SHIFT-004 Pending orders block closure unless overridden

**Security Rules:**
- SEC-101: JWT mandatory | SEC-102: RBAC evaluated on every request
- SEC-103: Password complexity enforced | SEC-104: Password reuse prevented
- SEC-105: Session timeout configurable | SEC-106: Failed login threshold locks account
- SEC-107: Secrets encrypted at rest | SEC-108: Sensitive operations require audit entries

**Administration Rules:**
- ADM-101: Configuration changes versioned | ADM-102: Feature flags require publish workflow
- ADM-103: System roles cannot be deleted | ADM-104: Branch deletion prohibited when transactions exist
- ADM-105: Tax profile changes never affect historical transactions | ADM-106: Configuration rollback restores last published version

## 9.5 Reporting & Audit Rules

**Reporting Rules:**
- RPT-101: Reports respect RBAC and branch access
- RPT-102: KPI calculations use only posted transactions
- RPT-103: Cancelled orders excluded unless explicitly requested
- RPT-104: Dashboard widgets use cache when valid
- RPT-105: Long-running reports execute asynchronously
- RPT-106: Drill-down data must reconcile with summary totals

**KPI Formulas:** Gross Sales = Sum of posted sales | Net Sales = Gross - Discounts | Food Cost % = Consumption / Net Sales | Average Bill = Net Sales / Paid Bills | Refund % = Refund Amount / Net Sales

**Export Rules:** EXP-001 Formats: PDF, Excel, CSV | EXP-002 Export actions audited | EXP-003 Sensitive reports watermarked | EXP-004 Export size limits configurable

**Audit Rules:**
- AUD-101: Every financial mutation generates immutable audit record
- AUD-102: Before/after values captured for configuration changes
- AUD-103: Correlation ID stored for every API request
- AUD-104: Audit records are append-only | AUD-105: Audit timestamps use UTC

**Event Rules:** EVT-001 Every state transition publishes a domain event | EVT-002 Failed delivery retries automatically | EVT-003 Duplicate events ignored by event ID | EVT-004 Event ordering preserved within aggregate

**Retention Rules:**

| Artifact | Online | Archive |
|---|---:|---:|
| Audit Logs | 5 Years | 7 Years |
| API Logs | 90 Days | 1 Year |
| Reports | 1 Year | 5 Years |
| Events | 1 Year | 5 Years |

**Compliance Rules:** Historical financial records are immutable. PII follows retention policy. Deleted users remain referenced in audit history. Configuration changes never rewrite historical transactions.

---

# 10. Edge Cases

## 10.1 System Edge Cases

**POS Edge Cases:**
- POS-001: Two waiters open same table simultaneously -> First succeeds, second receives `409 TABLE_ALREADY_OCCUPIED`. Audit created.
- POS-002: Item deleted from menu while in draft order -> Existing draft remains editable. New additions blocked.
- POS-003: Table transfer during payment -> Transfer blocked until payment completes.
- POS-004: Bill generated twice -> Existing bill returned. Duplicate prevented.
- POS-005: Incremental KOT with no new items -> No KOT generated. Informational response returned.

**Payment Edge Cases:**
- PAY-001: Gateway timeout after authorization -> Poll gateway. Prevent duplicate. Idempotency enforced.
- PAY-002: Duplicate callback -> Ignore. Preserve original transaction.
- PAY-003: Split payment exceeds bill -> Reject. Keep payment pending.
- PAY-004: Cash drawer unavailable -> Block cash settlement. Allow electronic methods.
- PAY-005: Refund after accounting period close -> Require finance approval.

**Kitchen Edge Cases:**
- KIT-001: Chef accepts same KOT from two devices -> Optimistic locking. One acceptance succeeds.
- KIT-002: Station goes offline during preparation -> Queue retained. Supervisor alerted.
- KIT-003: Ready event published twice -> Ignore duplicate.

**Inventory Edge Cases:**
- INV-001: Negative stock requested -> Reject unless configuration allows.
- INV-002: Expired batch selected -> Block issue.
- INV-003: Transfer destination disabled -> Cancel transfer.

**Procurement Edge Cases:**
- PUR-001: Duplicate supplier invoice -> Reject posting.
- PUR-002: Partial receipt exceeds ordered quantity -> Validate against tolerance.

**Security Edge Cases:**
- SEC-001: Expired JWT -> Return 401.
- SEC-002: Permission removed during active session -> Re-evaluate RBAC immediately.

**System Edge Cases:**
- SYS-001: Server restart during transaction -> Transaction rollback. No partial commits.
- SYS-002: Clock skew across services -> Use UTC timestamps. Correlation IDs preserved.
- SYS-003: Feature flag disabled mid-session -> Respect rollout policy.

**Network Edge Cases:**
- NET-001: Internet lost during KOT publish -> Queue request. Retry after reconnect.
- NET-002: Offline POS reconnects with stale data -> Version conflict detection.

**Database Edge Cases:**
- DB-001: Deadlock detected -> Automatic retry.
- DB-002: Unique constraint violation -> Return 409 Conflict.

## 10.2 Module Edge Cases

**POS Extended:**
- POS-006: Order edited after KOT -> Existing KOT immutable. Delta KOT generated. Removed prepared items require approval.
- POS-007: Table merge while one bill is partially paid -> Reject merge. Prompt settlement before merge.
- POS-008: Split bill after receipt printed -> Manager approval required. Original receipt voided and audited.
- POS-009: Waiter session expires while saving order -> Preserve draft locally. Force re-authentication.
- POS-010: Duplicate order submission from double-click -> Idempotency key prevents duplicate orders.

**Kitchen Extended:**
- KIT-004: Station printer offline -> Queue print jobs. Continue KDS workflow.
- KIT-005: Course sequencing conflict -> Hold next course until dependency completes.
- KIT-006: Chef marks Ready before Accept -> Reject invalid state transition.
- KIT-007: KDS reconnect after outage -> Replay pending events in order.
- KIT-008: Kitchen reassignment -> Supervisor transfer only. Audit ownership change.

**Inventory Extended:**
- INV-004: Simultaneous transfers of same stock -> Optimistic locking. Recalculate availability.
- INV-005: Stock count started during transfer -> Freeze affected SKUs. Delay transfer completion.
- INV-006: Barcode duplicated -> Reject duplicate mapping.
- INV-007: FEFO batch depleted mid-pick -> Reallocate next eligible batch.
- INV-008: Valuation recalculation interrupted -> Roll back transaction. Retry background job.

**Procurement Extended:**
- PUR-003: Supplier credit limit exceeded -> Block approval or require override.
- PUR-004: GRN without PO -> Allow only if policy enabled.
- PUR-005: Invoice currency mismatch -> Validate exchange rate policy.
- PUR-006: Supplier becomes inactive during approval -> Suspend workflow pending review.
- PUR-007: Landed cost posted twice -> Detect duplicate allocation.

**Payment Extended:**
- PAY-006: Card approved, callback delayed -> Poll gateway before retry.
- PAY-007: UPI callback arrives after manual retry -> Reconcile by transaction reference.
- PAY-008: Receipt printer failure -> Mark receipt pending reprint.
- PAY-009: Shift closes during payment -> Complete transaction before closure.
- PAY-010: Refund for split tender -> Allocate refund proportionally.

**Multi-Branch Edge Cases:**
- Branch offline during sync | Duplicate order numbers across branches
- Cross-branch inventory transfer conflict | Timezone mismatch in reports | Branch disabled with active sessions

**Offline Sync Edge Cases:**
1. Local order edited after server update | 2. Duplicate offline payment upload
3. Offline inventory adjustment conflict | 4. Lost WebSocket events | 5. Device clock drift

Resolution: Version checks | Conflict log | Manual merge where required

**Performance Edge Cases:**
- 10,000 active orders | 1M ledger records | Peak lunch concurrency | Printer queue overflow | Slow analytics query
- Expected: Graceful degradation | Queueing | Timeouts with retry

**QA Matrix:**

| Area | Automated | Integration | UAT |
|---|:---:|:---:|:---:|
| POS | Yes | Yes | Yes |
| Kitchen | Yes | Yes | Yes |
| Inventory | Yes | Yes | Yes |
| Payments | Yes | Yes | Yes |
| Procurement | Yes | Yes | Yes |

---

# 11. QA Test Cases

## 11.1 Functional Test Cases

**Test Case Template:**

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

**Authentication Test Cases:**
- QA-AUTH-001 Successful Login (Critical): Open login -> Enter credentials -> Click Login. Expected: JWT generated, Dashboard displayed, Audit log created.
- QA-AUTH-002 Invalid Password: Expected: Error displayed, Login denied, Failed attempt logged.

**POS Test Cases:**
- QA-POS-001 Create Dine-In Order: Open table -> Create order -> Add items -> Save. Expected: Order number generated, Totals calculated, Draft stored. Traceability: ORD-001, `POST /orders`.
- QA-POS-002 Generate KOT: Expected: KOT created, Kitchen notified, Duplicate prevented.
- QA-POS-003 Complete Payment: Expected: Payment posted, Receipt generated, Order closed, Table released.

**Kitchen Test Cases:**
- QA-KIT-001 Accept KOT: Expected: Status = Accepted, SLA timer starts.
- QA-KIT-002 Mark Ready: Expected: Waiter notified, Duplicate ready ignored.

**Inventory Test Cases:**
- QA-INV-001 Receive Stock: Expected: Ledger updated, Stock increased, Audit entry written.
- QA-INV-002 Transfer Stock: Expected: Source decreases, Destination increases, Transfer completed.

**Procurement Test Cases:**
- QA-PUR-001 Approve PO: Expected: Status Approved, GRN enabled.
- QA-PUR-002 Duplicate Invoice: Expected: Validation error, Invoice rejected.

**Payment Test Cases:**
- QA-PAY-001 Split Tender: Expected: Sum equals bill, Receipt generated.
- QA-PAY-002 Refund: Expected: Refund posted, Audit recorded.

**Reporting Test Cases:**
- QA-RPT-001 Sales Report: Expected: Totals reconcile, Filters applied.
- QA-RPT-002 Export PDF: Expected: PDF generated, Export logged.

**Regression Suite - Critical:** Login | Order lifecycle | Payment | KOT | Inventory posting | Reporting

**Exit Criteria:** 100% Critical tests pass | No open Critical defects | High defects accepted or fixed | UAT sign-off complete

## 11.2 Integration & Regression Testing

**End-to-End Integration Flow:** Login -> Open Table -> Create Order -> Generate KOT -> Kitchen Ready -> Payment -> Inventory Update -> Reporting -> Audit

**Integration Scenarios:**
- INT-001 Complete Dine-In Journey: Order completes, KOT generated, Payment settled, Inventory consumed, Sales in reports, Audit trail complete.
- INT-002 Procurement to Inventory: Purchase Request -> PO -> GRN -> Inventory Ledger -> Stock Available. Validation: Ledger updated, Average cost recalculated, Reports refreshed.
- INT-003 Multi-Branch Sync: Branch isolation maintained, Consolidated reports accurate, Duplicate IDs prevented.

**Regression Suite:**
- Critical: Login, Order lifecycle, KOT, Billing, Split payment, Refund, Stock receipt, Stock transfer, Report generation
- High: Feature flags, Notifications, Printer routing, Tax engine

**Performance Tests:**

| Scenario | Target |
|---|---:|
| Login | <1 sec |
| Create Order | <500 ms |
| Generate KOT | <1 sec |
| Payment | <2 sec |
| Dashboard | <2 sec |

**Load Tests:** 1,000 concurrent users | 10,000 orders/day | 1M ledger records

**Security Tests:** SQL Injection | XSS | CSRF | Broken Access Control | JWT Tampering | Session Hijacking | Password Policy | Secret Exposure

**Offline & Recovery:** Offline order creation | Offline payment queue | Reconnect conflict resolution | Printer recovery | Event replay | Database failover. Expected: No data loss, Automatic reconciliation, Audit continuity.

**Defect Severity Matrix:**

| Severity | Description |
|---|---|
| Critical | Release blocker |
| High | Major business impact |
| Medium | Workaround available |
| Low | Minor usability issue |

**Release Checklist:** Critical tests pass | Regression completed | Performance targets met | Security scan passed | Audit verified | UAT approved | Rollback plan validated

---

# 12. UAT Scenarios

## 12.1 UAT Participants

| Role | Responsibility |
|---|---|
| Restaurant Owner | Business sign-off |
| Store Manager | Operational validation |
| Cashier | Billing validation |
| Waiter | Order workflow validation |
| Kitchen Supervisor | KDS validation |
| Inventory Manager | Stock validation |
| Finance | Settlement validation |
| Product Team | Issue triage |

## 12.2 End-to-End Business Journey

Login -> Open Shift -> Open Table -> Take Order -> Send KOT -> Prepare Food -> Serve -> Generate Bill -> Collect Payment -> Close Order -> Close Shift -> Reports

## 12.3 UAT Scenarios

**UAT-001 Restaurant Opening**

Objective: Verify daily opening procedures.

Preconditions: Store configured, Users active, Opening cash available.

Steps: 1. Login | 2. Open cashier shift | 3. Verify printers | 4. Verify kitchen stations | 5. Verify menu sync.

Expected Results: Shift opens successfully, Devices online, Audit entries created.

---

**UAT-002 Complete Dine-In Service**

Steps: 1. Open table | 2. Add guests | 3. Add menu items | 4. Send KOT | 5. Kitchen prepares order | 6. Serve customer | 7. Generate bill | 8. Accept payment | 9. Print receipt | 10. Close table.

Acceptance Criteria: Correct totals, Correct KOT routing, Receipt generated, Table available again.

---

**UAT-003 Split Payment**

Expected: Multiple tenders accepted, Totals reconcile, Receipt reflects tender split.

---

**UAT-004 Inventory Replenishment**

Flow: Purchase Request -> PO -> GRN -> Inventory Available.

Acceptance: Stock updated, Ledger posted, Valuation recalculated.

---

**UAT-005 Restaurant Closing**

Steps: 1. Complete pending orders | 2. Close shift | 3. Reconcile cash | 4. Generate daily reports.

Expected: No pending transactions, Variance calculated, Reports available.

## 12.4 Acceptance Matrix

| Area | Business Owner | Status |
|---|---|---|
| Authentication | IT | Pending |
| POS | Operations | Pending |
| Kitchen | Kitchen Manager | Pending |
| Inventory | Store Manager | Pending |
| Payments | Finance | Pending |
| Reporting | Management | Pending |

## 12.5 Exit Criteria

- All critical UAT scenarios passed | No Sev-1 defects
- Approved workarounds for remaining issues | Stakeholder sign-off obtained

## 12.6 Sign-off

| Stakeholder | Signature | Date |
|---|---|---|
| Product Owner | | |
| Restaurant Owner | | |
| QA Lead | | |
| Engineering Lead | | |

---

# 13. Deployment & Operations

## 13.1 Environment Strategy

| Environment | Purpose |
|---|---|
| Development | Feature development |
| QA | Functional testing |
| UAT | Business validation |
| Staging | Production rehearsal |
| Production | Live operations |

## 13.2 Deployment Architecture

```
Developer -> Git -> CI -> Artifact Registry -> Staging / Production

Production:
  API  -> PostgreSQL / Redis / EventBus
  Web
  Workers -> Object Storage
```

## 13.3 CI/CD Pipeline

1. Commit & Pull Request
2. Static Code Analysis
3. Unit Tests
4. Security Scan
5. Build Docker Images
6. Publish Artifacts
7. Deploy to QA
8. Automated Regression
9. Deploy to UAT
10. Production Approval
11. Blue/Green or Canary Deployment
12. Post-deployment Health Checks

## 13.4 Release Strategy

Semantic Versioning | Release Notes | Feature Flags | Zero-downtime deployments | Controlled rollout by branch

## 13.5 Database Migration Rules

- Versioned migrations only | Backward-compatible schema changes
- Pre-deployment backup | Rollback scripts mandatory | Data validation after migration

## 13.6 Rollback Strategy

**Triggers:** Critical defect | Performance degradation | Failed health checks | Database incompatibility

**Steps:** 1. Stop rollout | 2. Switch traffic | 3. Restore previous release | 4. Validate services | 5. Publish incident report

## 13.7 Backup & Disaster Recovery

- Nightly full backup | 15-minute WAL/incremental backups
- Cross-region backup replication | Quarterly restore drills
- **RPO:** 15 minutes | **RTO:** 60 minutes

## 13.8 Monitoring & Alerting

**Metrics:** API latency | Error rate | CPU | Memory | Database connections | Queue depth | Payment failures | KDS delays

**Alert Levels:** Critical | High | Warning | Informational

## 13.9 Security Hardening

TLS 1.3 | Secrets Manager | RBAC | MFA for admins | WAF | Audit logging | Dependency scanning | Image signing

## 13.10 Production Readiness Checklist

- [ ] Infrastructure provisioned
- [ ] Secrets configured
- [ ] SSL certificates valid
- [ ] Monitoring enabled
- [ ] Backups verified
- [ ] Load test completed
- [ ] Security scan passed
- [ ] Rollback validated
- [ ] Support runbook approved

## 13.11 Operational Runbooks

API outage | Database failover | Payment gateway outage | Printer outage | Kitchen service interruption | Cache flush | Certificate renewal | Emergency rollback

## 13.12 Operations KPIs

- Availability >= 99.9%
- Deployment success > 98%
- MTTR < 60 min
- Change failure rate < 10%
- Backup success = 100%

---

## Document Metadata

| Field | Value |
|---|---|
| Document Title | Restaurant POS & Inventory Management System - Master Product Bible |
| Version | 1.0 |
| Source Files | 43 markdown files from Restaurant-POS-Product-Bible/ |
| Modules Covered | Authentication, POS, Kitchen, Inventory, Procurement, Reporting, Administration |
| Database Tables | 70+ tables documented |
| API Endpoints | 100+ REST endpoints |
| Business Rules | 200+ rules catalogued |
| Edge Cases | 100+ scenarios documented |
| QA Test Cases | 50+ functional + integration scenarios |
| UAT Scenarios | 5 end-to-end journeys |
| Deployment | Complete CI/CD and Operations playbook |
