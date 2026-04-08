# Manual Test Agent — BoardEffect (Diligent One Platform)

An AI-powered manual test generation system for the BoardEffect web application. Uses VS Code Agent Mode + Playwright MCP to autonomously browse the app, capture every UI element and role-based behavior, and produce Jira-ready manual test cases as CSV files — with zero automation code ever written.

---

## What This Repo Does

| Capability | Description |
|---|---|
| **Autonomous page exploration** | An AI agent logs into the app, navigates to any page, captures all UI elements (buttons, forms, dropdowns, modals, navigation), records locators, and notes role-based visibility differences |
| **Jira ATM-compatible CSV generation** | A second AI agent reads the captured context and generates crisp, human-readable test cases in the exact 22-column Jira ATM exporter format |
| **Locator-enriched steps** | Every test step references the exact UI locator (`data-testid`, `aria-label`, `role+name`) in the Test Data column — ready for an automation engineer to script without ambiguity |
| **Role-based coverage** | 17 test roles are pre-configured. The Explorer agent checks which UI elements are role-gated and flags gaps for security/permission tests |
| **Persistent page context** | Each explored page is saved as a `.context.md` file — a structured snapshot of all UI elements, locators, and behaviors. Re-run the Explorer to refresh after a UI change |
| **Per-module file structure** | Test cases are split into per-submodule CSV files and organized into a clean folder hierarchy under `manual-tests/`. A report file per module summarizes all generated test cases |

---

## How It Works

The system uses **two specialized agents** that run in sequence:

```
YOU
 │
 ├─▶  01 — Explorer Agent          Uses Playwright MCP browser
 │         (01-explorer.prompt.md)  → Logs in, navigates, inspects DOM
 │                                  → Saves page-contexts/<page>.context.md
 │
 └─▶  02 — Test Generator Agent    No browser — reads context files only
           (02-test-generator.prompt.md)
                                    → Writes per-submodule CSVs
                                    → Generates module report
```

**Exploration and test generation are fully decoupled.** Explore once, generate tests many times. Re-explore only when the UI changes.

---

## Quick Start

### Prerequisites
- VS Code with GitHub Copilot (Agent Mode enabled)
- Node.js installed (for `npx`)
- `.env.dev` file with application credentials (see `.env.dev` — never commit this file)

### 1 — Configure Playwright MCP

The `.vscode/mcp.json` file is already set up. VS Code will automatically register the Playwright MCP server on startup. No manual install needed — `npx` fetches it on first use.

To use **headed mode** (watch the browser as it explores), open `.vscode/mcp.json` and remove `"--headless"` from the args.

### 2 — Explore a Page

1. Open VS Code Chat → switch to **Agent** mode
2. Reference the Explorer agent: type `#agents/01-explorer.prompt.md`
3. Give an instruction:
   ```
   Explore the Home page as System Admin.
   ```
   ```
   Explore the Meeting Books > Create Book page. Check System Admin and Board Member 7.
   ```
4. The agent logs in, navigates, captures all UI details, and saves `page-contexts/<page>.context.md`

### 3 — Generate Test Cases

1. Open VS Code Chat → **Agent** mode
2. Reference the Test Generator: `#agents/02-test-generator.prompt.md`
3. Give an instruction:
   ```
   Generate full coverage from page-contexts/home.context.md. Start from BEW-T7100.
   ```
   ```
   Generate only role-based access tests from page-contexts/home.context.md.
   ```
   ```
   Generate negative/validation tests for the Upload sidesheet in page-contexts/meeting-books-create.context.md.
   ```
4. Test cases are written to `manual-tests/<module>/<submodule>/<submodule>-test-cases.csv`
5. A report is generated/updated at `manual-tests/<module>/<module>-test-cases-report.md`

---

## Output File Structure

```
manual-tests/
└── home/
    ├── home-page-test-cases-report.md     ← module report
    ├── authentication/
    │   └── authentication-test-cases.csv  ← 6 test cases
    ├── navigation/
    │   └── navigation-test-cases.csv      ← 4 test cases
    ├── widgets/
    │   ├── welcome/
    │   │   └── welcome-test-cases.csv     ← 2 test cases
    │   └── meetings/
    │       └── meetings-test-cases.csv    ← 3 test cases
    ├── search/
    │   └── search-test-cases.csv          ← 1 test case
    ├── approvals-drawer/
    │   └── approvals-drawer-test-cases.csv ← 3 test cases
    ├── top-system-bar/
    │   └── top-system-bar-test-cases.csv  ← 4 test cases
    └── footer/
        └── footer-test-cases.csv          ← 1 test case
```

**Naming convention:** `<submodule>-test-cases.csv` — always matches the innermost folder name.  
**Future modules** follow the same pattern: `manual-tests/login/login-test-cases.csv`, `manual-tests/workrooms/create-book/create-book-test-cases.csv`, etc.

---

## CSV Format

The CSV is a **22-column Jira ATM exporter format** with a **step-per-row pattern**:

- **Row 1** of each test case: all 22 metadata columns filled + step 1
- **Rows 2+** of the same test case: only 3 step columns filled (`Step`, `Test Data`, `Expected Result`), all metadata empty
- **New test case** starts when the `Key` column is populated again

### Locator Strategy

Locators are placed in the **Test Data column**, not in the step text:

| Column | Content |
|---|---|
| Step | Plain English: `Click 'Sign in' button` |
| Test Data | `locator: role=button name="Sign in" \| value: none` |
| Expected Result | Exact observable outcome |

Locator priority: `data-testid` > `aria-label` > `role+name` > `visible-text`

---

## Repo Structure

```
manual-test-agent/
├── .env.dev                          # Local credentials — NEVER commit
├── .vscode/
│   └── mcp.json                      # Playwright MCP server config
├── agents/
│   ├── 01-explorer.prompt.md         # Explorer Agent
│   └── 02-test-generator.prompt.md   # Test Generator Agent
├── page-contexts/
│   ├── _SCHEMA.md                    # Schema template for all context files
│   ├── INDEX.md                      # Index of all explored pages
│   └── home.context.md               # Explored page contexts (one per page)
├── screenshots/                      # DOM screenshots from exploration
├── manual-tests/                     # Generated test cases + reports
│   └── <module>/
│       ├── <module>-test-cases-report.md
│       └── <submodule>/
│           └── <submodule>-test-cases.csv
└── CONTEXT.md                        # Full onboarding doc — architecture, roles, CSV format
```

---

## Test Roles

17 pre-configured test users covering the full permission spectrum:

| Role | Key Difference |
|---|---|
| System Admin | Full system access — sees Site Settings, Edit on Welcome widget |
| Workroom Admin | Manages workroom settings & members |
| Board Member 1 | Full view access to all content |
| Board Member 2 | No access to archived books |
| Board Member 3 | No event access permissions |
| Board Member 4 | Has event management access |
| Board Member 5 | No collaborate access permissions |
| Board Member 6 | Has collaborate management access |
| Board Member 7 | No library access permissions |
| Board Member 8 | Has library management access |
| Board Member 9 | No discussion access permissions |
| Board Member 12 | Can edit profile contact info |
| Board Member 13 | Full view access |
| Board Member 14 | Resources deactivated |
| Board Member 15 | Event manager + resources deactivated |
| WA (not in UI Workroom) | Workroom Admin not a member of UI Workroom |
| BM (not in UI Workroom) | Board Member not a member of UI Workroom |

All credentials are defined in `.env.dev`. All users share a single shared password (`MODERNIZED_PASSWORD`).

---

## Current Coverage

| Module | Explored Roles | Test Cases | Status |
|---|---|---|---|
| Home | System Admin | 24 (BEW-T7100 – BEW-T7123) | ✅ Complete |
| Login | — | — | ⏳ Not yet explored |
| Workrooms | — | — | ⏳ Not yet explored |
| Library | — | — | ⏳ Not yet explored |
| Directory | — | — | ⏳ Not yet explored |
| Messaging | — | — | ⏳ Not yet explored |
| Minutes | — | — | ⏳ Not yet explored |
| Approvals | — | — | ⏳ Not yet explored |
| Site Settings | — | — | ⏳ Not yet explored |

---

## Key Design Decisions

- **No automation code is ever generated.** Playwright MCP is used purely as a remote-controlled browser — not as a test framework.
- **Exploration and generation are decoupled.** Explore once, generate tests independently, re-explore only on UI change.
- **Locators live in Test Data, not steps.** Steps remain human-readable; locators are available for automation AI without cluttering the manual step text.
- **One CSV per submodule.** Each file is independently importable into Jira ATM without touching other modules.
- **Reports co-locate with test cases.** The module report lives inside the module folder, not in a separate `reports/` directory.

---

## More Details

See [CONTEXT.md](CONTEXT.md) for the full onboarding document: agent usage instructions, all 17 role details, complete CSV format specification, and known constraints.
