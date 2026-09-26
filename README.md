<p align="center">
  <img src="images/icon.png" width="96" alt="epímonos">
</p>

<h1 align="center">epímonos</h1>

<p align="center">
  <strong>A real Markdown editor inside VS Code — and persistent memory for your AI assistants, stored as your own notes.</strong>
</p>

<p align="center">
  <a href="https://marketplace.visualstudio.com/items?itemName=save5bucks.epimonos">Install from the VS Code Marketplace</a>
  ·
  <a href="docs/INSTALL.md">Setup guide</a>
  ·
  <a href="https://github.com/Save5Bucks/epimonos/issues">Report a bug</a>
</p>

---

This is the public home for **epímonos**: documentation, issues and releases. The extension itself is closed source.

> *Επιμονή* — persistence. The path is rarely direct; what matters is that you keep moving.

## What it does

**Write, don't mark up.** A live editing mode where you work in the rendered document and click any block to edit its raw Markdown. Source and preview modes too. Your file is never reformatted — edits are spliced in as minimal diffs, so undo, save, git and side-by-side editing all behave normally.

**LaTeX maths, properly typeset.** Fractions, radicals, big operators with limits, matrices, `cases`, `align`, stretchy delimiters, accents and the standard symbol set — compiled to MathML and laid out by the browser. A contextual maths toolbar appears when the caret enters an expression, with a searchable palette of every symbol the engine knows.

**Your Obsidian vaults in the sidebar.** Detected automatically from Obsidian's own registry, or added by hand. Obsidian itself is not required — a vault is just a folder.

**Persistent AI memory.** A built-in [Model Context Protocol](https://modelcontextprotocol.io) server gives Claude, Copilot and other agents long-term memory stored as ordinary Markdown notes in your vault. You can read, edit, link and graph it like anything else you write. Eight tools: overview, search, read, write, journal, list, links and delete.

No third-party runtime dependencies — the Markdown engine, the maths typesetter and the MCP server are all written from scratch.

## Documentation

| | |
| --- | --- |
| [Setup guide](docs/INSTALL.md) | Installing, the four setup steps, other MCP clients, troubleshooting |
| [Maths engine](docs/MATH_ENGINE.md) | How LaTeX becomes MathML, and why it is built that way |
| [Maths toolbar](docs/MATH_TOOLBAR.md) | The contextual palette and slot navigation |

## Requirements

VS Code **1.101** or newer. Obsidian optional.

## Issues and feedback

Bug reports and feature requests: **[open an issue](https://github.com/Save5Bucks/epimonos/issues)**.

Useful things to include: your OS, VS Code version, the extension version, and — for a maths problem — the LaTeX that misrendered.

## Status

Early. The free feature set above is complete and in use. **Team Memory**, which will share a project's AI memory across a team, is designed but not yet implemented.

## License

Proprietary. Copyright © 2026 Tim Flinn, all rights reserved. See [LICENSE](LICENSE).
