# 06_Screen_Specifications_02_Dashboard.md

# Restaurant POS Enterprise Product Bible
## Screen Specification – Dashboard

**Screen ID:** SCR-DASH-001

---

# 1. Purpose

The Dashboard is the operational home screen after authentication. It provides real-time KPIs, shortcuts, alerts, pending approvals, and operational visibility based on the logged-in user's role.

---

# 2. Supported Roles

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

---

# 3. Business Goals

- Display restaurant health in real time.
- Reduce navigation time.
- Surface actionable alerts.
- Enable one-click access to daily operations.

---

# 4. Wireframe

```text
+----------------------------------------------------------------------------------+
| Logo | Branch | Date | Shift | Notifications | Profile                           |
+----------------------------------------------------------------------------------+
| Today's Sales | Orders | Avg Bill | Open Tables | Pending KOT | Inventory Alerts |
+----------------------------------------------------------------------------------+
| Quick Actions                                                           |
| [New Dine-In] [Takeaway] [Purchase] [Stock Count] [Reports]             |
+----------------------------------------------------------------------------------+
| Charts: Sales Trend | Payment Mix | Top Selling Items | Hourly Orders            |
+----------------------------------------------------------------------------------+
| Alerts & Approvals                                                      |
| - Refund Approval                                                       |
| - Low Stock                                                             |
| - Cash Difference                                                       |
+----------------------------------------------------------------------------------+
```

---

# 5. Widgets

## KPI Cards
- Gross Sales
- Net Sales
- Orders Today
- Average Order Value
- Open Tables
- Pending Kitchen Orders
- Inventory Variance
- Low Stock Items

## Charts
- Hourly Sales
- Payment Mode Distribution
- Top Selling Categories
- Top Selling Items
- Inventory Consumption
- Purchase Trend

## Quick Actions
- Start Dine-In
- Quick Billing
- Takeaway
- Purchase Entry
- Stock Count
- Reports

---

# 6. Business Logic

1. Load dashboard based on user role.
2. Fetch branch-specific metrics.
3. Display today's operational KPIs.
4. Refresh every 30 seconds (configurable).
5. Display pending approvals.
6. Highlight critical alerts.

---

# 7. Role Specific Views

### Owner
- Revenue
- GP
- Multi-branch comparison
- Top branches
- Pending approvals

### Manager
- Open Tables
- Kitchen SLA
- Refund Queue
- Shift Sales
- Staff Performance

### Cashier
- Current Shift
- Today's Bills
- Drawer Balance
- Refund Requests

### Waiter
- Assigned Tables
- Ready Orders
- Pending Bills

### Kitchen
- New KOT
- Preparing
- Ready
- Delayed Orders

### Inventory
- Low Stock
- Today's Purchases
- Pending Count
- Variance

---

# 8. Validation Rules

- Dashboard filtered by branch.
- User only sees authorized widgets.
- Archived data excluded from live KPIs.
- Cached data marked with last refresh time.

---

# 9. APIs

## GET /dashboard/summary
Returns KPI cards.

## GET /dashboard/charts
Returns graph datasets.

## GET /dashboard/alerts
Returns notifications.

## GET /dashboard/approvals
Returns pending approvals.

---

# 10. Database Tables

- orders
- order_items
- payments
- tables
- shifts
- stock_counts
- purchases
- audit_logs
- notifications

---

# 11. Events

DashboardOpened
DashboardRefreshed
AlertAcknowledged
ApprovalOpened

---

# 12. Notifications

- Low stock
- High refund
- Failed payment
- Kitchen backlog
- Cash mismatch
- Printer offline

---

# 13. Edge Cases

- No sales today.
- No active shift.
- Branch offline.
- Dashboard API timeout.
- Empty charts.
- Delayed synchronization.
- Permission removed during session.

---

# 14. Performance

- Initial load <3 seconds.
- Widget refresh <1 second.
- Cached fallback when reporting service unavailable.

---

# 15. Acceptance Criteria

- Correct dashboard shown by role.
- KPIs match reports.
- Alerts displayed within refresh interval.
- Quick actions open target screens.
- Unauthorized widgets hidden.
- Dashboard remains usable with partial service failures.

---

# Next Screen

06_Screen_Specifications_03_POS_Quick_Billing.md

This screen will include complete billing logic, UI layout, pricing engine, discount engine, KOT generation, taxation, payment orchestration, validation rules, APIs, database interactions, business rules, and edge cases.
