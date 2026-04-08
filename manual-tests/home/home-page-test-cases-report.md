# Test Cases Report — Home Page
**Module:** Home
**Source Context File:** `page-contexts/home.context.md`
**Explored Role:** System Admin (MODERNIZED_SYSTEM_ADMIN_USEREMAIL)
**Generated On:** 2026-04-08
**Total Test Cases:** 24 (BEW-T7100 to BEW-T7123)

---

## Folder Structure

```
/BE-Modernisation/Frontend/Home/
├── Authentication/          (6 TCs)  — Login, Sign Out, Post-login alerts
├── Navigation/              (4 TCs)  — Sidebar nav items, Site Settings, Back to Platform, Workgroup
├── Widgets/
│   ├── Welcome/             (2 TCs)  — Welcome widget display, Edit link
│   └── Meetings/            (3 TCs)  — Empty state, Open calendar, Export all
├── Search/                  (1 TC)   — Keyword search bar
├── Approvals Drawer/        (3 TCs)  — Drawer open, Response Required, Manage link
├── Top System Bar/          (4 TCs)  — Profile dropdown, Notifications, PAI Search, Help button
└── Footer/                  (1 TC)   — Privacy Policy link
```

---

## Authentication — 6 Test Cases

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7100 | Verify successful login as System Admin redirects to Home page | High | MOD-P0-Automation | E2E, Functional, Smoke |
| BEW-T7101 | Verify login fails and error is shown when incorrect password is entered | Normal | MOD-P1-Automation | Functional, Regression |
| BEW-T7102 | Verify login fails when the email field is left empty | Normal | MOD-P1-Automation | Functional |
| BEW-T7115 | Verify 'Sign Out' successfully logs out the user and redirects to the login page | High | MOD-P0-Automation | E2E, Functional, Smoke |
| BEW-T7116 | Verify login success info alert is displayed on the Home page immediately after login | Normal | MOD-P1-Automation | Functional, UI/UX |
| BEW-T7117 | Verify login success info alert can be dismissed via the Dismiss button | Low | MOD-P1-Automation | Functional, UI/UX |

---

## Navigation — 4 Test Cases

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7103 | Verify all expected left sidebar navigation items are visible for System Admin | High | MOD-P0-Automation | Functional, Smoke |
| BEW-T7104 | Verify 'Site Settings' sidebar link is visible and navigates to Site Settings for System Admin | High | MOD-P0-Automation | Security, Functional |
| BEW-T7118 | Verify 'Back to Platform' link navigates to the Diligent One Platform home | Normal | MOD-P1-Automation | Functional |
| BEW-T7120 | Verify 'UI-Workgroup' link is visible in the sidebar Workgroup section for workgroup members | Normal | MOD-P1-Automation | Functional |

---

## Widgets / Welcome — 2 Test Cases

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7105 | Verify Welcome widget is displayed and in expanded state by default on the Home page | Normal | MOD-P1-Automation | Functional, UI/UX |
| BEW-T7106 | Verify 'Edit' link on Welcome widget is visible to System Admin and navigates to Welcome Edit page | High | MOD-P0-Automation | Security, Functional |

---

## Widgets / Meetings — 3 Test Cases

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7107 | Verify Meetings widget displays empty state message when no meetings are scheduled | Normal | MOD-P1-Automation | Functional, UI/UX |
| BEW-T7108 | Verify 'Open calendar' link in Meetings widget navigates to the calendar page | Normal | MOD-P1-Automation | Functional |
| BEW-T7109 | Verify 'Export all' button in Meetings widget is enabled and triggers an export | Low | MOD-Manual-Only | Functional |

---

## Search — 1 Test Case

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7110 | Verify keyword search can be submitted from the Home page search bar | Normal | MOD-P1-Automation | Functional |

---

## Approvals Drawer — 3 Test Cases

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7111 | Verify Approvals drawer opens and displays pending approval items | High | MOD-P0-Automation | Functional, E2E |
| BEW-T7112 | Verify 'Response Required' link in Approvals drawer navigates to the approval respond page | High | MOD-P0-Automation | Functional, E2E |
| BEW-T7113 | Verify 'Manage' link in Approvals drawer navigates to the approvals manage page | Normal | MOD-P1-Automation | Functional |

---

## Top System Bar — 4 Test Cases

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7114 | Verify profile dropdown opens and displays correct user information and navigation options | High | MOD-P0-Automation | Functional, Smoke |
| BEW-T7119 | Verify notifications bell with unread count badge is visible in the top system bar | Normal | MOD-P1-Automation | Functional, UI/UX |
| BEW-T7121 | Verify PAI Search element is visible and accessible in the top system bar | Normal | MOD-P1-Automation | Functional, UI/UX |
| BEW-T7122 | Verify 'Overflow Menu Help' button is accessible in the top system bar | Low | MOD-P1-Automation | Functional, UI/UX |

---

## Footer — 1 Test Case

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T7123 | Verify Privacy Policy link at the bottom of the Home page navigates to the privacy policy page | Low | MOD-Manual-Only | Functional, UI/UX |

---

## Summary by Priority

| Priority | Count | Test Case Keys |
|---|---|---|
| High | 8 | T7100, T7103, T7104, T7106, T7111, T7112, T7114, T7115 |
| Normal | 12 | T7101, T7102, T7105, T7107, T7108, T7110, T7113, T7116, T7118, T7119, T7120, T7121 |
| Low | 4 | T7109, T7117, T7122, T7123 |

---

## Summary by Automation Plan

| Plan | Count | Purpose |
|---|---|---|
| MOD-P0-Automation | 8 | Critical smoke/E2E — must automate first |
| MOD-P1-Automation | 14 | Standard functional — automate in next sprint |
| MOD-Manual-Only | 2 | Visual/file-download — manual only |

---

## Summary by Testing Type

| Type | Count |
|---|---|
| Functional | 24 (all) |
| E2E | 4 (T7100, T7111, T7112, T7115) |
| Smoke | 4 (T7100, T7103, T7114, T7115) |
| Security | 2 (T7104, T7106) |
| Regression | 1 (T7101) |
| UI/UX | 9 (T7105, T7107, T7109, T7116, T7117, T7119, T7121, T7122, T7123) |

---

## Coverage Gaps (Roles Not Yet Explored)

The following role-based tests **cannot be generated yet** because only System Admin has been explored. Run the Explorer Agent for each role to unlock these tests:

| Missing Test Area | Roles Needed |
|---|---|
| Verify 'Site Settings' is NOT visible for non-admin users | Workroom Admin, Board Members |
| Verify 'Edit' on Welcome widget is NOT visible for non-admin users | Workroom Admin, Board Members |
| Verify Directory URL path differs for non-admin roles | All Board Members |
| Verify Approvals badge count is 0 for users with no pending approvals | Board Members with no approvals |
| Verify 'UI-Workgroup' is NOT visible for users not in the workgroup | MODERNIZED_WORKROOM_ADMIN_NOT_PART_OF_UI_WORKROOM, MODERNIZED_BOARD_MEMBER_NOT_PART_OF_UI_WORKROOM |
| Verify 'Manage' link in Approvals drawer may not be visible for non-admin roles | Workroom Admin, Board Members |
