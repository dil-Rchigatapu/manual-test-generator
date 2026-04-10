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

The system uses **five specialized agents** organized in a self-improving loop:

```
                    ┌──────────────────┐
                    │  00 — Planner    │  ← Reads memory, scores risk,
                    │  (No browser)    │    outputs prioritized backlog
                    └────────┬─────────┘
                             ↓
                    ┌──────────────────┐
                    │  01 — Explorer   │  ← Playwright MCP browser
                    │  (Browser)       │    Explores page, detects flows,
                    └────────┬─────────┘    maps state transitions,
                             │              monitors for bugs
                             ↓
              ┌──────────────────────────┐
              │  04 — Bug Detector       │  ← Optional: deep bug hunt
              │  (Browser)               │    on any high-risk page
              └──────────────┬───────────┘
                             ↓
                    ┌──────────────────┐
                    │  02 — Generator  │  ← No browser, reads context
                    │  (No browser)    │    Modes: Smoke/Regression/
                    └────────┬─────────┘    Security/Accessibility/Negative
                             ↓              Heuristic engine auto-applies
                    ┌──────────────────┐
                    │  03 — Critic     │  ← Reviews generated test cases
                    │  (No browser)    │    Finds gaps, duplicates,
                    └────────┬─────────┘    weak assertions, risk holes
                             │
                    ┌────────▼─────────┐    Generator loops back to
                    │  02 — Generator  │  ← fill gaps found by Critic
                    │  (refine loop)   │
                    └────────┬─────────┘
                             ↓
                    ┌──────────────────┐
                    │    memory/       │  ← Persistent state across all
                    │  coverage.json   │    sessions: coverage metrics,
                    │  risk-map.json   │    risk scores, bug history,
                    │  test-history.json│   run history
                    └──────────────────┘
```

**The loop:**
1. **Planner** reads memory → tells you what has highest risk and lowest coverage → prioritizes next exploration
2. **Explorer** visits the page → captures elements, flows, state machines, and bugs
3. **Bug Detector** (optional) → deep-probes any high-risk page for defects
4. **Generator** reads context → selects a strategy mode → applies heuristic rules → writes test cases
5. **Critic** reviews CSVs → outputs a gap report → Generator loops back to fill gaps
6. **Memory** is updated by every agent → next Planner run is smarter than the last

---

## Quick Start

### Prerequisites
- VS Code with GitHub Copilot (Agent Mode enabled)
- Node.js installed (for `npx`)
- `.env.dev` file with application credentials (see `.env.dev` — never commit this file)

### 1 — Configure Playwright MCP

The `.vscode/mcp.json` file is already set up. VS Code will automatically register the Playwright MCP server on startup. No manual install needed — `npx` fetches it on first use.

To use **headed mode** (watch the browser as it explores), open `.vscode/mcp.json` and remove `"--headless"` from the args.

### 2 — Run the Planner (Start Here Each Sprint)

1. Open VS Code Chat → switch to **Agent** mode
2. Reference the Planner agent: `#agents/00-planner.prompt.md`
3. Give an instruction:
   ```
   What should I work on next?
   ```
4. The Planner reads `memory/`, `page-contexts/INDEX.md`, and `manual-tests/` → outputs a prioritized backlog with risk scores

### 3 — Explore a Page

1. Open VS Code Chat → agent mode
2. Reference the Explorer agent: `#agents/01-explorer.prompt.md`
3. Give an instruction:
   ```
   Explore the Login page as System Admin and Board Member 7.
   ```
   ```
   Explore the Meeting Books > Create Book flow. Check SA and WA roles.
   ```
4. The agent logs in, navigates, detects flows + state transitions, monitors for bugs, and saves `page-contexts/<page>.context.md`

### 4 — (Optional) Run the Bug Detector on High-Risk Pages

1. Reference the Bug Detector: `#agents/04-bug-detector.prompt.md`
2. Give an instruction:
   ```
   Probe the GovernAI panel on book 30818 for bugs.
   ```
4. The agent outputs `bugs/<page>-bugs.md` and updates `memory/risk-map.json`

### 5 — Generate Test Cases

1. Reference the Generator: `#agents/02-test-generator.prompt.md`
2. Give an instruction:
   ```
   Generate Smoke tests from page-contexts/login.context.md. Start from BEW-T7200.
   ```
   ```
   Generate Security tests only from page-contexts/meeting-books-ai-feature.context.md.
   ```
   ```
   Generate full Regression coverage from page-contexts/home.context.md. Start from BEW-T7150.
   ```
3. The agent applies the heuristic engine, selects the right coverage categories for the mode, and writes to the per-submodule CSV

### 6 — Run the Coverage Critic

1. Reference the Critic: `#agents/03-critic.prompt.md`
2. Give an instruction:
   ```
   Review page-contexts/login.context.md and manual-tests/login/login-test-cases.csv
   ```
3. The Critic outputs a gap report to `manual-tests/<module>/<module>-coverage-critique-<date>.md`
4. Loop back to Generator with: "Fill gaps from the critique report. Start from BEW-T7xxx."

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
│   ├── 00-planner.prompt.md          # Planner Agent — decides what to explore next
│   ├── 01-explorer.prompt.md         # Explorer Agent — flow detection + state mapping
│   ├── 02-test-generator.prompt.md   # Generator Agent — strategy modes + heuristic engine
│   ├── 03-critic.prompt.md           # Critic Agent — coverage gap analysis
│   └── 04-bug-detector.prompt.md     # Bug Detector Agent — active bug hunting
├── memory/
│   ├── README.md                     # Schema docs for all memory files
│   ├── coverage.json                 # Per-module: explored?, tc count, types covered
│   ├── risk-map.json                 # Risk scores, known bugs, fragile flows
│   └── test-history.json            # Append-only log of all generation runs
├── page-contexts/
│   ├── _SCHEMA.md                    # Schema template for all context files
│   ├── INDEX.md                      # Index of all explored pages
│   └── *.context.md                  # Explored page contexts (one per page)
├── bugs/                             # Bug reports from Bug Detector Agent
│   └── <page>-bugs.md
├── screenshots/                      # DOM screenshots from exploration
├── manual-tests/                     # Generated test cases + reports
│   └── <module>/
│       ├── <module>-test-cases-report.md
│       ├── <module>-coverage-critique-<date>.md
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

| Module | Explored Roles | Test Cases | Critique Run? | Status |
|---|---|---|---|---|
| Home | System Admin | 24 (BEW-T7100 – BEW-T7123) | ❌ | ✅ Tests generated |
| Navigation (Side Nav) | System Admin | 5 (BEW-T10535 – T10541) | ❌ | ✅ Tests generated |
| Meeting Books / AI Feature | System Admin | 2 (BEW-T7131 – T7132) | ❌ | ✅ Tests generated, live-verified |
| Login | — | 0 | — | 🔴 Not explored (Critical) |
| Workrooms | — | 0 | — | 🔴 Not explored (Critical) |
| Library | — | 0 | — | 🟠 Not explored (High) |
| Minutes | — | 0 | — | ⏳ Not yet explored |
| Approvals | — | 0 | — | ⏳ Not yet explored |
| Directory | — | 0 | — | ⏳ Not yet explored |
| Messaging | — | 0 | — | ⏳ Not yet explored |
| Site Settings | — | 0 | — | ⏳ Not yet explored |

---

## Key Design Decisions

- **No automation code is ever generated.** Playwright MCP is used purely as a remote-controlled browser — not as a test framework.
- **Exploration and generation are decoupled.** Explore once, generate tests independently, re-explore on UI change.
- **Locators live in Test Data, not steps.** Steps remain human-readable; locators are available for automation AI without cluttering the manual step text.
- **One CSV per submodule.** Each file is independently importable into Jira ATM without touching other modules.
- **Reports co-locate with test cases.** The module report lives inside the module folder, not in a separate `reports/` directory.
- **Generator → Critic → Generator loop.** Test cases are never committed without a Critic pass. The loop catches gaps the Generator missed on first run.
- **Heuristic rules engine.** The Generator applies a deterministic lookup table based on element types detected in the context file. Coverage is not left to LLM intuition alone.
- **Strategy modes.** A single context file can produce completely different test suites depending on focus: Smoke, Regression, Security, Accessibility, or Negative.
- **Memory persists across sessions.** `coverage.json`, `risk-map.json`, and `test-history.json` give the Planner real data to reason about — not guesses.
- **Bug Detector is a first-class agent.** Active bug hunting is separate from exploration and from test generation. Bugs found update the risk map, which in turn influences the Planner's next priorities.

---

## More Details

See [CONTEXT.md](CONTEXT.md) for the full onboarding document: agent usage instructions, all 17 role details, complete CSV format specification, and known constraints.
