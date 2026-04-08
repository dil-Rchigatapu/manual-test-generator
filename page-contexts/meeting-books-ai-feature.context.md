# Meeting Books – AI Feature Page Context

Explored by: Explorer Agent via Playwright MCP (System Admin)
Date: 2026-04-08
Workroom: UI-Workroom (id: 5732) in UI-Workgroup
App Base URL: `https://mod-lab-auto.boardeffect.diligentoneplatform-dev.com`

---

## URL Patterns

| Page | URL |
|---|---|
| Meeting Books list | `/workrooms/{workroom_id}/library/books` |
| Create book | `/workrooms/{workroom_id}/library/books/new` |
| Book builder (edit) | `/workrooms/{workroom_id}/library/books/{book_id}/edit` |
| Book details | `/workrooms/{workroom_id}/library/books/{book_id}` |

---

## Meeting Books List Page

| Element | Locator |
|---|---|
| Workroom Library tab | `role=tab name="Library" selected` |
| Meeting books tab | `role=tab name="Meeting books" selected` |
| Create book link | `role=link name="Create book"` → `/workrooms/{id}/library/books/new` |
| Card view toggle | `role=button name="Card view" pressed` |
| List view toggle | `role=button name="List view"` |
| Search field | `role=textbox name="Book title"` (placeholder: Search) |
| Sort by combobox | `role=combobox name="Sort by First created"` |
| Filters button | `role=button name="Filters"` |
| Book card title link | `role=link name="{book-title}"` → `/workrooms/{id}/library/books/{book-id}` |
| Book status (built+published) | `generic "{Book Title} Book is Published"` → text: "Built" |
| Book status (not built) | `generic "{Book Title} Book is Not Published"` → text: "Not built" |
| Book status (not visible) | `generic "Book is Not Visible"` → text: "Not visible" |
| GovernAI button (card — built books) | `role=button name="GovernAI"` |
| Open book builder link | `role=link name="Open book builder"` → `/workrooms/{id}/library/books/{book-id}/edit` |
| Download button | `role=button name="Download"` |
| Open book button | `role=button name="Open book"` |
| More options button | `role=button name="More options"` |
| Visible to workroom checkbox (card) | `role=checkbox name="Visible to workroom"` |

> **Note:** GovernAI button only appears in the card actions for **Built** books. Not-built books only show "Open book builder".

---

## Create Book Page (`/library/books/new`)

| Element | Locator |
|---|---|
| Page heading | `role=heading level=1 name="New book"` |
| Title field (required) | `role=textbox name="Title"` |
| Template selector | `role=combobox name="Template Select"` |
| Generate coverpage checkbox | `role=checkbox name="Generate coverpage"` |
| Generate agenda checkbox | `role=checkbox name="Generate agenda"` |
| Upload own book button | `role=button name="Open Upload own book"` |
| Advanced settings button | `role=button name="Open Advanced settings"` |
| Save and continue button | `role=button name="Save and continue"` |
| Cancel button | `role=button name="Cancel"` |

---

## Book Builder – Edit Book Page (`/library/books/{id}/edit`)

| Element | Locator |
|---|---|
| Page title | "Edit book" |
| Book title heading | `role=heading level=1 name="{book-title}"` |
| Go Back button | `role=button name="Go Back"` |
| Build book button | `role=button name="Build book"` |
| More options (header) | `role=button name="More options"` |
| Meeting book tab | `tablist "meeting book navigation"` → `tab "Meeting book" selected` |
| Event tab | `tab "Event"` |
| Workflow approvals tab | `tab "Workflow approvals"` |
| Add main category (top) | `role=button name="Add main category"` |
| Preview button | `role=button name="Preview"` |
| Collapse/Expand all | `role=button name="Expand all Accordions"` / `role=button name="Collapse all Accordions"` |
| Visible to workroom checkbox | `role=checkbox name="Visible to workroom"` |
| Generate cover checkbox | `role=checkbox name="Generate coverpage" checked` |
| Generate agenda checkbox | `role=checkbox name="Generate agenda" checked` |
| Category section header | `role=button name="Collapse {category-name}"` (expanded) |
| Add to category button | `role=button name="Add to {category-name}"` |
| More options for category | `role=button name="More options for {category-name}"` |
| File drop zone heading | `role=heading level=2` "Drag files here or select here for more options" |
| File upload area button | `role=button name="select here for more options"` |
| File link in category | `role=link name="Open file {filename}"` → `/workrooms/{id}/books/{book-id}/bookfolders/{folder-id}/preview` |
| Add main category (bottom) | `role=button name="Add main category"` (second instance at page bottom) |

---

## Edit and Unpublish Confirmation Dialog

Appears when clicking **"Edit book"** on a **Published** book.

| Element | Locator |
|---|---|
| Dialog container | `role=dialog name="Edit and unpublish Close"` |
| Heading | `role=heading level=2 name="Edit and unpublish"` |
| Body text | "Are you sure you want to edit and unpublish this meeting book? It won't be available to attendees until it's republished." |
| Cancel button | `role=button name="Cancel"` |
| Confirm (edit) button | `role=button name="Edit and unpublish"` |
| Close (X) button | `role=button name="Close"` |

---

## Rebuild Confirmation Modal

Appears when clicking **"Build book"** on an **already-built** book.

| Element | Locator |
|---|---|
| Dialog container | `role=dialog name="Building your book"` |
| Modal heading | `role=heading level=2 name="Rebuild book"` |
| Warning text | "Rebuilding the book makes the current AI content outdated." |
| Body | "Do you want to rebuild the book and regenerate the AI content? Regenerating will use your current edits. Do you want to rebuild the book only? The current AI content will remain and you can regenerate it later." |
| Rebuild book only button | `role=button name="Rebuild book only"` |
| Rebuild & generate AI button | `role=button name="Rebuild & generate AI"` |
| Close button | `role=button name="Close"` |

---

## First-Time Build Dialog

When clicking **"Build book"** on a book that has **never been built before** (no prior build exists), the dialog transitions **immediately** to the success state — no progressbar or percentage text is shown.

| Element | Locator |
|---|---|
| Dialog container | `role=dialog name="Building your book"` |
| Heading | `role=heading level=2 name="Building your book"` |
| Success paragraph | `role=paragraph` "Book built" (immediate — no progress bar) |
| Close button | `role=button name="Close"` |
| Attach or create event | `role=button name="Attach or create event"` |

> **Note:** For first-time builds the dialog skips the progress/loading state and goes directly to "Book built". The progressbar and "Processing... XX%" paragraph only appear during **Rebuild** flows (when a prior build exists).

---

## Build Progress Dialog (Rebuild Only)

Only shown during **Rebuild** flows (clicking "Rebuild book only" or "Rebuild & generate AI" in the rebuild modal). Same dialog container — state changes after action.

| Element | Locator |
|---|---|
| Dialog container | `role=dialog name="Building your book"` |
| In-progress heading | `role=heading level=2 name="Building your book"` |
| Progress bar | `role=progressbar` |
| Progress text | `role=paragraph` "Processing... XX%" |
| Info text | `role=paragraph` "This may take a few minutes" |

---

## Build Success Dialog

Same container, post-completion state.

| Element | Locator |
|---|---|
| Dialog container | `role=dialog name="Building your book"` |
| Heading (kept) | `role=heading level=2 name="Building your book"` |
| Success icon | `role=img` (checkmark) |
| Success text | `role=paragraph` "Book built" |
| Supplementary text | "This book is not attached to any event yet. Attach an event now or open the Event tab later to attach an event." |
| Close button | `role=button name="Close"` |
| Attach or create event | `role=button name="Attach or create event"` |

---

## Book Details Page (`/library/books/{id}`)

| Element | Locator |
|---|---|
| Page title | "Book details" |
| Book title heading | `role=heading level=1 name="{book-title}"` |
| Book status (built) | `generic "Book is Published"` → text: "Built" |
| Last published text | `role=paragraph` "Last Published: {date} by {user}" |
| Go back button | `role=button name="Go back"` |
| Edit book button | `role=button name="Edit book"` |
| More options button | `role=button name="More options"` |
| Book view tabs | `tablist "Book view tabs"` → Meeting book (selected), Event |
| GovernAI button | `role=button name="GovernAI"` |
| Take minutes link | `role=link name="Take minutes"` → `/extensions/minutes?book_id={id}&committee_id={workroom_id}` |
| View book button | `role=button name="View book"` |
| More button | `role=button name="More"` |
| Expand/Collapse all | `role=button name="Expand all Accordions"` / `role=button name="Collapse all Accordions"` |
| Visible to workroom | `role=checkbox name="Visible to workroom"` |
| Section accordion (expanded) | `role=button name="Collapse {section-name}"` |
| File link in section | `role=link name="{filename}"` |
| Meeting books breadcrumb | `role=link name="Meeting books"` → `/workrooms/{id}/library/books` |

---

## GovernAI Panel – Initial State (No AI Generated)

Panel opens as `role=dialog [active]` from the right side.

| Element | Locator |
|---|---|
| Panel dialog | `role=dialog [active]` |
| GovernAI heading | `role=heading level=2 name="GovernAI"` |
| Close panel button | `role=button name="Close governAI panel"` |
| Section heading | `role=heading level=4 name="Do more with AI"` |
| Description | "Generate a summary and insights using AI-powered tools to enhance your meeting book." |
| Generate AI content button | `role=button name="Generate AI content"` |
| Smart Summary card | `role=paragraph name="Smart Summary"` subtitle: "A concise summary of the meeting book." |
| Smart Prep card | `role=paragraph name="Smart Prep"` subtitle: "Meeting preparation document with a preview of key topics and questions from the meeting book." |
| Footer Close button | `role=button name="Close"` |

---

## GovernAI Panel – AI Generated & Published State

After AI content has been generated and published for both items.

| Element | Locator |
|---|---|
| Generated on text | `role=paragraph` "Generated on: {date}" |
| Last contributor text | `role=paragraph` "Last contributor: {email}" |
| Smart Summary card | `role=paragraph name="Smart Summary"` → description visible, no "Review & publish" button (published) |
| Smart Prep card | `role=paragraph name="Smart Prep"` → description visible, no "Review & publish" button (published) |
| Footer Close button | `role=button name="Close"` |
| Footer Regenerate button | `role=button name="Regenerate AI content"` |

---

## GovernAI Panel – Outdated State (After "Rebuild Book Only")

| Element | Locator |
|---|---|
| Panel dialog | `role=dialog name="GovernAI"` |
| Outdated warning note | `role=note` containing text "AI content is outdated." |
| Generated on text | `role=paragraph` "Generated on: {date}" |
| Last contributor text | `role=paragraph` "Last contributor: {email}" |
| Smart Summary row | `role=paragraph name="Smart Summary"` + status `generic "Not visible"` + `role=button name="Review & publish"` |
| Smart Prep row | `role=paragraph name="Smart Prep"` + status `generic "Not visible"` + `role=button name="Review & publish"` |
| Footer Close | `role=button name="Close"` |
| Footer Regenerate | `role=button name="Regenerate AI content"` |

---

## GovernAI Panel – Mixed State (One Item Published, One Not)

When Smart Summary has been published but Smart Prep has not yet.

| Element | Locator |
|---|---|
| Outdated note | `role=note` "AI content is outdated." — **still present** until both items are published |
| Smart Summary row | `generic "Visible"` status + `role=button name="Review & unpublish"` |
| Smart Prep row | `generic "Not visible"` status + `role=button name="Review & publish"` |
| Footer Regenerate | `role=button name="Regenerate AI content"` — still present |

---

## GovernAI Panel – Fully Published State (Both Items Published)

| Element | Locator |
|---|---|
| No outdated note | `role=note` "AI content is outdated." is **absent** |
| Generated on text | `role=paragraph` "Generated on: {date}" — still present |
| Smart Summary row | `generic "Visible"` status + `role=button name="Review & unpublish"` |
| Smart Prep row | `generic "Visible"` status + `role=button name="Review & unpublish"` |
| Footer Close | `role=button name="Close"` — **only** footer button; "Regenerate AI content" is absent |

> **Key behavior:** The `"Regenerate AI content"` footer button disappears once both Smart Summary and Smart Prep are in "Visible" (published) state.

---

## Smart Summary Sub-view (inside GovernAI Panel)

| Element | Locator |
|---|---|
| Sub-view heading | `role=heading level=2 name="Smart Summary - {Book Title}"` |
| Go back button | `role=button name="Go back"` |
| Close panel button | `role=button name="Close governAI panel"` |
| Alert (book NOT visible) | `role=alert` "You can only publish the Smart Summary after you make the book visible." |
| AI label | text: "Generated by AI" |
| Executive Summary heading | `role=heading level=2 name="Executive Summary"` |
| Detailed Summary heading | `role=heading level=2 name="Detailed Summary"` |
| AI accuracy disclaimer | "AI-generated content may have inaccuracies." + `role=link name="Learn more"` |
| Footer Edit button | `role=button name="Edit"` |
| Footer Download button | `role=button name="Download"` |
| Footer Publish button (book visible) | `role=button name="Publish"` — **enabled** |
| Footer Publish button (book NOT visible) | `role=button name="Publish" [disabled]` — disabled, visibility alert shown |

> **Verified:** When "Visible to workroom" is OFF → Publish is `[disabled]` and the visibility alert appears. When "Visible to workroom" is ON → Publish is enabled (no `[disabled]` attribute) and the alert is absent.

## Smart Summary Sub-view – Post-Publish State

| Element | Locator |
|---|---|
| Publish confirmation alert | `role=alert` "Smart Summary published" + `role=button name="Close"` |
| Published on timestamp | `role=paragraph` "Published on: {date/time}" |
| AI label | text: "Generated by AI" |
| Footer Unpublish button | `role=button name="Unpublish"` — replaces "Publish" after publishing |

---

## Smart Prep Sub-view (inside GovernAI Panel)

Follows the same structure as Smart Summary sub-view.

| Element | Locator |
|---|---|
| Sub-view heading | `role=heading level=2 name="Smart Prep - {Book Title}"` |
| Go back button | `role=button name="Go back"` |
| Alert (book NOT visible) | `role=alert` "You can only publish the Smart Prep after you make the book visible." |
| Footer Publish button (book visible) | `role=button name="Publish"` — **enabled** |
| Footer Publish button (book NOT visible) | `role=button name="Publish" [disabled]` — disabled |

## Smart Prep Sub-view – Post-Publish State

| Element | Locator |
|---|---|
| Publish confirmation alert | `role=alert` "Smart Prep published" + `role=button name="Close"` |
| Published on timestamp | `role=paragraph` "Published on: {date/time}" |
| Footer Unpublish button | `role=button name="Unpublish"` — replaces "Publish" after publishing |

---

## Notes

- GovernAI button: use `role=button name="GovernAI"` as the primary locator.
- The GovernAI panel `dialog [active]` is a right-side sidesheet that overlaps the page.
- For AI to be publishable, the book must first be made **Visible to workroom** (checkbox on book details and book builder pages).
- **First-time build skips progress:** When building a book for the first time, the build dialog (`role=dialog name="Building your book"`) transitions IMMEDIATELY to success state ("Book built") — no progressbar or "Processing... XX%" is shown. The progress/loading state only appears during REBUILD flows.
- **Progressbar only during Rebuild:** The `role=progressbar` and "Processing... XX%" paragraph are only visible when rebuilding an already-built book (choosing "Rebuild book only" or "Rebuild & generate AI" from the rebuild modal).
- **Generate AI content requires document content:** Calling `api/v5/books/{id}/summaries/generate` returns `422 Unprocessable Entity` if the book contains only URL links. AI generation requires actual file attachments (e.g., PDF files) to extract content.
- **Publish button depends on "Visible to workroom":** In the Smart Summary and Smart Prep sub-views, the Publish button is `[disabled]` and an alert appears if "Visible to workroom" is OFF. When visibility is ON, Publish is enabled with no alert.
- "Rebuild book only" vs "Rebuild & generate AI": choosing "Rebuild book only" preserves the existing AI content but marks it as **outdated**.
- After "Rebuild book only": GovernAI panel shows `role=note` with "AI content is outdated." + both Smart Summary and Smart Prep show "Not visible" status and "Review & publish" buttons.
- Outdated state toast on book details: "The AI content is outdated" toast with "Dismiss" and "Regenerate summary" buttons may appear briefly after rebuild completion.
- Test environment explored: book id 30818 ("r") in Workroom 5732; book id 30833 (Automation_89385) used for first-time build verification.

---

## INDEX Entry (for page-contexts/INDEX.md)

| File | Pages Covered |
|---|---|
| `meeting-books-ai-feature.context.md` | Meeting Books list, Create Book, Book Builder, Book Details, GovernAI Panel (all states), Smart Summary sub-view, Smart Prep sub-view, Rebuild Modal, Build Progress/Success dialogs |
