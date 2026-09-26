<p align="center">
  <img src="https://raw.githubusercontent.com/Save5Bucks/epimonos/main/images/icon.png" width="96" alt="epímonos">
</p>

<h1 align="center">epímonos</h1>

<p align="center">
  <em>Επιμονή — persistence. The path is rarely direct; what matters is that you keep moving.</em>
</p>

<p align="center">
  <strong>A real Markdown editor inside VS Code — and persistent memory for your AI assistants, stored as your own notes.</strong>
</p>

<p align="center">
  <a href="https://marketplace.visualstudio.com/items?itemName=save5bucks.epimonos">Install from the VS Code Marketplace</a>
  ·
  <a href="https://github.com/Save5Bucks/epimonos/blob/main/docs/INSTALL.md">Setup guide</a>
  ·
  <a href="https://github.com/Save5Bucks/epimonos/issues">Report a bug</a>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/Save5Bucks/epimonos/main/images/menus-and-mcp.gif" alt="The File menu and the AI memory status indicator" width="820">
</p>

<p align="center"><em>A proper menu bar — files, recent notes, export, print, and a live AI memory indicator.</em></p>

---

This is the public home for **epímonos**: documentation, issues and releases. The extension itself is closed source.

## What it does

**Write, don't mark up.** A live editing mode where you work in the rendered document and click any block to edit its raw Markdown. Source and preview modes too. Your file is never reformatted — edits are spliced in as minimal diffs, so undo, save, git and side-by-side editing all behave normally.

**LaTeX maths, properly typeset.** Fractions, radicals, big operators with limits, matrices, `cases`, `align`, stretchy delimiters, accents and the standard symbol set — compiled to MathML and laid out by the browser. A contextual maths toolbar appears when the caret enters an expression, with a searchable palette of every symbol the engine knows.

**Flowcharts, drawn.** A `mermaid` code fence containing a flowchart renders as SVG — nine node shapes, labelled edges, all four directions, themed from your editor colours. Other Mermaid diagram types still render as code blocks; a diagram that cannot be drawn always falls back to the code it has always been. Written from scratch in 8 KB rather than bundling several megabytes.

**Your Obsidian vaults in the sidebar.** Detected automatically from Obsidian's own registry, or added by hand. Obsidian itself is not required — a vault is just a folder.

**Persistent AI memory.** A built-in [Model Context Protocol](https://modelcontextprotocol.io) server gives Claude, Copilot and other agents long-term memory stored as ordinary Markdown notes in your vault. You can read, edit, link and graph it like anything else you write. Eight tools: overview, search, read, write, journal, list, links and delete.

**Built for VS Code.** Chat picks the memory server up automatically in agent mode, and Claude Code is detected whether you run it as the VS Code extension or the standalone CLI. Nothing to configure on your PATH.

No third-party runtime dependencies — the Markdown engine, the maths typesetter and the MCP server are all written from scratch. The only bundled third-party component is the maths font ([STIX Two Math](https://github.com/stipub/stixfonts), OFL).

## Maths, typeset as you write

<p align="center">
  <img src="https://raw.githubusercontent.com/Save5Bucks/epimonos/main/images/live-editing-math.gif" alt="Live editing with typeset mathematics" width="820">
</p>

<p align="center"><em>Click any block to edit its raw Markdown. Maths typesets as you go.</em></p>

Written from scratch and compiled to MathML, so it renders fast and copies cleanly. The original LaTeX travels inside the document, so nothing is lost when you export or print. A malformed formula never damages the page — it falls back to showing its source, which matters when you are mid-keystroke.

## Documentation

| | |
| --- | --- |
| [Setup guide](https://github.com/Save5Bucks/epimonos/blob/main/docs/INSTALL.md) | Installing, the four setup steps, other MCP clients, troubleshooting |
| [Maths engine](https://github.com/Save5Bucks/epimonos/blob/main/docs/MATH_ENGINE.md) | How LaTeX becomes MathML, and why it is built that way |
| [Maths toolbar](https://github.com/Save5Bucks/epimonos/blob/main/docs/MATH_TOOLBAR.md) | The contextual palette and slot navigation |

## Requirements

VS Code **1.101** or newer. Obsidian optional.

## Issues and feedback

Bug reports and feature requests: **[open an issue](https://github.com/Save5Bucks/epimonos/issues)**.

Useful things to include: your OS, VS Code version, the extension version, and — for a maths problem — the LaTeX that misrendered.

## Status

Early. The free feature set above is complete and in use. **Team Memory**, which will share a project's AI memory across a team, is designed but not yet implemented.

## License

Proprietary. Copyright © 2026 Tim Flinn, all rights reserved. See [LICENSE](https://github.com/Save5Bucks/epimonos/blob/main/LICENSE).
