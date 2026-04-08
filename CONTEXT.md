# Manual Test Agent — Context & Methodology

> **Purpose:** This repo uses a VS Code AI agent with the Playwright MCP server to autonomously explore the BoardEffect (Diligent One Platform) web application, understand its UI/navigation, and generate crisp manual test cases into `test-cases.csv`. This document lets any AI agent or human contributor quickly onboard and replicate the same flow.

---

## 1. Target Application

| Property        | Value                                                                 |
|-----------------|-----------------------------------------------------------------------|
| Application     | BoardEffect by Diligent (board governance & management platform)      |
| Base URL        | Stored in `.env.dev` → `MODERNIZED_APPLICATION_URL`                  |
| Auth Type       | Username + Password via Diligent One Platform OIDC SSO               |
| Login URL       | `<MODERNIZED_APPLICATION_URL>/login` (redirects to OIDC provider)    |
| Test Admin User | `MODERNIZED_SYSTEM_ADMIN_USEREMAIL` in `.env.dev`                    |
| Password        | `MODERNIZED_PASSWORD` in `.env.dev`                                  |
| Test Workroom   | `UI Workroom` (ID: `5732`) — pre-existing workroom for UI test data  |

**Never commit `.env.dev` to version control.** Credentials are local-only.

---

## 2. Toolchain

| Tool                  | Purpose                                                        |
|-----------------------|----------------------------------------------------------------|
| VS Code Agent Mode    | Orchestrates the exploration + test generation flow            |
| Playwright MCP Server | Acts as a **browser** for the Explorer Agent — navigate, click, screenshot. It does NOT generate test code. |
| `@playwright/mcp`     | The npm package powering the MCP browser (runs via npx) |

> **Important:** Playwright MCP is used here purely as a remote-controlled browser to visit and observe the application. No Playwright test scripts, no code files, and no automation framework files are ever generated. The deliverables from this system are per-submodule CSVs of plain-English manual test steps, stored under `manual-tests/<module>/<submodule>/<submodule>-test-cases.csv`.

### Playwright MCP Config
Located at `.vscode/mcp.json`. Registers the Playwright MCP server so VS Code agent mode can invoke browser tools (`playwright_navigate`, `playwright_click`, `playwright_fill`, `playwright_screenshot`, etc.).

```json
{
  "servers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--headless"]
    }
  }
}
```

To use **headed mode** (see the browser), remove `"--headless"` from the args.

---

## 3. Multi-Agent Architecture

The system uses **two specialized agents** that operate independently in sequence. The site is too large to explore and generate tests in one shot — so exploration and test generation are decoupled.

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER (YOU)                               │
│   "Explore Meeting Books page"  →  "Create tests for Create Book"│
└────────────────┬────────────────────────────────┬───────────────┘
                 │                                │
                 ▼                                ▼
  ┌──────────────────────────┐    ┌──────────────────────────────┐
  │   01 — EXPLORER AGENT    │    │  02 — TEST GENERATOR AGENT   │
  │  agents/01-explorer.     │    │  agents/02-test-generator.   │
  │  prompt.md               │    │  prompt.md                   │
  │                          │    │                              │
  │  • Uses Playwright MCP   │    │  • No browser needed         │
  │  • Logs in, navigates    │    │  • Reads context files only  │
  │  • Checks all roles      │    │  • Writes per-submodule CSV  │
  │  • Saves .context.md     │    │  • Full coverage per scope   │
  └──────────┬───────────────┘    └──────────────────────────────┘
             │                                    ▲
             ▼                                    │
  ┌──────────────────────────┐                   │
  │    page-contexts/        │ ──────────────────┘
  │  <page>.context.md       │  (Test Generator reads these)
  │  INDEX.md                │
  │  _SCHEMA.md              │
  └──────────────────────────┘
```

### When to use which agent

| Situation | Agent to Run |
|---|---|
| You want to explore a new page/feature | `agents/01-explorer.prompt.md` |
| You want to create test cases for an already-explored page | `agents/02-test-generator.prompt.md` |
| You want to re-explore a page after a UI change | `agents/01-explorer.prompt.md` (will overwrite context file) |
| Page hasn't been explored yet and you ask for tests | Test Generator will stop and tell you to explore first |

---

## 4. How to Use Each Agent

### Exploring a New Page
1. Open VS Code Chat in **Agent mode**.
2. Type `#agents/01-explorer.prompt.md` to reference the agent prompt.
3. Tell it which page to explore and which roles to check. Examples:
   - *"Explore the Meeting Books > Create Book page. Check System Admin and Board Member 7."*
   - *"Explore the Login page for all roles."*
4. The agent logs in, navigates, captures all UI details, saves a `.context.md` file.

### Generating Test Cases
1. Open VS Code Chat in **Agent mode**.
2. Type `#agents/02-test-generator.prompt.md` to reference the agent prompt.
3. Tell it what to generate. Examples:
   - *"Generate full test coverage for `page-contexts/meeting-books-create.context.md`. Start from BEW-T7100."*
   - *"Generate only role-based access tests for `page-contexts/login.context.md`."*
   - *"Generate negative/validation tests for the Upload sidesheet in `page-contexts/meeting-books-create.context.md`."*
4. Test cases are written to the correct per-submodule CSV under `manual-tests/<module>/<submodule>/<submodule>-test-cases.csv`.

---

## 4a. Repo File Structure

```
manual-test-agent/
├── .env.dev                          # Local credentials (NEVER commit)
├── .vscode/
│   └── mcp.json                      # Playwright MCP server registration
│
├── agents/
│   ├── 01-explorer.prompt.md         # Explorer Agent — explores pages, saves context
│   └── 02-test-generator.prompt.md   # Test Generator Agent — reads context, writes CSV
│
├── page-contexts/                    # Page Context store (like a POM for AI agents)
│   ├── _SCHEMA.md                    # Template / schema all context files must follow
│   ├── INDEX.md                      # Index of all explored pages
│   └── <module>-<submodule>.context.md  # One file per explored page/feature
│
├── screenshots/                      # Screenshots captured by Explorer Agent
│   └── <context-filename>[-role].png
│
├── manual-tests/                     # OUTPUT: all generated test artifacts
│   └── <module>/                     #   e.g. home/
│       ├── <module>-test-cases-report.md  # Report for this module (lives here, not in reports/)
│       └── <submodule>/              #         authentication/, navigation/, widgets/welcome/ ...
│           └── <submodule>-test-cases.csv  # e.g. authentication-test-cases.csv
├── explore-and-generate-tests.prompt.md  # Legacy single-shot prompt (kept for reference)
└── CONTEXT.md                        # This file
```

---

## 5. Application Domain Knowledge

BoardEffect is a **board governance & management SaaS** platform. Key modules to test:

| Module            | Key Actions                                                           |
|-------------------|-----------------------------------------------------------------------|
| Login / Auth      | Email+password login, SSO, forgot password, invalid credentials       |
| Dashboard / Home  | Widget display, navigation shortcuts, notifications                    |
| Meetings          | Create/edit/delete meeting, agenda builder (drag-drop), board book, meeting minutes, attendance tracking, video conferencing |
| Workrooms         | Create workroom, add/remove members, manage documents, discussions    |
| Documents         | Upload, organize folders, version control, set access permissions     |
| Users / Members   | Invite user, assign roles, deactivate user, resend invite             |
| Calendar          | Create event, scheduler/availability coordination                     |
| Surveys / Polls   | Create survey, set respondents, view responses                        |
| Action Items      | Create task, assign to member, set due date, mark complete            |
| Settings / Admin  | Org settings, branding, SSO config, integrations, email notifications |
| Profile           | Edit profile, change password, notification preferences               |

---

## 5a. Test User Roles (from `.env.dev`)

| Role | Env Var (email) | Key Permission Difference |
|---|---|---|
| System Admin | `MODERNIZED_SYSTEM_ADMIN_USEREMAIL` | Full system access |
| Workroom Admin | `MODERNIZED_WORKROOM_ADMIN_USEREMAIL` | Manages workroom settings & members |
| Board Member 1 | `MODERNIZED_BOARD_MEMBER_1_USEREMAIL` | Full view access to all content |
| Board Member 2 | `MODERNIZED_BOARD_MEMBER_2_USEREMAIL` | No access to archived books |
| Board Member 3 | `MODERNIZED_BOARD_MEMBER_3_USEREMAIL` | No event access permissions |
| Board Member 4 | `MODERNIZED_BOARD_MEMBER_4_USEREMAIL` | Has event management access |
| Board Member 5 | `MODERNIZED_BOARD_MEMBER_5_USEREMAIL` | No collaborate access permissions |
| Board Member 6 | `MODERNIZED_BOARD_MEMBER_6_USEREMAIL` | Has collaborate management access |
| Board Member 7 | `MODERNIZED_BOARD_MEMBER_7_USEREMAIL` | No library access permissions |
| Board Member 8 | `MODERNIZED_BOARD_MEMBER_8_USEREMAIL` | Has library management access |
| Board Member 9 | `MODERNIZED_BOARD_MEMBER_9_USEREMAIL` | No discussion access permissions |
| Board Member 12 | `MODERNIZED_BOARD_MEMBER_12_USEREMAIL` | Full view + permission to edit profile contact info |
| Board Member 13 | `MODERNIZED_BOARD_MEMBER_13_USEREMAIL` | Full view access |
| Board Member 14 | `MODERNIZED_BOARD_MEMBER_14_USEREMAIL` | Resources deactivated |
| Board Member 15 | `MODERNIZED_BOARD_MEMBER_15_USEREMAIL` | Event manager + resources deactivated |
| WA (not in UI Workroom) | `MODERNIZED_WORKROOM_ADMIN_NOT_PART_OF_UI_WORKROOM` | Workroom Admin NOT member of UI Workroom |
| BM (not in UI Workroom) | `MODERNIZED_BOARD_MEMBER_NOT_PART_OF_UI_WORKROOM` | Board Member NOT member of UI Workroom |

All users share the same password: `MODERNIZED_PASSWORD` in `.env.dev`.

---

## 6. CSV Test Case Format

The CSV has **22 columns** matching the Jira ATM exporter format. The structure is **one row per test step**:

```
Key,Name,Status,Precondition,Objective,Folder,Priority,Component,Labels,Owner,Estimated Time,Coverage (Issues),Coverage (Pages),MOD_Automation_Plan,MOD_Module (Frontend),MOD_SubModule (Frontend),MOD_Testing_Type (Frontend),Test Script (Step-by-Step) - Step,Test Script (Step-by-Step) - Test Data,Test Script (Step-by-Step) - Expected Result,Test Script (Plain Text),Test Script (BDD)
```

### Step-Per-Row Pattern
- **Row 1 of a test case**: All 22 columns filled (metadata + step 1).
- **Rows 2+ of the same test case**: Only 3 step columns filled (`Step`, `Test Data`, `Expected Result`). All others **empty**.
- A **new test case** starts when the `Key` column is populated again.

### Key Field Rules
| Field | Format / Values |
|---|---|
| `Key` | `BEW-T<number>` — only on row 1 |
| `Name` | `MOD-<Module>-<SubModule>-<Type>: <what is verified>` |
| `Status` | `Draft` |
| `Folder` | `/BE-Modernisation/Frontend/<Module>/<SubModule>` |
| `Priority` | `Normal` / `High` / `Low` |
| `Labels` | `MOD_New` |
| `MOD_Automation_Plan` | `MOD-P0-Automation` / `MOD-P1-Automation` / `MOD-Manual-Only` |
| `MOD_Testing_Type (Frontend)` | Comma-separated: `E2E`, `Functional`, `Regression`, `Smoke`, `Security`, `UI/UX` |
| Step columns | Action-verb-first; reference exact UI labels in single quotes; test data = specific value or `none` |

---

## 7. Exploration History

> This section is updated by the agent after each run. Human reviewers can also add notes here.

| Date       | Run By          | Modules Covered | TC Count | Notes                         |
|------------|-----------------|-----------------|----------|-------------------------------|
| _(pending)_ | Agent (first run) | —             | —        | Initial setup, no run yet     |

---

## 8. Known Constraints & Gotchas

- The login page redirects through `oidc.diligentoneplatform-dev.com` — Playwright must follow the redirect chain before filling credentials.
- MFA/SSO via SAML (`Continue with SSO` button) is **not tested** — skip if encountered without credentials.
- Workroom-specific pages require the workroom `UI Workroom` (ID `5732`) to exist; if missing, workroom tests will fail.
- Session tokens expire — if the agent runs for >30 minutes, it may need to re-authenticate mid-exploration.
- Use `[SYSTEM_ADMIN_EMAIL]` and `[PASSWORD]` as placeholders in test steps, never real values.

---

## 9. Extending This Setup

To add a new website/environment:
1. Add new env vars to `.env.dev` with a descriptive prefix.
2. Update `explore-and-generate-tests.prompt.md` with the new URL and any module differences.
3. Append a new row to the **Exploration History** table above after the run.
4. Screenshots for the new run will be in `screenshots/` — prefix with the env name if running multiple envs.
