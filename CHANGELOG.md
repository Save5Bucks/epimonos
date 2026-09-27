# Changelog

## 0.10.1

- **Checkboxes are blocks of their own** in Live mode: a click opens just that item, not the whole list, and a run of them still reads as one list. When a checklist touches other text, leaving the block adds the blank lines Markdown needs, so a line typed straight after a checklist is no longer folded into its last checkbox. Only blank lines are ever added, never inside code, and as one undo step. On by default; the `editorCheckboxBlocks` setting turns it off.
- **Typing in a tall block no longer throws the page around.** In a block taller than the window, clicking could put the caret far from the click, and Enter or Backspace sent the page to the top or bottom. Three causes, each fixed: the click is matched to the nearest occurrence of the text around it, not the first; opening a block keeps it in place with the clicked line under the pointer; and the browser's own caret-follow step, which misplaces the caret when a line break is added or removed, is held back while the editor shows the caret itself. Scrolling by hand is never held.
- Clicking a block right after another was tidied opens the block now holding the clicked line, never a new box at the end of the note.

## 0.10.0

### Diagrams: mindmaps, git graphs, timelines, quadrant, XY, sankey, block, kanban, requirement, C4, packet, radar, architecture, treemap, venn, ishikawa, tree view, use case, cynefin, wardley, swimlane, railroad, event modeling, agentflow and ZenUML

- **Mindmaps** — a tree written by indentation, drawn with the root in the middle and branches balanced left and right, each in its own colour, with lines thinning away from the root. All seven node shapes: circle, rounded, square, hexagon, cloud, bang and the plain default. Icons, classes and markdown strings are accepted and ignored rather than shown as text. Tabs count as indentation.
- **Git graphs** — `commit`, `branch`, `checkout`/`switch`, `merge` and `cherry-pick`, with `id`, `tag`, `type` (`NORMAL`, `REVERSE`, `HIGHLIGHT`) and branch `order`. A lane per branch; forks turn off at once and merges turn in at the merge, so no line cuts diagonally across other commits. Left-to-right, `TB` and `BT`. Generated ids are stable, so the same source always draws the same graph. Anything git itself would refuse — checking out a branch that does not exist, merging a branch into itself, reusing an id — falls back to the code block.
- In mindmaps, lines run straight through a plain node's underline at the same thickness as the line arriving, and thin only after it branches. At first the outgoing lines left from the middle of the text, half a line above the underline, so every branch visibly broke at the node.
- **Timelines** — periods along an axis, each with its events stacked beneath on a dashed stem. `:` continuation lines add events to the period above. Sections band their periods in a shared colour; without sections, each period takes its own.
- **Quadrant charts** — four labelled quadrants numbered as Mermaid numbers them, axis labels at either end or centred, and points with optional `radius`, `color`, `stroke-color` and `stroke-width`. A colour value is only used if it looks like a colour, so a point's style cannot write arbitrary CSS into the page. A point outside 0–1 falls back to the code block, as it is an error in Mermaid.
- **XY charts** — bar and line series over a category list or a numeric range, grouped side by side when there are several bar series, with titles, a legend for named series, per-point line labels, `horizontal`, and a zero line when values go negative. Both `xychart` and the original `xychart-beta` keyword are accepted.
- **Sankey diagrams** — CSV rows of source, target and value, with quoted names and `""` for a quote inside one. Ribbons are drawn to one scale end to end, sinks are pushed to the last column as Mermaid does, and nodes are ordered by where their flow comes from to cut crossings. A cycle has no left-to-right order and falls back. Both `sankey` and `sankey-beta` are accepted.
- **Block diagrams** — the author places everything: `columns`, spans (`a:2`), `space`, nested `block … end` grids with their own columns, sixteen shapes including block arrows in six directions, and edges with or without heads and labels. An edge can end at a group. Where two shapes open alike — `[/a/]` and `[/a\]` — the first closer wins; taking the first shape listed read two blocks as one.
- **Kanban boards** — columns and cards from indentation, `@{ assigned, ticket, priority }` metadata, priority as a coloured card edge, and a column count. A `---config---` block's `ticketBaseUrl` turns tickets into links, accepted only if it is http or https. A leading config block no longer stops any diagram parsing.
- **Requirement diagrams** — all six requirement types and elements, listing their fields; relationships labelled «satisfies», «traces» and the rest, written either way round.
- **C4 diagrams** — Context, Container, Component, Dynamic and Deployment: people, systems, containers and components (with database and queue forms, and external variants) in the C4 model's colours, inside dashed boundaries. Placement follows statement order in rows, as in Mermaid, and `UpdateLayoutConfig` changes the row sizes. A dynamic diagram numbers its steps. Relationship lines run beneath the shapes, and each label moves to the first spot clear of every shape and every other label.
- **Packet diagrams** — rows of 32 bits, fields as ranges, single bits or relative `+N`, split where they wrap, with bit numbers at each end. A label too wide for its cell stands upright in a narrow one or is shortened with an ellipsis. Gaps and overlaps fall back, as Mermaid rejects them.
- **Radar charts** — axes, curves by position or by axis id, and the `max`, `min`, `ticks`, `graticule` (circle or polygon) and `showLegend` options.
- **Architecture diagrams** — groups (nested), services and junctions, placed on a grid by the sides their edges use, with square-cornered edges side to side, arrows either way, and `{group}` edges from a group's boundary. The five built-in icons are drawn; an icon from an unbundled pack shows its first letter rather than a blank.
- **Treemaps** — an indented hierarchy as nested rectangles by value, laid out squarified so cells stay close to square; sections sum what they hold. `valueFormat` (`$`, `,`, `.Nf`, `.N%`) and `showValues` from the config block. Names that do not fit stand upright in tall cells or are shortened, with the full name as a tooltip.
- **Venn diagrams** — up to three sets with labels, unions of two or three, and text nodes in any region. With `:N` sizes the circles are drawn to area: each radius from its set, and each pair's distance solved numerically so the overlap has the union's area. Four or more sets have no honest circle drawing and fall back.
- **Ishikawa (fishbone) diagrams** — the problem at the head, categories as bones alternating above and below the spine, causes as ribs and sub-causes indented beneath them.
- **Tree views** — a directory tree from indentation or from box-drawing characters (standard and heavy), with bold folders, `##` descriptions, `:::highlight`, `icon()` and the `showIcons` config for file and folder icons.
- **Use case diagrams** — actors (with stereotypes), ellipse and rectangle use cases, quoted names with Mermaid's id rule, one-level system boundaries, all seven association operators with labels, include and extend dashed and labelled, generalization with a hollow head, and direction. Syntax outside this subset falls back rather than drawing wrongly.
- **Cynefin** — the five fixed domains with their decision models and practices, wavy boundaries seeded from the source so they never change between renders, the cliff between Clear and Chaotic, item badges, a confusion ellipse that shows three items and "+N more", and curved, labelled transitions.
- **Wardley maps** — `[visibility, evolution]` coordinates as OnlineWardleyMaps writes them, anchors, components with multi-word names, build/buy/outsource/market and inertia decorators, every link and flow style, evolve arrows with ghost positions, notes, pipelines, numbered annotations, accelerators and custom evolution stages with `@` widths. Links stop at circle and label edges; notes and force labels have a backing so a crossing link does not strike through them.
- **Swimlanes** — flowchart syntax under `swimlane-beta`, each top-level subgraph a lane of its own in the order written, drawn as full-length bands that meet, with the lane name in a header; any direction.
- **Railroad diagrams** — all four notations Mermaid accepts, EBNF (W3C and ISO 14977), ABNF, PEG and the constructor form, parsed to one tree and drawn classically: rounded terminals, square rule references, branching choices, optional bypasses above and repetition loops beneath, with ABNF counts noted on the loop. Each rule is a row with start and end marks.
- **Event modeling** — numbered frames in the order written, in both compact and relaxed notation, each in its swimlane by type with a lane per namespace; relations inferred frame to frame, reset frames starting a fresh chain and `->>` naming sources; inline data and data blocks shown beneath; the conventional colours per entity type.
- **Agentflow** — `flow … end` containers that nest, `global` blocks, the task/tool/input/decision/refdoc/action shapes, sequence, reference and failure edges (labelled either way), single- and multi-line `@{ … }` metadata, collapsible containers and connectors, with a tool bound by `connectorRef` drawn pointing at its connector. Prototype-shaped metadata keys are dropped.
- **ZenUML** — translated into sequence diagrams and drawn by the same renderer, with no extra library: sync calls (`A.method()`) nested in `{ }` bodies with activation bars, the caller taken from the enclosing body (an unseen starter at the top level), replies from `return`, assignments and `@return`, async messages, `new` drawing the participant where it is created, aliases, `@Actor`/`@Database` heads and other annotators shown as «captions», `while`/`for`/`forEach`/`loop`, `if`/`else if`/`else`, `opt`, `par` and `try`/`catch`/`finally` fragments, titles, and `//` comments above the next message.
- **Embeds mid-sentence show the note.** `See ![[Recipe]] for more` used to stay a link card; now the note is shown there, as Obsidian shows it, with the paragraph split around it — a box cannot sit inside a paragraph. Embeds in code spans, escaped `\![[`, and image embeds stay inline.
- **Links follow renames made outside VS Code.** Rename or move a note or folder in Obsidian, File Explorer or a terminal while VS Code is open, and links to it are rewritten just as for a rename in the explorer. A file watcher only sees a delete and a create, so they are paired by fingerprint: a rename keeps a note's size and modified time, and a folder's notes keep theirs. A copy, a new note or a git checkout does not match and is left alone; two identical candidates are left alone rather than guessed between. The sweep waits a moment so an app that updates links itself, like Obsidian, finishes first, and renames VS Code is already handling are not processed twice.
- **Venn diagrams with any number of sets.** Four or more are fitted to their overlaps, as venn.js fits them for Mermaid, and each label goes where its region is deepest. Sizes default as Mermaid's do (a set 10, a union of k sets 10/k²), and a pair no `union` names no longer overlaps — Mermaid draws it apart, and so does this now.
- **Gantt `excludes`, `includes`, `weekend`, `tickInterval`, `weekday` and `todayMarker`.** Excluded weekends, weekdays and dates are shaded and push each task's end out by as many days, the bar stopping at its last working day, exactly as Mermaid computes it; an end given as a `YYYY-MM-DD` date is fixed. `tickInterval` places ticks as d3's intervals do (every nth day, week from `weekday`, month…), leaving off a label that would overprint its neighbour. Today is marked with a red line when it falls on the chart, styled by `todayMarker` or hidden with `todayMarker off`.
- **Sequence diagrams: labels set the column spacing.** Neighbouring participants now part far enough for the longest message label between them, and nested activations on one lifeline step right so each bar shows.
- **Nested groups, in every flowchart-like diagram.** A parent's box now encloses its child groups' boxes, not just its own nodes, and a group holding only other groups is kept rather than dropped as empty. An edge naming a subgraph attaches to the group's box instead of creating a stray node with the group's name.
- **Groups that share ranks get separate lanes.** A subgraph or boundary spanning two ranks could drift into the space beside another group's box; now groups whose ranks overlap sit in lanes of their own, and groups on different ranks share one, so a column of subgraphs stays straight.
- Syntax for the newest types was read from the Markdown source of Mermaid's own docs; the rendered pages' summaries left out the examples.
- **Long edges route around nodes, in every graph type.** An edge spanning several ranks used to run straight through whatever sat between its ends, with its label hidden under those nodes. It now bows out past them, its label sits wholly on the open side, and the drawing widens to hold both.
- Ids and tags sit on the side of a commit that no line leaves from — above it left to right, to its left top to bottom. The first attempt put them below, and every fork and merge ran straight through the text; the screenshot showed it at once.

### Fixes

- **Edges running both ways were drawn on top of each other.** The code meant to separate them gave the two edges opposite offsets, but the offset is measured from each edge's own direction and the two point opposite ways, so the flips cancelled and both lines landed in the same place, one label hidden under the other. The test for it compared endpoints in order, and a reversed line has its ends swapped, so it passed while the lines coincided. It now measures the distance between the lines and checks the labels don't overlap.
- **Two-way labels sit on their own line's side**, instead of both at the shared midpoint.
- **A labelled edge no longer covers its own arrowhead.** The gap between rows was fixed at 48px, so in a left-to-right diagram a label wider than that hid the line and its head. The gap now grows to fit the widest label crossing it.

### Tests

- The legacy-settings migration test was stale from the rebrand: it wrote the old keys under `epimonos.*`, where no user ever had them. It now plants them in a real `settings.json` under `vaultMd.*`, exactly as an upgrading user has them, and checks they are migrated, removed from the file, and not migrated again on the next launch. The migration itself was working; only the test was wrong.

---

## 0.9.0

### Embedded notes show their content

`![[Note]]` used to render as a link card. On a line of its own it now shows the note in place.

- **`![[Note]]`** — the whole note, frontmatter left out.
- **`![[Note#Heading]]`** — that heading down to the next of the same or higher level. Obsidian's nested form `![[Note#Method#Timing]]` works, and headings match by slug, so `#set-up` finds "Set up".
- **`![[Note#^block-id]]`** — the paragraph or list item carrying that id.
- Maths, diagrams, tasks and callouts render as they do in the note itself. Embeds inside embeds are followed four levels deep.
- **Nothing recurses.** A note embedding itself, directly or through a loop of others, shows a notice rather than hanging the editor. A missing note, heading or block says which.
- Embedded content cannot act on the note it sits in: ticking a checkbox inside an embed used to be a real risk, because rendered blocks carry line numbers for click-to-edit, and those would have been the *other* note's lines. They are stripped, and embedded checkboxes are read-only.
- Embeds refresh when the other note is saved, and use its unsaved text if it is open.
- **HTML export and Print** fill embeds the same way.
- Trailing ` ^block-id` markers are now hidden when rendering, as in Obsidian.

An embed in the middle of a sentence stays a link card, since a note's content cannot sit inside a paragraph.

### Diagrams: ER, pie, gantt and journey

The last four common Mermaid types are drawn.

- **ER diagrams** — entities as tables with type, name, `PK`/`FK`/`UK` keys and comments; crow's-foot markers for all four cardinalities at both ends; solid identifying and dashed non-identifying relationships.
- **Pie charts** — title, `showData`, percentage labels (dropped on slices too thin to hold one), and a legend.
- **Gantt charts** — sections, `after` dependencies including forward references and several at once, `until`, durations from minutes to years, custom `dateFormat` and `axisFormat`, and the `done`, `active`, `crit` and `milestone` tags. A task that cannot be placed falls back to the code block rather than drawing a chart with a hole in it.
- **User journeys** — sections, a face per step whose height and expression follow the score, the line through them, and a coloured dot per actor.

### Fixes

- **A node feeding only a distant node no longer sits at the top.** Ranking put every source on the first row, so its edge ran straight through the nodes between and hid its own label underneath them. Sources now sit just above their nearest successor. This improves every graph type, not just ER.

### Verification

Every diagram type and every transclusion case was checked by screenshot, rendering the real webview code in headless Edge — the same Chromium as VS Code's webviews — in dark and light themes. `test/visual/shoot.js` and `test/visual/webview.js` do this; the second loads the whole editor with a stand-in for the VS Code host. The rename-updates-links feature from 0.8.0 is now also tested end to end inside a real VS Code instance.

---

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
