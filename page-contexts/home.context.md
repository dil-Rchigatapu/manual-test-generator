# Page Context: Home Page

## Meta
- **Explored On:** 2026-04-08
- **Explorer Role Used (Primary):** MODERNIZED_SYSTEM_ADMIN_USEREMAIL (System Admin1)
- **URL Path:** /home/index
- **Full URL:** https://mod-lab-auto.boardeffect.diligentoneplatform-dev.com/home/index
- **Module:** Home
- **Sub-Module:** Home Dashboard
- **Screenshot:** screenshots/home-system-admin.png (pending — screenshots folder exists)

---

## Navigation Breadcrumb
> How to reach this page from login

1. Navigate to `https://mod-lab-auto.boardeffect.diligentoneplatform-dev.com`
2. Enter credentials on the OIDC login page
3. Click 'Sign in' button
4. User is redirected directly to the Home page at `/home/index`
5. Alternatively, click 'Home' link in the left sidebar navigation

---

## Roles With Access
> Only System Admin explored in this run. All roles with any login access land on this page after login.

| Role | Env Var | Can Access? | Differences vs System Admin |
|---|---|---|---|
| System Admin | MODERNIZED_SYSTEM_ADMIN_USEREMAIL | Yes | Full access — baseline. Has 'Site Settings' in sidebar. Has 'Edit' on Welcome widget. Directory URL is `/directory/admin`. |
| Workroom Admin | MODERNIZED_WORKROOM_ADMIN_USEREMAIL | Yes (assumed) | Likely no 'Site Settings' sidebar item. To be verified. |
| Board Member 1–15 | MODERNIZED_BOARD_MEMBER_*_USEREMAIL | Yes (assumed) | No 'Site Settings'. Directory URL likely `/directory`. To be verified per role. |
| WA not in UI Workroom | MODERNIZED_WORKROOM_ADMIN_NOT_PART_OF_UI_WORKROOM | Yes (assumed) | No 'UI-Workgroup' in sidebar workgroup section. To be verified. |
| BM not in UI Workroom | MODERNIZED_BOARD_MEMBER_NOT_PART_OF_UI_WORKROOM | Yes (assumed) | No 'UI-Workgroup' in sidebar workgroup section. To be verified. |

---

## Page Layout

The page has three major layout zones:

1. **Top System Bar (Diligent One Platform header)** — spans full width above everything. Contains org switcher, PAI Search, notifications bell, help menu, profile avatar.
2. **Left Sidebar** — fixed vertical nav on the left. Contains main navigation items (Home, Workrooms, Library, Directory, Messaging, Minutes, Approvals, Site Settings) and a Workgroup section below the main nav.
3. **Main Content Area** — right of sidebar. Contains a search bar at the top, page heading "Home", any dismissable info banners, and home widgets (Welcome accordion, Meetings accordion).

Additionally, a **Top Drawer** overlay (Approvals/Workflows) is anchored to the top and can be expanded.

---

## UI Elements

### Buttons

| Label (exact text) | Locator | Location | Visible To | Enabled When | Action Triggered |
|---|---|---|---|---|---|
| Sign in | `role=button name="Sign in"` | Login page — below password field | All | Always | Submits login credentials |
| Dismiss (support access banner) | `role=button name="Dismiss"` | Home main content — support access info banner | System Admin | When banner is visible | Dismisses the info banner |
| Export all | `role=button name="Export all"` | Home main content — Meetings accordion header | All | Always | Exports all meetings/calendar events |
| Overflow Menu Help | `role=button name="Overflow Menu Help"` | Top system bar — right side | All | Always | Opens help/overflow menu |
| Profile avatar button | `id="atlas-navbar-profile-dropdown"` | Top system bar — far right | All | Always | Opens profile dropdown menu (Profile link, Sign Out link) |

### Forms / Input Fields

| Field Label (exact) | Locator | Type | Required? | Placeholder Text | Validation Rule | Located In |
|---|---|---|---|---|---|---|
| Email | `role=textbox name="Email"` | Text input (email) | Yes | Enter email | Valid email format required | Login page |
| Password | `role=textbox name="Password"` | Password input | Yes | Enter password | Cannot be blank | Login page |
| Keyword(s) | `role=textbox name="Keyword(s)"` | Text input | No | (empty) | None visible | Home page — search bar in main content area |

### Dropdowns / Selects

| Label (exact) | Locator | Options | Default | Located In |
|---|---|---|---|---|
| Organization switcher | `aria-label="Organization switcher. Currently in Mod-lab-auto"` | (depends on user memberships) | Mod-lab-auto | Top system bar |

### Tables / Lists

| Table Name | Locator (container) | Columns | Sortable Columns | Filterable? | Pagination? | Row Action Locators |
|---|---|---|---|---|---|---|
| Approvals list (drawer) | `role=tabpanel` under `tab "Approvals"` | File name (link), Requested by, "Response Required" action link, approval question text | No | No | Not visible | `link "Response Required"` per item |
| Pending Approvals items | each item is a `role=tab` inside Approvals drawer | File thumbnail, filename link, requester, "Response Required" CTA | No | No | No | `link "Response Required"` — navigates to `/resources/<id>/approvals/<id>/respond` |

### Modals / Sidesheets

| Trigger (locator) | Modal/Sidesheet Title | Title Locator | Content / Fields (with locators) | Footer Actions (with locators) |
|---|---|---|---|---|
| Profile avatar button `id="atlas-navbar-profile-dropdown"` | (dropdown menu, not modal) | n/a | User display name, email, `link "Profile"` → `/users/48287`, `link "Sign Out"` → `/logout` | None — links navigate |

### Navigation Items — Left Sidebar

| Label (exact) | Locator | URL / Route | Visible To |
|---|---|---|---|
| Back to Platform | `role=link name="Back to Platform"` | https://mod-lab-auto.home.diligentoneplatform-dev.com | All |
| Home | `role=link name="Home"` | /home/index | All |
| Workrooms | `role=link name="Workrooms"` | `#` (expandable — opens submenu) | All |
| Library | `role=link name="Library"` | /resources | All (access varies by permissions) |
| Directory | `role=link name="Directory"` | /directory/admin (System Admin) | All — URL path differs by role |
| Messaging | `role=link name="Messaging"` | /new_message?type=directory | All |
| Minutes | `role=link name="Link to minutes"` | /extensions/minutes (expandable) | All |
| Approvals | `role=link name="3 Approvals"` | `#` (opens top drawer) | All — badge count shows pending approvals |
| Site Settings | `role=link name="Site Settings"` | /settings/index | **System Admin only** |

### Navigation Items — Top Drawer (Approvals & Workflows)

| Label (exact) | Locator | Content |
|---|---|---|
| Approvals tab | `role=tab name="Approvals"` | List of pending approval documents. Each has filename link, requester, "Response Required" CTA and approval question. Also has "Manage" link → `/drawer/manage` |
| Workflows tab | `role=tab name="Workflows"` | (not explored in this run) |

### Navigation Items — Workgroup Section (bottom of sidebar)

| Label (exact) | Locator | URL / Route | Visible To |
|---|---|---|---|
| UI-Workgroup | `role=link name="UI-Workgroup"` | `#` (expandable) | Users who are members of UI-Workgroup |

### Navigation Items — Profile Dropdown

| Label (exact) | Locator | URL / Route |
|---|---|---|
| Profile | `role=link name="Profile"` | /users/48287 (user ID varies per user) |
| Sign Out | `role=link name="Sign Out"` | /logout |

### Home Page Widgets — Main Content

| Widget | Locator | Content | Actions Available |
|---|---|---|---|
| Welcome accordion | `role=heading name="Welcome"` parent section | Expandable welcome message panel (expanded by default) | 'Edit' link `role=link name="Edit"` → `/home/welcome_edit` — **System Admin only** |
| Meetings accordion | `role=heading name="You have no meetings scheduled at this time"` parent tab | Shows upcoming meetings. When empty: "You have no meetings scheduled at this time" with count "0" | 'Open calendar' link → `/events/calendar`; 'Export all' button |
| Privacy Policy link | `role=link name="Privacy Policy"` | Footer of main content | → https://www.boardeffect.com/privacy-policy |

### Search Bar

| Element | Locator | Located In |
|---|---|---|
| Search trigger link | `role=link name="Search"` | Top of main content area |
| Keyword(s) input | `role=textbox name="Keyword(s)"` | Search bar in main content |
| Submit button | `role=button name="Submit"` | Search bar in main content |

### Top System Bar

| Element | Locator | Notes |
|---|---|---|
| Diligent logo | `img` inside top banner | Links back to org root |
| Organization switcher | `aria-label="Organization switcher. Currently in Mod-lab-auto"` | Shows current org name "Mod-lab-auto" |
| PAI Search | `aria-label="PAI Search"` | AI-powered search |
| Notifications bell | `aria-label="Alert With 2"` | Badge shows unread notification count (2 for System Admin) |
| Help / Overflow Menu | `role=button name="Overflow Menu Help"` | Help and additional options |
| Profile avatar | `id="atlas-navbar-profile-dropdown"` | Opens dropdown with Profile + Sign Out |

### Toast / Notification Messages

| Triggered By | Type | Exact Message Text | Locator |
|---|---|---|---|
| Successful login | Info | 'You have successfully logged in. You last signed in on [date/time].' | `role=alert aria-label="Info"` |
| Support team access | Info | 'Access has been granted to the BoardEffect Support Team. To disable this access, please update your security settings.' | inline `generic` in main content — dismiss button `role=button name="Dismiss"` |

---

## Key User Actions

| Action | Who Can Do It | Pre-condition | Notes |
|---|---|---|---|
| Log in | All | Valid credentials | Redirected to Home after login |
| View pending approvals | All (only users with pending items see count) | At least one approval pending | Approvals count shown in badge on sidebar link and drawer tab |
| Respond to an approval | All users who are approvers | Document requires their approval | Navigates to `/resources/<id>/approvals/<id>/respond` |
| Edit Welcome message | System Admin only | Logged in as System Admin | 'Edit' link in Welcome widget → `/home/welcome_edit` |
| Open calendar | All | None | → /events/calendar |
| Export all meetings | All | None | Triggers export of calendar/meetings data |
| Search keyword | All | None | Keyword search from Home page |
| Navigate to Site Settings | System Admin only | Logged in as System Admin | Sidebar item — not visible to other roles |
| Sign Out | All | Logged in | Via profile dropdown → /logout → redirected to login page |
| Dismiss support access banner | System Admin | Banner is visible | Closes the banner |

---

## Permission-Gated Behaviors

- **'Site Settings'** sidebar nav item — visible **only to System Admin**. Not present for Workroom Admins or Board Members.
- **'Edit' on Welcome widget** — visible **only to System Admin**. Other roles can view welcome text but cannot edit.
- **Directory URL** — System Admin navigates to `/directory/admin`. Other roles likely navigate to `/directory` without the `/admin` segment (to be confirmed).
- **Approvals badge count** — shows only the count of approvals pending for the **logged-in user**. Will be different per role or zero if none pending.
- **Notifications bell badge** — count varies by user. System Admin had "2".
- **Workgroup section** — shows workgroup links only if the user is a member of at least one workgroup. Users not in UI-Workgroup will not see "UI-Workgroup" link.

---

## Error / Edge States Observed

| State | Trigger | Exact Message Text | Message Locator | What else changes on UI |
|---|---|---|---|---|
| Successful login | Any successful login | 'You have successfully logged in. You last signed in on [weekday, date time].' | `role=alert aria-label="Info"` with 'Dismiss' button | Alert auto-visible on Home page after login |
| Support access granted | When BoardEffect support team has been granted access | 'Access has been granted to the BoardEffect Support Team. To disable this access, please update your security settings.' | `generic` info panel with `role=button name="Dismiss"` and `role=link name="update your security settings"` | Panel shown on Home page — dismissable |
| No meetings | No meetings scheduled | 'You have no meetings scheduled at this time' with count '0' | `role=heading` inside meetings accordion tab | 'Open calendar' and 'Export all' still visible |

---

## Related Pages

| Page Name | Context File | Relationship |
|---|---|---|
| Login | login.context.md (not yet created) | Entry point — redirects to Home after success |
| Welcome Edit | (not yet created) | Navigated to via 'Edit' in Welcome widget — System Admin only |
| Calendar | (not yet created) | Navigated to via 'Open calendar' link |
| Approvals Respond | (not yet created) | Navigated to via 'Response Required' in Approvals drawer |
| Site Settings | (not yet created) | Navigated to via 'Site Settings' sidebar — System Admin only |
| Library | (not yet created) | Navigated to via 'Library' sidebar |
| Directory | (not yet created) | Navigated to via 'Directory' sidebar — URL differs per role |
| Messaging | (not yet created) | Navigated to via 'Messaging' sidebar |
| Minutes | (not yet created) | Navigated to via 'Minutes' sidebar |
| Profile | (not yet created) | Navigated to via profile dropdown → 'Profile' |

---

## Notes / Observations

- The login page lives on a **separate OIDC domain** (`oidc.diligentoneplatform-dev.com`) — Playwright must follow the redirect chain from the app URL to reach it.
- The `Sign in` button reference may timeout due to navigation wait — allow sufficient timeout after click.
- The **Approvals top drawer** slides in from the top of the page and is always visible in a collapsed/expanded state. It is separate from the main page content.
- **'Site Settings'** is a clear System Admin-only element — useful as a role verification checkpoint in any test that needs to confirm System Admin access.
- The profile dropdown is opened by clicking `id="atlas-navbar-profile-dropdown"` — the ref `e94` was resolved to this ID.
- The **Directory** link URL is `/directory/admin` for System Admin. This `/admin` suffix is likely role-dependent — confirm for other roles.
- The **Workgroup section** ("UI-Workgroup") appears at the bottom of the left sidebar, below the main nav list — it is separate from the main nav `list` element.
- The 'Manage' link inside Approvals drawer → `/drawer/manage` was only observed for System Admin.
- Console errors were present (5–8 errors) on page load — these appear to be pre-existing non-blocking JS errors in the app, not caused by the exploration.
