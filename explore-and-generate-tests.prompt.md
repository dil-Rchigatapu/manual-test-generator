---
mode: agent
description: Explores the BoardEffect (Diligent One Platform) website using Playwright MCP and generates manual test cases in CSV format.
tools:
  - playwright
---

# BoardEffect Manual Test Case Generator

## Credentials & Config
Load the following from `.env.dev` before starting:
- `MODERNIZED_APPLICATION_URL` → the base URL to navigate to
- `MODERNIZED_SYSTEM_ADMIN_USEREMAIL` → login email
- `MODERNIZED_PASSWORD` → login password

---

## Phase 1 — Login & Landing Page

1. Navigate to `MODERNIZED_APPLICATION_URL`.
2. Observe the login page: note all visible UI elements (fields, buttons, links, error states).
3. Log in using `MODERNIZED_SYSTEM_ADMIN_USEREMAIL` and `MODERNIZED_PASSWORD`.
4. After login, screenshot the dashboard/home page.
5. Document: page title, top navigation items, left sidebar items, any widgets or panels present.

---

## Phase 2 — Systematic Module Exploration

For **each top-level navigation item and sidebar module**, repeat these steps:

1. Click on the module/nav item.
2. Note the URL path.
3. List all sub-navigation items, tabs, or secondary menus visible.
4. Identify all interactive UI elements: buttons, forms, dropdowns, search bars, tables, modals.
5. Identify key user actions available on the page (create, edit, delete, filter, export, upload, etc.).
6. Note any role indicators or permission-gated elements.
7. Navigate one level deeper into any sub-modules and repeat.

Modules to cover (based on BoardEffect domain knowledge — verify against actual nav):
- **Dashboard / Home**
- **Meetings** (create, view, agenda builder, board book, minutes, attendance)
- **Workrooms / Committees** (create workroom, manage members, documents, discussions)
- **Documents / File Library** (upload, organize, version control, access control)
- **Users / Members** (invite, roles, permissions, profile management)
- **Calendar** (create events, meeting scheduler)
- **Surveys / Polls** (create survey, distribute, view results)
- **Action Items / Tasks** (create, assign, track, complete)
- **Settings / Administration** (org settings, branding, SSO, integrations, notifications)
- **Profile / Account** (personal profile, password change, notification preferences)
- **Logout flow**

---

## Phase 3 — Generate Test Cases

After exploring all modules, generate test cases following these rules:

### Test Case Writing Rules
- Each test case must be **self-contained** — a fresh reader or AI agent must understand it without prior context.
- **Test steps** must be crisp, action-verb-first sentences (e.g., `Click 'Create Book' button`, `Enter meeting title in 'Title' field`).
- Steps must reference **exact UI labels** as they appear on screen (e.g., `Click 'Upload' button in footer of sidesheet`).
- **Expected results** must be observable and specific (e.g., `Green toaster message appears with text 'Book uploaded'`).
- **Test data** is either a specific value (e.g., `Title: "Q4 Board Meeting Book"`) or `none` if no data input is required.
- Cover: Happy path, critical negative scenarios, boundary conditions, permission/role checks.

### Priority Values
- `Normal` — standard functional coverage
- `High` — critical flows, smoke-worthy
- `Low` — edge cases, cosmetic

### MOD_Automation_Plan Values
- `MOD-P0-Automation` — must automate (critical)
- `MOD-P1-Automation` — automate next sprint
- `MOD-Manual-Only` — manual only

### MOD_Testing_Type Values
Use comma-separated combination as needed: `Functional`, `E2E`, `Regression`, `Smoke`, `Security`, `UI/UX`

---

### CSV Output Format — EXACT STRUCTURE (CRITICAL)

Write all test cases to `test-cases.csv`. The CSV has **22 columns** exactly matching this header:

```
Key,Name,Status,Precondition,Objective,Folder,Priority,Component,Labels,Owner,Estimated Time,Coverage (Issues),Coverage (Pages),MOD_Automation_Plan,MOD_Module (Frontend),MOD_SubModule (Frontend),MOD_Testing_Type (Frontend),Test Script (Step-by-Step) - Step,Test Script (Step-by-Step) - Test Data,Test Script (Step-by-Step) - Expected Result,Test Script (Plain Text),Test Script (BDD)
```

### STEP-PER-ROW FORMAT (CRITICAL — follow exactly)

Each test case spans **multiple CSV rows** — one row per test step:

- **Row 1 of a test case**: Fill ALL metadata columns + the first step's Step / Test Data / Expected Result columns.
- **Rows 2+ of the same test case**: Leave ALL metadata columns **empty**. Fill ONLY the three step columns: `Test Script (Step-by-Step) - Step`, `Test Script (Step-by-Step) - Test Data`, `Test Script (Step-by-Step) - Expected Result`.
- A **new test case begins** when the `Key` column has a value again.

### Column Filling Guide

| Column | Rule |
|---|---|
| `Key` | `BEW-T<auto-increment number starting from 7100>`. Only on row 1 of each test case. |
| `Name` | `MOD-<Module>-<SubModule>-<TestingType>: <short description of what is verified>` |
| `Status` | `Draft` |
| `Precondition` | What must be true before the test starts (user logged in, data exists, etc.) |
| `Objective` | One sentence: what success looks like at the end of this test |
| `Folder` | `/BE-Modernisation/Frontend/<Module>/<SubModule>` |
| `Priority` | `Normal`, `High`, or `Low` |
| `Component` | Leave blank |
| `Labels` | `MOD_New` |
| `Owner` | Leave blank |
| `Estimated Time` | Leave blank |
| `Coverage (Issues)` | Leave blank |
| `Coverage (Pages)` | Leave blank |
| `MOD_Automation_Plan` | `MOD-P0-Automation`, `MOD-P1-Automation`, or `MOD-Manual-Only` |
| `MOD_Module (Frontend)` | Top-level module name (e.g., `Meeting Books`, `Login`, `Workrooms`) |
| `MOD_SubModule (Frontend)` | Sub-module or feature name (e.g., `Create Books`, `Authentication`) |
| `MOD_Testing_Type (Frontend)` | Comma-separated types (e.g., `E2E,Functional`) |
| `Test Script (Step-by-Step) - Step` | Plain English action, action-verb first. Exact UI label in single quotes. No locators here — keep it human-readable. |
| `Test Script (Step-by-Step) - Test Data` | `locator: <locator> \| value: <input or none>` — carries the element locator AND the input value together. For navigation steps with no element interaction, write `none`. |
| `Test Script (Step-by-Step) - Expected Result` | Observable, specific outcome of this step — exact message text for toasts/errors |
| `Test Script (Plain Text)` | Leave blank |
| `Test Script (BDD)` | Leave blank |

### Example (2 test cases, showing step-per-row layout)

```
Key,Name,Status,Precondition,Objective,Folder,Priority,Component,Labels,Owner,Estimated Time,Coverage (Issues),Coverage (Pages),MOD_Automation_Plan,MOD_Module (Frontend),MOD_SubModule (Frontend),MOD_Testing_Type (Frontend),Test Script (Step-by-Step) - Step,Test Script (Step-by-Step) - Test Data,Test Script (Step-by-Step) - Expected Result,Test Script (Plain Text),Test Script (BDD)
BEW-T7100,MOD-Login-Authentication-Functional: Verify successful login with valid credentials,Draft,User has valid system admin credentials,To verify user can log in successfully and is redirected to the dashboard,/BE-Modernisation/Frontend/Login/Authentication,High,,MOD_New,,,,,MOD-P0-Automation,Login,Authentication,"E2E,Functional",Navigate to [MODERNIZED_APPLICATION_URL],none,Login page is displayed with 'Email' and 'Password' input fields,,
,,,,,,,,,,,,,,,,,Enter login email in the 'Email' field,"locator: aria-label=""Email"" | value: [MODERNIZED_SYSTEM_ADMIN_USEREMAIL]",Email value is populated in the field,,
,,,,,,,,,,,,,,,,,Enter password in the 'Password' field,"locator: aria-label=""Password"" | value: [MODERNIZED_PASSWORD]",Password is entered and masked with dots,,
,,,,,,,,,,,,,,,,,Click 'Sign in' button,"locator: role=button name=""Sign in"" | value: none",User is redirected to the dashboard homepage,,
BEW-T7101,MOD-Login-Authentication-Functional: Verify login fails with invalid password,Draft,User is on the login page,To verify an appropriate error message is shown when an incorrect password is entered,/BE-Modernisation/Frontend/Login/Authentication,Normal,,MOD_New,,,,,MOD-P1-Automation,Login,Authentication,Functional,Navigate to [MODERNIZED_APPLICATION_URL],none,Login page is displayed with 'Email' and 'Password' input fields,,
,,,,,,,,,,,,,,,,,Enter login email in the 'Email' field,"locator: aria-label=""Email"" | value: [MODERNIZED_SYSTEM_ADMIN_USEREMAIL]",Email value is populated in the field,,
,,,,,,,,,,,,,,,,,Enter an incorrect password in the 'Password' field,"locator: aria-label=""Password"" | value: WrongPassword999",Password is entered in the field,,
,,,,,,,,,,,,,,,,,Click 'Sign in' button,"locator: role=button name=""Sign in"" | value: none","Error message 'Invalid email or password' is displayed. User remains on the login page.",,
```

---

## Phase 4 — Update CONTEXT.md

After generating all test cases, append a summary to `CONTEXT.md`:
- Total test cases generated
- Modules covered
- Any pages that required authentication escalation or were inaccessible
- Any UI anomalies or unexpected behaviors observed during exploration

---

## Important Constraints
- Do NOT use hardcoded credentials in test steps — reference them as `[SYSTEM_ADMIN_EMAIL]` and `[PASSWORD]` placeholders.
- If a page requires a specific workroom, use `UI_WORKROOM_NAME` = `'UI Workroom'` (ID: `5732`) from `.env.dev`.
- If a page redirects or requires MFA/SSO, note it and skip — do not attempt to bypass.
- Capture screenshots at each major module landing page and save as `screenshots/<module-name>.png`.
