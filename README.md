# Manual Test Agent — BoardEffect (Diligent One Platform)

A true agentic QA system for the BoardEffect web application. Uses VS Code Agent Mode + Playwright MCP to autonomously browse the app, reason about coverage, detect bugs, critique its own output, and produce Jira-ready manual test cases as CSV files — with zero automation code ever written.

The system is self-improving: every exploration, generation, and critique run updates persistent memory files that make the next Planner pass smarter. It doesn't just generate tests — it decides what to test, validates quality, and tracks what it has already covered.

---

## What This Repo Does

| Capability | Description |
|---|---|
| **Autonomous QA planning** | The Planner agent reads memory, scores every module by risk, and tells you exactly what to explore or test next — with priority scores and reasoning |
| **Flow-first page exploration** | The Explorer captures complete user flows and state transition maps, not just element lists. Outputs a structured semantic context layer with grouped elements, validation rules, and role diffs |
| **Deterministic page-ready detection** | Built-in wait protocol: network idle → container visible → loaders gone → assert ready. Prevents capturing garbage loading states |
| **Validation rule engine** | Every form field in a context file records exact valid/invalid examples and error message text — removing LLM guesswork from test generation |
| **Role diffing** | Role comparisons use a structured REMOVED / DISABLED / ADDED / ACCESS DENIED diff format instead of freeform notes |
| **Change detection** | Re-running the Explorer on an already-explored page produces a `## Changes Detected` section showing exactly what was added, removed, or changed — enabling regression planning |
| **Strategy-mode test generation** | The Generator supports 6 modes: Smoke, Regression, Security, Accessibility, Negative, Full. Each mode produces a completely different test suite from the same context file |
| **Heuristic rules engine** | A 14-pattern lookup table maps detected element types (forms, file uploads, role gates, state transitions, etc.) to required test types — coverage is deterministic, not guessed |
| **Coverage Critic loop** | The Critic reviews generated CSVs against the context file and outputs an actionable gap report with missing scenarios, duplicate tests, and weak assertions. Generator refines based on the report |
| **Active bug hunting** | The Bug Detector agent probes every interactive element, monitors for console errors, and audits accessibility. Outputs a structured bug report and updates the risk map |
| **Persistent memory** | Three memory files track coverage metrics, risk scores, known bugs, and generation history across all sessions. Every agent reads and updates memory |
| **Jira ATM-compatible CSV output** | 22-column format, step-per-row, with locators in the Test Data column — ready to import directly into Jira ATM |

---

## The Five Agents

| Agent | File | Browser? | Job |
|---|---|---|---|
| **00 — Planner** | `agents/00-planner.prompt.md` | No | Reads memory + coverage state → scores every module by risk → outputs a prioritized backlog of what to explore and test next |
| **01 — Explorer** | `agents/01-explorer.prompt.md` | Yes | Navigates to a page → builds semantic context layer (grouped elements, validation rules, error patterns, flow maps, state transitions) → detects changes vs. previous exploration → role-diffs with structured REMOVED/DISABLED/ADDED format → saves `.context.md` with a coverage confidence score |
| **02 — Generator** | `agents/02-test-generator.prompt.md` | No | Reads context files → applies a strategy mode (Smoke/Regression/Security/Accessibility/Negative/Full) → runs heuristic engine against element types → writes Jira ATM-compatible CSV test cases |
| **03 — Critic** | `agents/03-critic.prompt.md` | No | Reviews generated CSVs against context → checks 14 heuristic categories → flags missing scenarios, duplicates, weak assertions, incomplete steps → outputs a gap report → tells Generator exactly what to fix |
| **04 — Bug Detector** | `agents/04-bug-detector.prompt.md` | Yes | Probes every interactive element on a page → submits empty forms → checks for console errors → audits accessibility labels and focus management → outputs `bugs/<page>-bugs.md` → updates `memory/risk-map.json` |

---

## How It Works

```
                    ┌──────────────────────┐
                    │   00 — Planner       │  Reads memory/ → scores risk → outputs
                    │   (no browser)       │  prioritized backlog: what to explore next
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │   01 — Explorer      │  Playwright MCP browser
                    │   (browser)          │  Semantic context layer: grouped elements,
                    └──────────┬───────────┘  validation rules, flow maps, state machines,
                               │              role diffs, coverage confidence score,
                               ↓              change detection vs. prior exploration
              ┌────────────────────────────┐
              │   04 — Bug Detector        │  Optional — deep bug hunt on any page
              │   (browser)               │  Console errors, accessibility audit,
              └──────────────┬─────────────┘  broken interactions → bugs/<page>-bugs.md
                             ↓
                    ┌──────────────────────┐
                    │   02 — Generator     │  No browser — reads context files only
                    │   (no browser)       │  Strategy mode: Smoke/Regression/Security/
                    └──────────┬───────────┘  Accessibility/Negative/Full
                               ↓              Heuristic engine: 14 element-type patterns
                    ┌──────────────────────┐  → deterministic coverage requirements
                    │   03 — Critic        │
                    │   (no browser)       │  Reviews CSV vs. context file
                    └──────────┬───────────┘  Finds gaps, duplicates, weak assertions
                               │              Outputs coverage-critique-<date>.md
                    ┌──────────▼───────────┐
                    │   02 — Generator     │  Loops back to fill critique gaps
                    │   (refine pass)      │
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │      memory/         │  Updated by every agent after every run
                    │   coverage.json      │  coverage.json: explored?, tc count, types
                    │   risk-map.json      │  risk-map.json: risk scores, known bugs
                    │   test-history.json  │  test-history.json: append-only run log
                    └──────────────────────┘
```

**The loop:**
1. **Planner** reads memory → tells you what has highest risk and lowest coverage → prioritizes next exploration
2. **Explorer** visits page → builds semantic context layer → detects flows + state transitions + change diffs + role diffs → assigns coverage confidence score
3. **Bug Detector** (optional) → deep-probes any high-risk page → logs defects → updates risk map
4. **Generator** reads context → selects strategy mode → applies heuristic rules → writes Jira ATM CSVs + updates memory
5. **Critic** reviews CSVs → outputs gap report → Generator loops back to fill gaps
6. **Memory** updated after every step → next Planner run is smarter than the last

---

## Quick Start

### Prerequisites
- VS Code with GitHub Copilot (Agent Mode enabled)
- Node.js installed (for `npx`)
- `.env.dev` file with application credentials (see `.env.dev` — never commit this file)

### 1 — Configure Playwright MCP

The `.vscode/mcp.json` file is already set up. VS Code will automatically register the Playwright MCP server on startup. No manual install needed — `npx` fetches it on first use.

To use **headed mode** (watch the browser), open `.vscode/mcp.json` and remove `"--headless"` from the args.

### 2 — Run the Planner (always start here each sprint)

```
#agents/00-planner.prompt.md

What should I work on next?
```

The Planner reads `memory/`, `page-contexts/INDEX.md`, and `manual-tests/` → scores every module by risk → outputs a red/amber/green prioritized backlog.

### 3 — Explore a Page

```
#agents/01-explorer.prompt.md

Explore the Login page as System Admin and Board Member 7.
```

```
#agents/01-explorer.prompt.md

Explore the Meeting Books > Create Book flow. Check SA and WA roles.
```

The Explorer logs in, applies the wait protocol, builds the semantic context layer, maps all flows + state transitions, performs role diffs, and assigns a coverage confidence score. Saves `page-contexts/<page>.context.md`.

> **Re-exploration:** Run the same instruction again on an already-explored page. The Explorer loads the baseline and outputs a `## Changes Detected` section — useful for regression planning after a UI release.

### 4 — (Optional) Probe for Bugs

```
#agents/04-bug-detector.prompt.md

Probe the GovernAI panel on book 30818 for bugs.
```

Outputs `bugs/<page>-bugs.md` and updates `memory/risk-map.json`. The Planner will factor discovered bugs into risk scoring on the next run.

### 5 — Generate Test Cases

```
#agents/02-test-generator.prompt.md

Generate Smoke tests from page-contexts/login.context.md. Start from BEW-T7200.
```

```
#agents/02-test-generator.prompt.md

Generate Security tests only from page-contexts/home.context.md. Start from BEW-T7150.
```

```
#agents/02-test-generator.prompt.md

Generate full Regression coverage from page-contexts/meeting-books-ai-feature.context.md. Start from BEW-T7133.
```

The Generator applies the heuristic engine, selects coverage categories for the chosen mode, writes the per-submodule CSV, and updates `memory/`.

### 6 — Critique the Output

```
#agents/03-critic.prompt.md

Review page-contexts/login.context.md and manual-tests/login/login-test-cases.csv
```

The Critic outputs `manual-tests/<module>/<module>-coverage-critique-<date>.md`. Then loop the Generator:

```
#agents/02-test-generator.prompt.md

Fill all critical gaps from manual-tests/login/login-coverage-critique-2026-04-10.md. Start from BEW-T7220.
```

---

## Test Strategy Modes

The Generator supports six modes. Pass the mode name in your instruction:

| Mode | What it generates | CSV `MOD_Testing_Type` | Automation Plan |
|---|---|---|---|
| **Smoke** | One critical happy path per flow — minimum steps to verify core works | `Smoke, E2E` | `MOD-P0-Automation` |
| **Regression** | All happy paths + all documented state transitions | `Regression, E2E, Functional` | `MOD-P1-Automation` |
| **Security** | Role-based access tests for every role in the context — can-access + cannot-access variants | `Security` | `MOD-Manual-Only` |
| **Accessibility** | Empty states, dialog focus, ARIA label coverage, keyboard navigation hints | `UI/UX` | `MOD-Manual-Only` |
| **Negative** | Validation errors, invalid inputs, file upload failures, cancel mid-flow | `Functional` | `MOD-P1-Automation` |
| **Full** | All of the above combined (default when no mode specified) | varies | varies |

---

## Heuristic Engine

The Generator applies a deterministic 14-pattern lookup table. When a pattern is detected in a context file, the corresponding test types are automatically required — no guessing:

| Pattern in Context | Required Tests |
|---|---|
| Form with required fields | Empty submit; each field empty individually; max-length exceeded; invalid format |
| File upload | Valid file; size exceeded; wrong type; empty/corrupted; cancel upload |
| Role-gated element | Role CAN access (positive); Role CANNOT access (negative — hidden/disabled/redirect) |
| Multi-step flow | Complete success path; cancel at each step; validation error at each step |
| Enabled/disabled state button | Verify disabled state + blocking condition; verify enabled state + triggering condition |
| State transition A → B | Verify state A UI; trigger transition; verify state B UI; verify blocked transitions |
| Delete / destructive action | Happy path with confirmation; cancel the dialog; verify result after each path |
| Search / filter | Valid query; no results (empty state); clear search; special characters |
| Pagination | First page; next page; last page; item count display |
| Toast / alert message | Verify exact message text for every documented toast |
| Empty state | Trigger empty state; verify message text and any CTA button |
| Table with row actions | Open row menu; each action; verify absent for restricted roles |
| Modal with unsaved changes | Navigate away → verify warning prompt; confirm discard; cancel discard |

---

## Output File Structure

```
manual-tests/
└── <module>/
    ├── <module>-test-cases-report.md          ← auto-generated after each run
    ├── <module>-coverage-critique-YYYY-MM-DD.md ← output of Critic agent
    └── <submodule>/
        └── <submodule>-test-cases.csv          ← Jira ATM-importable, step-per-row

bugs/
└── <page>-bugs.md                             ← output of Bug Detector agent

page-contexts/
└── <module>-<submodule>.context.md            ← semantic context layer per page

memory/
├── coverage.json                              ← per-module: explored?, tc count, roles
├── risk-map.json                              ← risk scores, known bugs, fragile flows
└── test-history.json                          ← append-only log of all generation runs
```

---

## CSV Format

22-column Jira ATM exporter format, step-per-row:

- **Row 1** of each test case: all 22 metadata columns filled + step 1
- **Rows 2+**: only 3 step columns filled (`Step`, `Test Data`, `Expected Result`), metadata empty
- **New test case** starts when `Key` column is populated again

### Locator Strategy

| Column | Content |
|---|---|
| **Step** | Plain English: `Click 'Sign in' button` |
| **Test Data** | `locator: role=button name="Sign in" \| value: none` |
| **Expected Result** | Exact observable outcome |

Locator priority: `data-testid` > `aria-label` > `role+name` > `visible-text`

---

## Repo Structure

```
manual-test-agent/
├── .env.dev                              # Local credentials — NEVER commit
├── .vscode/
│   └── mcp.json                          # Playwright MCP server config
├── agents/
│   ├── 00-planner.prompt.md              # Planner — risk scoring + prioritized backlog
│   ├── 01-explorer.prompt.md             # Explorer — semantic context, flow maps, role diffs
│   ├── 02-test-generator.prompt.md       # Generator — strategy modes + heuristic engine
│   ├── 03-critic.prompt.md               # Critic — gap analysis + quality enforcement
│   └── 04-bug-detector.prompt.md         # Bug Detector — active probing + accessibility audit
├── memory/
│   ├── README.md                         # Schema docs for all memory files
│   ├── coverage.json                     # Per-module: explored?, tc count, types, dates
│   ├── risk-map.json                     # Risk scores, known bugs, fragile flows
│   └── test-history.json                 # Append-only log of all generation runs
├── page-contexts/
│   ├── _SCHEMA.md                        # Schema template for all context files
│   ├── INDEX.md                          # Index of all explored pages + confidence scores
│   └── *.context.md                      # Semantic context per page
├── bugs/
│   └── <page>-bugs.md                    # Bug reports from Bug Detector
├── screenshots/                          # Screenshots captured during exploration
├── manual-tests/
│   └── <module>/
│       ├── <module>-test-cases-report.md
│       ├── <module>-coverage-critique-<date>.md
│       └── <submodule>/
│           └── <submodule>-test-cases.csv
└── CONTEXT.md                            # Full onboarding doc — architecture, roles, CSV spec
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

All credentials are in `.env.dev`. All users share `MODERNIZED_PASSWORD`.

---

## Current Coverage

| Module | Explored Roles | Test Cases | Critique Run? | Status |
|---|---|---|---|---|
| Home | System Admin | 24 (BEW-T7100 – BEW-T7123) | ❌ | ✅ Tests generated |
| Navigation (Side Nav) | System Admin | 5 (BEW-T10535 – T10541) | ❌ | ✅ Tests generated |
| Meeting Books / AI Feature | System Admin | 2 (BEW-T7131 – T7132) | ❌ | ✅ Live-verified, pushed |
| Login | — | 0 | — | 🔴 Critical — not explored |
| Workrooms | — | 0 | — | 🔴 Critical — not explored |
| Library | — | 0 | — | 🟠 High — not explored |
| Minutes | — | 0 | — | ⏳ Not yet explored |
| Approvals | — | 0 | — | ⏳ Not yet explored |
| Directory | — | 0 | — | ⏳ Not yet explored |
| Messaging | — | 0 | — | ⏳ Not yet explored |
| Site Settings | — | 0 | — | ⏳ Not yet explored |

> Run `#agents/00-planner.prompt.md` → "What should I work on next?" for a live risk-scored backlog based on current memory state.

---

## Key Design Decisions

- **No automation code is ever generated.** Playwright MCP is used purely as a remote-controlled browser — not as a test framework.
- **Exploration and generation are fully decoupled.** Explore once, generate from context any number of times, re-explore only when the UI changes.
- **Semantic context layer, not raw DOM.** The Explorer transforms the accessibility tree into a structured, grouped, testable model — forms with validation rules, flows with exit points, elements with enabled/disabled conditions.
- **Locators live in Test Data, not steps.** Steps stay human-readable; locators are in the Test Data column for automation engineers.
- **One CSV per submodule.** Every file is independently importable into Jira ATM.
- **Reports co-locate with test cases.** Module reports and critique reports live inside the module folder.
- **Generator → Critic → Generator loop.** No test suite is complete without a Critic pass. The loop enforces quality, not just quantity.
- **14-pattern heuristic engine.** Test coverage requirements are derived deterministically from element types in the context file. Coverage is not guessed.
- **6 strategy modes.** Smoke, Regression, Security, Accessibility, Negative, Full — one context file, six different test suites depending on your need.
- **Memory persists across all sessions.** `coverage.json`, `risk-map.json`, `test-history.json` give the Planner real data — not assumptions.
- **Bug Detector is a first-class agent.** Defect discovery is separate from exploration and generation. Bugs feed the risk map; the risk map feeds the Planner.
- **Change detection built in.** Re-running Explorer on a known page produces a structured diff. Changes drive regression test priorities.
- **Coverage confidence score.** Every context file rates itself on 7 dimensions. The Planner uses this to decide if re-exploration is needed before generating tests.

---

## More Details

See [CONTEXT.md](CONTEXT.md) for the full onboarding document: agent usage instructions, all 17 role credential env vars, complete CSV format specification, and known constraints.
