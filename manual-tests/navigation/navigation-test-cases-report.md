# Test Cases Report — Navigation
**Module:** Navigation
**Source Context File:** `page-contexts/home-navigation.context.md`
**Explored Role:** System Admin (MODERNIZED_SYSTEM_ADMIN_USEREMAIL)
**Generated On:** 2026-04-08
**Total Test Cases:** 5 (BEW-T10535, BEW-T10536, BEW-T10537, BEW-T10538, BEW-T10541)
**Improved From:** `bad_cases/P0-meetingbooks-navigation.xlsx`

---

## Folder Structure

```
/BE-Modernisation/Frontend/Navigation/
└── Side Navigation/     (5 TCs) — Back to platform, Home, Workrooms, Library, Minutes
```

---

## Side Navigation — 5 Test Cases

| Key | Test Case Name | Priority | Automation Plan | Type |
|---|---|---|---|---|
| BEW-T10535 | Verify 'Back to platform' link redirects user to the Diligent One Platform launchpad | Normal | MOD-P0-Automation | Functional |
| BEW-T10536 | Verify 'Home' link navigates to the Home page and displays the 'Home' h1 heading | Normal | MOD-P0-Automation | Functional |
| BEW-T10537 | Verify Workrooms accordion expands and collapses with workgroup and workroom drill-down | Normal | MOD-P0-Automation | Functional |
| BEW-T10538 | Verify 'Library' navigates to the Library page and is hidden for a user with Deactivate Resources permission | Normal | MOD-P0-Automation | Functional, Security |
| BEW-T10541 | Verify 'Minutes' link opens the Minutes application in a new browser tab | Normal | MOD-P0-Automation | Functional |

---

## Summary by Priority

| Priority | Count | Test Case Keys |
|---|---|---|
| Normal | 5 | T10535, T10536, T10537, T10538, T10541 |

---

## Summary by Automation Plan

| Plan | Count | Purpose |
|---|---|---|
| MOD-P0-Automation | 5 | All critical smoke/navigation — must automate first |

---

## Summary by Testing Type

| Type | Count |
|---|---|
| Functional | 5 (all) |
| Security | 1 (T10538 — Library hidden for Deactivate Resources user) |

---

## What Was Improved Over bad_cases/

| Issue in Original | Fix Applied |
|---|---|
| No locators on any step | Every step has `locator: role=... \| value: ...` in Test Data column |
| Vague "Login to application" single step | 3 explicit login steps with exact OIDC locators |
| "Workrooms" treated as a link | Correctly documented as `role=button name="Workrooms"` (accordion button, not a link) |
| Active state described as "red background highlight" with no locator | Correctly uses `role=treeitem name="UI-Workroom" selected` ([selected] attribute) |
| "Navigate to any workroom page" — no specific target | Specific: `UI-Workroom` at `/workrooms/5732` with full URL |
| T10538 requires dynamic permission change mid-test | Uses `MODERNIZED_BOARD_MEMBER_14_USEREMAIL` which has Resources deactivated natively — no setup step needed |
| Minutes tab behavior vague — "verify new tab opens" | Exact new tab URL: `https://minutes-orca01.diligentdatasystems.com/api/launch` and explicit original tab assertion |
| "Logout" step had no locator | `role=button name="Open profile menu"` → `role=link name="Sign Out"` |

---

## Coverage Gaps (Roles Not Yet Explored)

| Missing Test Area | Roles Needed |
|---|---|
| Verify 'Site settings' is NOT visible for non-admin users | Workroom Admin, Board Members |
| Verify 'Workrooms' tree shows only workrooms the user is a member of | Board Members not in UI-Workgroup |
| Verify 'Library' is hidden for Board Member 7 (no library access permissions) | MODERNIZED_BOARD_MEMBER_7_USEREMAIL |
| Verify Messaging opens the message modal correctly | All roles |
| Verify Directory URL is correct per role (admin vs non-admin) | Workroom Admin, Board Members |
