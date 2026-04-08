# Page Context — Home (New-Style) & Side Navigation

**Module:** Navigation  
**Sub-Module:** Side Navigation  
**URL:** `/home-new`, `/workrooms/:id`, `/library` (all new-style pages)  
**Page Titles:** "Welcome to BoardEffect" (`/home-new`), "Workroom home" (`/workrooms/:id`), "Library"  
**Explored By:** System Admin (`MODERNIZED_SYSTEM_ADMIN_USEREMAIL`)  
**Explored On:** 2026-04-08  
**Status:** Complete for System Admin — other roles noted below

---

## CRITICAL: TWO SIDEBAR VARIANTS EXIST

The application has **two completely different sidebar designs** depending on which page you are on. The bad test cases conflate both. Tests must be clear about which variant they are testing.

| Variant | Pages | Structure |
|---|---|---|
| **Variant A — Legacy sidebar** | `/home/index`, `/` (old home) | Always-visible left rail, `list > listitem > link` |
| **Variant B — New-style sidebar** | `/home-new`, `/workrooms/:id`, `/library`, all new-style pages | Collapsible drawer, opened via button, renders as `role=dialog` |

**All 5 bad test cases target Variant B (new-style sidebar).** The bad cases test steps say "Navigate to any workroom page" before clicking sidebar items — that context puts you in the new-style sidebar.

---

## 1. Login & Navigation to New-Style Area

**Entry Point:** `MODERNIZED_APPLICATION_URL` → redirects to OIDC login  
**OIDC Login Page URL:** `https://oidc.diligentoneplatform-dev.com/login`

| Login Element | Locator |
|---|---|
| Email field | `role=textbox name="Email"` |
| Password field | `role=textbox name="Password"` |
| Sign in button | `role=button name="Sign in"` |

**To reach a new-style page:** After login, navigate to any workroom → `role=link name="UI-Workroom"` → URL: `/workrooms/5732`  
**Or navigate directly to:** `https://mod-lab-auto.boardeffect.diligentoneplatform-dev.com/workrooms/5732`

---

## 2. Opening & Closing the New-Style Side Navigation

| Action | Locator | State Change |
|---|---|---|
| Open side navigation | `role=button name="Side navigation"` | Button becomes `[expanded]`; `role=dialog` appears |
| Close side navigation | `role=button name="Side navigation" expanded` | Dialog closes |
| Side nav container | `role=dialog` > `navigation "Side"` | Contains all nav links |

---

## 3. New-Style Sidebar Navigation Items (Variant B)

All items below are inside `role=dialog` > `navigation "Side"`.

### 3a. Back to platform

| Property | Value |
|---|---|
| Locator | `role=link name="Back to platform"` |
| URL | `https://mod-lab-auto.home.diligentoneplatform-dev.com` |
| Destination | Diligent One Platform launchpad (Highbond home) — **exits BoardEffect** |
| Note | Label is lowercase 'p' ("Back to platform") in new-style sidebar. Compare with legacy sidebar ("Back to Platform" — uppercase P). |

### 3b. Home

| Property | Value |
|---|---|
| Locator | `role=link name="Home"` |
| URL | `/home-new` |
| Destination | New-style home page. Heading `role=heading level=1 name="Home"` is visible. |
| Active state | Visual CSS highlight only — no `aria-current` or `[selected]` in accessibility tree |
| Page title | "Welcome to BoardEffect" |

### 3c. Workrooms (accordion/tree)

**Workrooms is a `button`, NOT a link in the new-style sidebar.**

| State | Locator | Behaviour |
|---|---|---|
| Collapsed | `role=button name="Workrooms"` | `[expanded]` attribute absent. Clicking expands the tree. |
| Expanded | `role=button name="Workrooms" expanded` | `role=tree` becomes visible with workgroup treeitems |
| Re-collapse | Click `role=button name="Workrooms" expanded` | Tree collapses, workgroups hidden |

**Workgroup drill-down (after Workrooms is expanded):**

| Level | Locator | State |
|---|---|---|
| Workgroup item | `role=treeitem name="UI-Workgroup"` | Clicking toggles `[expanded]` |
| Workgroup item expanded | `role=treeitem name="UI-Workgroup" expanded` | Shows nested workroom treeitems |
| Workroom item (Non-UI) | `role=treeitem name="Non-UI-Workroom"` | URL: `/workrooms/5768` |
| Workroom item (UI) | `role=treeitem name="UI-Workroom"` | URL: `/workrooms/5732` |
| Active workroom | `role=treeitem name="UI-Workroom" selected` | `[selected]` attribute present when on that workroom page |

**Workroom Homepage (after navigating to workroom):**

| Element | Locator | Value |
|---|---|---|
| Workroom name heading | `role=heading level=1 name="UI-Workroom"` | Heading with workroom name |
| Breadcrumb nav | `navigation "Breadcrumbs"` | Shows "UI-Workgroup > UI-Workroom" |
| Workroom tabs | `tablist "Workroom navigation tabs"` | Tabs: Home, Events, Library, Collaborate, Members |
| Home tab active | `role=tab name="Home" selected` | Tab selected by default on workroom home |
| Page URL | `/workrooms/5732` | |
| Page title | "Workroom home" | |

### 3d. Library

| Property | Value |
|---|---|
| Locator | `role=link` with `generic "Library"` (no accessible name on link element itself) |
| Best locator | `role=link` containing text `"Library"` filtered by navigation dialog context |
| URL | `/library` |
| Destination | Library page |

**Library Page:**

| Element | Locator | Value |
|---|---|---|
| Page heading h1 | `role=heading level=1 name="Library"` | "Library" |
| Sub-heading h2 | `role=heading level=2` | "Browse resources, workgroup and workroom collections, private and secured files" |
| Resource library row | `role=row name="Resource library"` | Global resources folder |
| UI-Workgroup row | `role=row name="UI-Workgroup Workgroup"` | Workgroup folder with "Workgroup" label |
| Secured folders row | `role=row name="Secured folders"` | |
| Private folder row | `role=row name="Private folder"` | |
| Page URL | `/library` | |
| Page title | "Library" | |

**Library access restriction:** A user with 'Deactivate Resources' permission CANNOT see the Library link in the sidebar. Use `MODERNIZED_BOARD_MEMBER_14_USEREMAIL` (env var) — pre-configured with "Resources deactivated" — to test this without changing permissions dynamically.

### 3e. Directory

| Property | Value |
|---|---|
| Locator | `role=link name="Directory"` |
| URL | `/directory/users` |

### 3f. Messaging

| Property | Value |
|---|---|
| Locator | `role=link name="Messaging"` |
| URL | `/?open_modal=message` |

### 3g. Minutes

| Property | Value |
|---|---|
| Locator | `role=link name="Minutes"` |
| URL (href) | `/extensions/minutes` |
| **Opens in new tab** | YES — confirmed. New tab opens at `https://minutes-orca01.diligentdatasystems.com/api/launch` |
| Visual indicator | External link icon (`img`) present after "Minutes" text in sidebar |
| Original tab | Stays on current page — **unaffected** |

### 3h. Approvals

| Property | Value |
|---|---|
| Locator | `role=link name="Approvals"` |
| URL | `/approvals` |
| Badge | Shows count (e.g. "4") as child `generic` element |

### 3i. Site settings (System Admin only)

| Property | Value |
|---|---|
| Locator | `role=link name="Site settings"` (lowercase 's') |
| URL | `/?open_modal=settings` |
| Note | Label is lowercase ("Site settings") in new-style sidebar. Legacy sidebar uses "Site Settings" (uppercase S). |

---

## 4. Roles With Access

| Role | Back to platform | Home | Workrooms | Library | Minutes | Site settings |
|---|---|---|---|---|---|---|
| System Admin | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ (SA only) |
| Workroom Admin | ✅ (assumed) | ✅ (assumed) | ✅ (assumed) | ✅ (assumed) | ✅ (assumed) | ❌ |
| Board Members (incl. BM14) | ✅ (assumed) | ✅ (assumed) | ✅ (assumed) | ❌ BM14 (Resources deactivated) | ✅ (assumed) | ❌ |

> Other roles not yet explored — assumptions above come from `.env.dev` role descriptions.

---

## 5. Key Behaviors Summary

| Behavior | Detail |
|---|---|
| Side nav is a dialog | New-style sidebar renders as `role=dialog` — must open it first before interacting with nav links |
| Workrooms is a button not a link | `role=button name="Workrooms"` — clicking toggles expand/collapse; does NOT navigate |
| Active workroom = `[selected]` treeitem | When on `/workrooms/5732`, the `UI-Workroom` treeitem has `[selected]` attribute |
| Home active state = visual only | No `aria-current`/`[selected]` attr on the Home link when on `/home-new` |
| Library is permission-gated | `MODERNIZED_BOARD_MEMBER_14_USEREMAIL` has Library hidden (no sidebar link visible) |
| Minutes opens new tab | Confirmed — `/extensions/minutes` always opens `minutes-orca01.diligentdatasystems.com` in a new browser tab |
| Back to platform exits app | Goes to `https://mod-lab-auto.home.diligentoneplatform-dev.com` — the Diligent One Platform launchpad |

---

## 6. Sign Out (New-Style)

| Element | Locator |
|---|---|
| Profile menu button | `role=button name="Open profile menu"` |
| (Profile menu contents not yet captured — exploration skipped for this context) | |

> For sign-out in test steps, navigate to `/home/index` or use the profile button on the old home page where Sign Out is confirmed captured: `role=link name="Sign Out"`.
