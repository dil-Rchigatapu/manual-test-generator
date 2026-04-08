# Page Context Schema

> **What is this?**
> Every file in this folder is a **Page Context** — a structured snapshot of a page/feature captured by the Explorer Agent using Playwright MCP. The Test Generator Agent reads these files to create test cases without needing to re-explore.
>
> **Naming convention:** `<module>-<submodule>.context.md` (kebab-case, lowercase)
> Example: `meeting-books-create.context.md`, `login.context.md`, `workroom-library.context.md`

---

## Required Sections (Explorer must fill all)

```markdown
# Page Context: <Human-Readable Page/Feature Name>

## Meta
- **Explored On:** YYYY-MM-DD
- **Explorer Role Used (Primary):** <role env var name> e.g. MODERNIZED_SYSTEM_ADMIN_USEREMAIL
- **URL Path:** <relative path after base URL> e.g. /workrooms/5732/library/meeting-books
- **Full URL:** <exact URL captured during exploration>
- **Module:** <top-level module name matching MOD_Module column>
- **Sub-Module:** <sub-module name matching MOD_SubModule column>
- **Screenshot:** screenshots/<filename>.png

---

## Navigation Breadcrumb
> How to reach this page from the dashboard step-by-step
1. <Step 1 — e.g. Click 'Workrooms' in left sidebar>
2. <Step 2>
3. ...

---

## Roles With Access
> List every role that CAN access this page and any notable differences in what they see/can do.

| Role | Env Var | Can Access? | Differences vs System Admin |
|---|---|---|---|
| System Admin | MODERNIZED_SYSTEM_ADMIN_USEREMAIL | Yes | Full access — baseline |
| Workroom Admin | MODERNIZED_WORKROOM_ADMIN_USEREMAIL | Yes/No | <describe diff> |
| Board Member 1 (Full View) | MODERNIZED_BOARD_MEMBER_1_USEREMAIL | Yes/No | <describe diff> |
| ... | ... | ... | ... |

---

## Page Layout
> Describe the visual layout of the page — header, sidesheets, main content area, panels.

<Describe the layout — e.g. "Page has a top header bar with page title and action buttons. Left sidebar shows workroom navigation. Main content area shows a table of meeting books with columns: Title, Created By, Date, Status, Actions.">

---

## UI Elements

### Buttons
> For each button, inspect the DOM and capture the best available locator (priority: data-testid > aria-label > role+name > visible text).

| Label (exact text) | Locator | Location | Visible To | Enabled When | Action Triggered |
|---|---|---|---|---|---|
| Create Book | `data-testid="create-book-btn"` | Top-right of main content | System Admin, Workroom Admin | Always | Opens book creation page |
| ... | | | | | |

### Forms / Input Fields
| Field Label (exact) | Locator | Type | Required? | Placeholder Text | Validation Rule | Located In |
|---|---|---|---|---|---|---|
| Title (required) | `aria-label="Title"` | Text input | Yes | e.g. "Enter title" | Max 255 chars, cannot be blank | 'Create Book' sidesheet |
| ... | | | | | | |

### Dropdowns / Selects
| Label (exact) | Locator | Options | Default | Located In |
|---|---|---|---|---|
| Status | `role=combobox, name="Status"` | Draft, Published, Archived | Draft | Book detail header |
| ... | | | | |

### Tables / Lists
| Table Name | Locator (container) | Columns | Sortable Columns | Filterable? | Pagination? | Row Action Locators |
|---|---|---|---|---|---|---|
| Meeting Books list | `data-testid="meeting-books-table"` | Title, Created By, Date, Status, Actions | Title, Date | Yes (by Status) | Yes — 25 per page | `role=button, name="More actions"` per row |
| ... | | | | | | |

### Modals / Sidesheets
| Trigger (locator) | Modal/Sidesheet Title | Title Locator | Content / Fields (with locators) | Footer Actions (with locators) |
|---|---|---|---|---|
| `data-testid="create-book-btn"` | Create Book | `role=heading, name="Create Book"` | Option 1: 'Upload own book' `[data-testid="upload-own-book"]`; Option 2: 'Build a new book' `[data-testid="build-new-book"]` | Cancel `[role=button, name="Cancel"]`, Next `[role=button, name="Next"]` |
| ... | | | | |

### Navigation Items (tabs, secondary nav)
| Label (exact) | Locator | URL / Route | Visible To |
|---|---|---|---|
| All Books | `role=tab, name="All Books"` | /library/meeting-books | All roles with library access |
| ... | | | |

### Toast / Notification Messages
> Every success or error toast this page can emit — exact text and locator. These are used directly in Expected Result steps.

| Triggered By | Type | Exact Message Text | Locator |
|---|---|---|---|
| Successful book upload | Success | 'Book uploaded' | `role=alert` |
| Delete confirmed | Success | 'Book deleted successfully' | `role=alert` |
| Required field missing | Validation error | 'Title is required' | inline below field `[aria-describedby or similar]` |
| ... | | | |

---

## Key User Actions
> Summarize the meaningful actions a user can perform on this page. This drives test case scope.

| Action | Who Can Do It | Pre-condition | Notes |
|---|---|---|---|
| Create a book via 'Upload own book' | System Admin, Workroom Admin | PDF file available | Redirects to view book page on success |
| Create a book via 'Build a new book' | System Admin, Workroom Admin | — | Multi-step wizard |
| View an existing book | All roles with library access | At least one book exists | — |
| Delete a book | System Admin only | Book must not be published | Shows confirmation modal |
| Archive a book | System Admin, Workroom Admin | Book is published | Status changes to Archived |
| ... | | | |

---

## Permission-Gated Behaviors
> Elements or actions that behave differently based on role or data state.

- `Create Book` button — hidden for Board Members (BM1–BM9)
- `Delete` action in table row — only visible to System Admin
- Board Member 7 (MODERNIZED_BOARD_MEMBER_7_USEREMAIL) — has **no library access** — entire page should be inaccessible
- Board Member 8 (MODERNIZED_BOARD_MEMBER_8_USEREMAIL) — has **library management access** — same as Workroom Admin for this page
- <Add any other gating observed>

---

## Error / Edge States Observed
> States the page can be in that generate different UI behavior. Include locator and exact message text for every error/toast so test steps can assert precisely.

| State | Trigger | Exact Message Text | Message Locator | What else changes on UI |
|---|---|---|---|---|
| Empty state | No books created yet | 'No books found' | `data-testid="empty-state-message"` | Illustration + 'Create Book' CTA shown |
| Upload failure — wrong type | Non-PDF file selected | 'Only PDF files are accepted' | `role=alert` or inline error below upload | Upload button remains disabled |
| Upload failure — size exceeded | File > max size | 'File exceeds maximum size of {X}MB' | inline error below upload field | Upload button remains disabled |
| Session timeout | User idle >30 min | — | — | Redirect to login page |
| ... | | | | |

---

## Related Pages
> Pages linked to/from this page — useful for tracing test scenarios across multiple pages.

| Page Name | Context File | Relationship |
|---|---|---|
| View Book | meeting-books-view.context.md | Navigated to after successful book creation |
| Library (overview) | workroom-library.context.md | Parent page — breadcrumb link |
| ... | | |

---

## Notes / Observations
> Anything unusual, flaky, or worth knowing for test design.

- <e.g. Upload button becomes active only after both Title and File are filled — no validation message shown before clicking>
- <e.g. Sidesheet animation takes ~500ms — wait for it to fully open before interacting>
```
