# Page Contexts — Exploration Index

> This index tracks which pages have been explored. Update it every time the Explorer Agent saves a new context file.

| Context File | Module | Sub-Module | Explored On | Roles Explored | Status |
|---|---|---|---|---|---|
| home.context.md | Home | Home Dashboard | 2026-04-08 | System Admin (MODERNIZED_SYSTEM_ADMIN_USEREMAIL) | Complete — other roles pending |
| home-navigation.context.md | Navigation | Side Navigation (new-style) | 2026-04-08 | System Admin (MODERNIZED_SYSTEM_ADMIN_USEREMAIL) | Complete for SA — BM14 Library restriction noted; other roles pending. Tests generated: BEW-T10535–T10538, T10541 |

---

## How to Read Context Files
Each `.context.md` file in this folder is a structured snapshot of a page/feature. See `_SCHEMA.md` for the full format.

## How to Add a New Context
1. Run the **Explorer Agent** (`agents/01-explorer.prompt.md`) and tell it which page to explore.
2. The agent saves a new `.context.md` file here.
3. Add an entry to the table above.

## Naming Convention
`<module>-<submodule>[-<variant>].context.md` — all kebab-case lowercase.

Examples:
- `login.context.md`
- `meeting-books-create.context.md`
- `workroom-library.context.md`
- `users-invite.context.md`
- `settings-org.context.md`
