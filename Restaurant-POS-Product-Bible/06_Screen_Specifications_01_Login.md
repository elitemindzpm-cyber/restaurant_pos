# 06_Screen_Specifications_01_Login.md

# Restaurant POS Enterprise Product Bible
## Screen Specification - Login

**Screen ID:** SCR-AUTH-001

---

# 1. Purpose

The Login screen is the secure entry point into the Restaurant POS platform. It authenticates users, identifies their role, loads branch-specific configuration, restores any active session context, and routes them to the appropriate landing page.

---

# 2. Actors

- Owner
- Restaurant Manager
- Cashier
- Waiter
- Kitchen Staff
- Stock Controller
- Accountant
- System Administrator

---

# 3. Entry Points

- Application launch
- Session timeout
- Manual logout
- Device restart
- Browser refresh (expired session)

---

# 4. Exit Points

Successful Login
→ Dashboard

Session Locked
→ Unlock Screen

Authentication Failure
→ Retry

Forgot Password
→ Reset Password

---

# 5. Wireframe (Low Fidelity)

```text
+------------------------------------------------------+
| Restaurant Logo                                      |
|------------------------------------------------------|
| Username / Employee ID                               |
| PIN / Password                                       |
| Branch (Auto / Dropdown)                             |
| Remember Device []                                   |
|                                                      |
| [ Login ]                                            |
|                                                      |
| Forgot Password?                                     |
| Version | Environment                                |
+------------------------------------------------------+
```

---

# 6. UI Components

| Component | Type | Mandatory |
|----------|------|-----------|
| Username | Text | Yes |
| Password/PIN | Password | Yes |
| Branch | Dropdown | Configurable |
| Login Button | Primary CTA | Yes |
| Forgot Password | Link | Optional |

---

# 7. Business Logic

1. Validate username.
2. Validate password/PIN.
3. Verify user is active.
4. Verify assigned branch.
5. Load role permissions.
6. Load restaurant configuration.
7. Redirect to default landing page.
8. Record audit event.

---

# 8. Validation Rules

- Username mandatory.
- Password mandatory.
- Password masked.
- Maximum 5 failed attempts.
- Locked account after configurable threshold.
- Inactive users cannot login.
- Disabled branches cannot be selected.

---

# 9. Role Routing

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

---

# 10. APIs

## POST /auth/login

Request

```json
{
  "username":"cashier01",
  "password":"********",
  "branchId":1
}
```

Success

```json
{
  "token":"jwt",
  "role":"Cashier",
  "landingPage":"/pos"
}
```

Errors
- 401 Invalid credentials
- 403 User disabled
- 423 Account locked

---

# 11. Database Tables

- users
- roles
- branches
- user_branch
- user_sessions
- audit_logs

---

# 12. Permission Checks

- User Active
- Role Active
- Branch Access
- Device Allowed
- Session Limit

---

# 13. Notifications

- Failed login alert (optional)
- Password expiry warning
- New device login

---

# 14. Audit Events

- Login Success
- Login Failure
- Logout
- Session Expired
- Account Locked

---

# 15. Error States

- Invalid credentials
- Server unavailable
- Branch disabled
- Network offline
- Session expired

Recovery:
Retry → Offline cache (if enabled) → Contact administrator

---

# 16. Accessibility

- Keyboard navigation
- Screen reader labels
- High contrast
- Focus indicators
- Minimum touch target 44px

---

# 17. Acceptance Criteria

- Login completes in <2 seconds.
- JWT issued on success.
- RBAC loaded correctly.
- Landing page determined by role.
- All attempts audited.
- Sensitive data never logged.

---

# Related Documents

- 01_Product_Vision_and_Requirements.md
- 02_User_Personas.md
- 03_Information_Architecture.md
- 04_Navigation_Hierarchy.md
- 05_User_Journeys.md

Next Screen:
06_Screen_Specifications_02_Dashboard.md
