# CLAUDE.md

## Hard rule: no writes without approval

**Never write to the spreadsheet without explicit user approval first.**
This applies to every write tool (`update_cells`, `batch_update_cells`,
`add_rows`, `add_columns`, `create_sheet`, `rename_sheet`, `batch_update`,
`add_chart`, `copy_sheet`, `share_spreadsheet`, etc.) — no exceptions for
"small" or "obviously correct" edits. Propose the change, wait for a yes,
then execute. This is a hard rule, not a suggestion — treat it the same
way as any other irreversible/shared-state action.

Read-only tools (`get_sheet_data`, `list_sheets`, `find_in_spreadsheet`,
`get_sheet_formulas`, etc.) are fine to use freely.

## Hard rule: docs stay in sync with features

**A feature/mechanic change to the spreadsheet is not done until the
matching doc is updated in the same turn.** "Added the feature, docs are
stale" is not an acceptable end state — update the relevant doc under `docs/`
as part of finishing the change, not as a follow-up.

## Access

- Access is granted per spreadsheet file, not on the containing Drive
  folder. `DRIVE_FOLDER_ID` doesn't resolve for the service account, so
  `list_spreadsheets`/folder-based tools return empty — use a spreadsheet
  ID directly instead.

See `README.md` for MCP server setup details.
