# sheets-mcp

Google Sheets access for Claude Code via MCP — read and (with approval) edit
spreadsheets directly, no CSV export/import round-trips.

Uses the community server [`xing5/mcp-google-sheets`](https://github.com/xing5/mcp-google-sheets)
over a Service Account — not Google's own `sheetsmcp.googleapis.com`, which requires
enrollment in the Google Workspace Developer Preview Program and rejects plain
`@gmail.com` accounts.

## Prerequisites

- [`uv`](https://astral.sh/uv) installed (`curl -LsSf https://astral.sh/uv/install.sh | sh`)
- A GCP project with **Google Sheets API** and **Google Drive API** enabled
- A Service Account in that project, with a downloaded JSON key
- The target spreadsheet(s) shared with the Service Account's email, **Editor** access

The Service Account needs no IAM role on the GCP project itself — access is governed
entirely by sharing the file in Drive, same as sharing with any other Google account.

## Getting started

1. Create the Service Account (GCP Console → IAM & Admin → Service Accounts →
   Create, no project role needed) and download its JSON key.
2. Share the spreadsheet with the Service Account's `client_email` (found in the
   key file), Editor access.
3. Lock down the key file permissions — it's a plaintext private key:
   ```
   chmod 600 sheets-mcp-*.json
   ```
4. Register the MCP server:
   ```
   claude mcp add sheets \
     -e SERVICE_ACCOUNT_PATH=/absolute/path/to/service-account-key.json \
     -e DRIVE_FOLDER_ID=<drive-folder-id-containing-the-sheet> \
     -- uvx --with 'mcp<2' mcp-google-sheets@latest
   ```
   `DRIVE_FOLDER_ID` comes from the URL of the Drive folder the sheet lives in
   (`.../folders/<this-part>`). It only matters for the `list_spreadsheets` tool —
   direct access by spreadsheet ID works regardless.
5. Restart the Claude Code session — newly added MCP servers only expose their
   tools after a restart, `claude mcp list` showing `✔ Connected` isn't enough.

## Known gotchas

- **`--with 'mcp<2'` is required.** `mcp-google-sheets` hasn't been updated for
  the MCP SDK's 2.x rename (`FastMCP` → `MCPServer`); without the pin, `uvx
  mcp-google-sheets@latest` installs and then crashes on import.
- **Never commit the Service Account JSON key.** Add it to `.gitignore` if this
  project ever becomes a git repo.
- **All spreadsheet writes go through explicit user approval** — hard rule, not
  a suggestion.
