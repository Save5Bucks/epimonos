# Changelog

## 0.8.0

### Renaming a note keeps its links

Rename or move a note and every link pointing at it is rewritten. Previously the link simply broke: `[[Old Name]]` resolves by file name, so changing the name left a dangling link with nothing to say so.

- **Wikilinks, embeds, Markdown links and image paths**, with aliases, `#headings` and `^block` references carried over untouched.
- **Renaming a folder** updates everything that pointed into it, and a note that moved gets its own relative links recomputed.
- **Code is left alone.** A link inside a fenced block or a code span is being shown, not made, so rewriting the example in a note about wikilink syntax would be the wrong fix.
- **A bare `[[Name]]` stays bare only while that name is still unique.** Shortest-path resolution always finds *something*, so renaming onto a name another folder already uses would otherwise produce a link that reads fine and opens the wrong note. When it is no longer unique, the full path is written instead.
- Links written with `%20` keep their encoding; a new name containing spaces is wrapped in `<>`.
- The whole sweep is a single undo, and notes that were not already open are saved rather than left dirty.
- Renames done from VS Code's own Explorer, or by dragging a file, are caught too.
- Off switch: `vaultsUpdateLinksOnRename` in the settings card.

### Accuracy

Known Limitations was still describing 0.6.1: it claimed only flowcharts were drawn, that `subgraph` was unsupported, and that `\widetilde` did not stretch. All three were fixed in 0.7.0.

---

## 0.7.0

### Diagrams

Mermaid diagrams are **drawn** rather than shown as code. Written from scratch — the whole engine adds about 16 KB, against the several megabytes bundling Mermaid would cost.

- **Flowcharts** — `graph` and `flowchart` in all four directions; nine node shapes (rectangle, rounded, stadium, circle, diamond, hexagon, subroutine, cylinder, parallelogram); chained statements; edge labels both piped and inline; solid, thick, dotted and invisible links; arrow, circle, cross and bidirectional endings; cycles and disconnected pieces.
- **Subgraphs** — members boxed with the group's title, including nesting.
- **Sequence diagrams** — participants and actors with aliases, all eight arrow forms, self-messages, notes left of / right of / over, `loop` `alt` `else` `opt` `par` blocks, and activations.
- **State diagrams** — terminals, transitions with labels, `direction`, and described states.
- **Class diagrams** — three-compartment boxes, both member syntaxes, and the UML relationship heads. Laid out with parents above children.
- `<br/>` breaks a node label across lines, and the line count feeds node height.

Anything that cannot be drawn still renders as a code block, so a half-typed diagram looks like code rather than breakage. ER, gantt, pie and journey are not implemented yet and fall back.

### Editor or editor and AI memory

**epímonos: Choose Setup** picks between the Markdown editor on its own and the editor plus AI memory.

Previously AI memory was effectively mandatory: the setup check cannot be dismissed, so anyone who only wanted the editor carried a permanent warning for a feature they never asked for. Choosing editor-only switches memory off and the check stops asking. Reversible at any time, and the memory feature stays visible in the check so it is not lost.

### Maths

- **Wide accents stretch.** `\widehat` and `\overline` use the font's glyph variants; `\widetilde` is drawn as a wave of constant amplitude, because no tilde in the bundled font stretches and scaling one flattens it into something indistinguishable from `\widehat`.

### Fixes

- **Arrowheads were missing in preview and split-preview.** Every diagram emitted markers with the same ids, and `url(#id)` resolves against the whole document, so all of them pointed at whichever pane was hidden — and a marker inside a hidden subtree does not paint.
- **Edges running both ways between the same pair** were drawn as one line with one label hidden beneath the other.
- **Statement keywords are usable as node ids.** A line starting `style`, `class`, `click`, `graph` or `subgraph` was treated as a statement, so a diagram using one as an id rendered empty.
- Other diagram types are refused rather than parsed as flowcharts. Left unguarded, `sequenceDiagram` parsed as a graph — every identifier looks like a node — and drew a confidently wrong picture.

---

## 0.6.1 — first public release

- **Markdown editor** with live block editing, source and preview modes, and a formatting toolbar. Edits splice into the document as minimal diffs, so the file is never reformatted.
- **LaTeX maths, typeset.** Written from scratch, compiled to MathML. Fractions, radicals, scripts, big operators with limits, matrices, `cases`, `align`, stretchy delimiters, accents and the standard symbol set. STIX Two Math bundled, so it renders identically everywhere.
- **A contextual maths toolbar** that appears when the caret enters an expression, generated from the engine's own symbol table.
- **Obsidian vault explorer**, detected automatically. Obsidian itself is not required.
- **Vault Memory MCP server** — eight tools giving assistants long-term memory as ordinary Markdown notes.
- **A guided setup check** that runs on every launch and reports what is actually unfinished.
- `Shift+Enter` starts a new paragraph by splitting the block.
