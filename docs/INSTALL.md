# Installing epímonos

Setup is guided: the first time the extension activates with anything
unfinished, a **Set up epímonos** tab opens and walks you through
what is left. A `⚠ epímonos setup` item stays in the status bar until every
step passes. This page is the manual reference behind that walkthrough — for
scripted installs, other MCP clients, and troubleshooting.

Run **epímonos: Check Setup** at any time to re-run the check.

---

## 1. Install the extension

**From a `.vsix`:**

```bash
code --install-extension vault-md-editor-0.6.0.vsix
```

or in VS Code: Extensions view → `…` menu → **Install from VSIX…**

**From source:**

```bash
git clone https://github.com/Save5Bucks/vault-md-editor.git
cd vault-md-editor
npm ci
npm run package                                    # -> vault-md-editor-<version>.vsix
code --install-extension vault-md-editor-*.vsix
```

Requires VS Code 1.101 or newer and, to build, Node 18+.

---

## 2. Make it the default Markdown editor

The extension registers with `"priority": "option"`, so VS Code does **not**
use it by default — `.md` files keep opening in the plain text editor, and
notes opened from the vault sidebar open in epímonos. That
inconsistency is expected until you complete this step.

Run **epímonos: Make Default Editor for Markdown Files**, or set it yourself:

```jsonc
// settings.json
{
  "workbench.editorAssociations": {
    "*.md": "epimonos.editor",
    "*.markdown": "epimonos.editor"
  }
}
```

**Open as Plain Text** in the editor title bar always returns you to the text
editor for a single file.

---

## 3. Choose a vault for AI memory

Run **epímonos: Set Up AI Memory (MCP)…** and pick a vault and a memory folder
(default `AI Memory`), or configure it directly:

```jsonc
{
  "epimonos.settings": {
    "mcpVault": "C:\Projects\Acme\Acme Vault",
    "mcpFolder": "AI Memory"
  }
}
```

Put those keys in **Workspace** settings instead of User settings to give one
project its own memory.

To turn the memory feature off entirely, set `"mcpEnabled": false` in the same
card — the three memory steps then drop out of the setup check.

---

## 4. Start the server

**epímonos: Show AI Memory (MCP) Status** launches the server and completes an
MCP handshake. VS Code chat (agent mode) picks it up automatically as
*Vault Memory*, on VS Code's own Node runtime.

To run it outside VS Code:

```bash
node out/mcp/server.js --vault "/path/to/vault" \
  [--memory-folder "AI Memory"] [--allow-write-anywhere]
```

---

## 5. Connect an AI client

### Claude Code

**epímonos: Connect AI Memory to Claude Code** runs `claude mcp add-json` for
you. The equivalent by hand:

```bash
claude mcp add-json vault-memory '{
  "type": "stdio",
  "command": "node",
  "args": [
    "<globalStorage>/save5bucks.epimonos/mcp/server.js",
    "--vault", "/path/to/vault",
    "--memory-folder", "AI Memory"
  ]
}' --scope user
```

`<globalStorage>` is:

| OS | Path |
| --- | --- |
| Windows | `%APPDATA%\Code\User\globalStorage` |
| macOS | `~/Library/Application Support/Code/User/globalStorage` |
| Linux | `~/.config/Code/User/globalStorage` |

Point at **globalStorage**, not `extensions/save5bucks.epimonos-<version>/`
— the extension refreshes that copy on activation, so the registration keeps
working across updates, whereas a versioned path breaks on the next release.

Use `--scope project` (writes `.mcp.json`) to scope memory to one repository.

**In a session that is already running, use `/mcp`** to list the configured servers and reconnect `vault-memory`. New sessions pick it up automatically.
Verify with `claude mcp list` — expect `vault-memory … ✔ Connected`.

### Claude Desktop and other clients

**epímonos: Copy AI Memory MCP Config** puts a ready `mcpServers` block on the
clipboard. Paste it into `claude_desktop_config.json`, a project `.mcp.json`,
or your client's MCP settings.

### Agent instructions

**epímonos: Add Agent Instructions to Workspace** writes a managed section into
`AGENTS.md` (read by Codex, Copilot, Cursor and others) and has `CLAUDE.md`
import it. The section sits between `<!-- vault-memory:start/end -->` markers;
everything else in those files is yours.

---

## Troubleshooting

**`.md` files still open as plain text.** Step 2 is not done. Check for
`"*.md"` under `workbench.editorAssociations`. Another extension may have
claimed the association — the last write wins.

**The MCP icon is red.** Hover it for the reason. Usually the vault folder was
moved or deleted; re-run **Set Up AI Memory**.

**Claude Code does not see the tools.** Confirm with `claude mcp list`, then
run `/mcp` and reconnect it, or start a new session. If it was registered at project scope, you must start the
session inside that folder. If the CLI was not on `PATH` when you connected,
the command falls back to copying the config instead.

**The setup tab keeps reopening.** Something genuinely is not done — the check
reads live state and there is no dismiss. Run **epímonos: Check Setup** to see
which step is failing and why.

**Building from source fails on `npm ci`.** Delete `node_modules` and retry;
if `package-lock.json` is out of step with `package.json`, use `npm install`.
