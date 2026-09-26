# The maths toolbar

Notes on the maths palette: what it does, why it is built this way, and where to change it.

Lives in `media/editor.js` under the *math toolbar* section. Styles are at the end of `media/editor.css`.

## What it is

A second toolbar row that **replaces** the formatting toolbar while the caret is inside maths, and swaps back when it leaves.

LaTeX is hard to type from memory, and a palette that has to be summoned is a palette nobody uses. Making it contextual costs the user nothing: there is no mode to learn, and the bar is present exactly when it is relevant. `✕` dismisses it for the current expression; it returns next time. **∑ ▾ → Show the maths toolbar** pins it open for anyone who wants it permanently.

## Why it is generated from the engine

`mathGroups()` reads `VaultMath.SYMBOLS` — the same table `media/math.js` compiles with — rather than a hand-maintained list.

This matters more than it first appears. A hand-written palette drifts: a symbol gets added to the engine and never reaches the toolbar, or the toolbar offers something the engine cannot compile and the user gets `\notacommand` rendered as an error. Deriving one from the other makes both impossible.

The atom class each symbol already carries (`ord`, `bin`, `rel`, `op`) supplies the grouping for free, so *Operators* and *Relations* are queries, not lists. Only the sets with no structural marker — Greek, arrows, logic — are named explicitly, and those lists are filtered against `SYMBOLS` so a typo drops the entry rather than producing a dead button.

**To add a symbol to the palette, add it to `SYMBOLS` in `media/math.js`.** Nothing here needs touching.

## Slot navigation

Structure buttons insert a template containing `{}` markers:

```
\frac{}{}      \sqrt[]{}      \sum_{}^{}      \begin{cases} {} & \text{if } {} \\ … \end{cases}
```

On insert the caret lands inside the first `{}`. `Tab` moves to the next one.

The implementation is deliberately **stateless**: `nextMathSlot()` is `value.indexOf('{}', caret)`. Nothing tracks which slots exist or where they have moved, because as soon as a slot is filled it stops being `{}` and is skipped automatically. Editing before a slot shifts its offset harmlessly — the search runs fresh each time.

The alternative, tracking slot offsets after insertion, needs invalidation on every edit and breaks the moment the user clicks somewhere unexpected. This does not.

`Tab` is shared with indentation. The maths branch only runs when `inMathContext()` is true *and* a slot exists ahead of the caret, so list indentation is untouched and `Tab` inside maths with no slots left still inserts a tab.

## Context detection

`inMathContext(ta)` decides whether the caret is inside maths:

- The textarea starts with `$$` → a maths block.
- Otherwise count unescaped `$` between the start of the line and the caret. **Odd means inside.**

Cheap, and correct for the cases that matter. It is a heuristic: `$5 and $10` in prose counts as an open delimiter, so the maths bar may appear over a price list. The cost of that is a toolbar showing when it need not, which is why the bar is non-destructive and dismissible rather than something that changes how keys behave.

Driven by a `selectionchange` listener on the document, so it tracks the caret without hooking every textarea as blocks are created and destroyed.

## Teaching, not replacing

Every cell shows the LaTeX name under the glyph (`≤` over `\leq`). Typing `\leq` is faster than finding it in a grid, and anyone using the editor seriously will end up typing. A palette that hides the command name keeps people dependent on it; one that shows the name makes itself progressively unnecessary. That is the intent.

The filter box searches command names, so half-remembering `leq` is enough — usually faster than scanning the grid.

## Structure

| Piece | Purpose |
| --- | --- |
| `MATH_STRUCTURES` | Buttons with templates — fraction, roots, scripts, operators, text |
| `MATH_ENVIRONMENTS` | Matrix and environment menu entries |
| `ACCENT_TEMPLATES` | Accent menu, with a preview glyph per entry |
| `GREEK_NAMES`, `ARROW_NAMES`, `LOGIC_NAMES` | The groups with no structural marker in `SYMBOLS` |
| `mathGroups()` | Builds the grouped palette from `SYMBOLS` |
| `showSymbolPalette()` | The searchable popup |
| `insertMath(tpl)` | Inserts a template, caret into the first slot, keeps any selection |
| `nextMathSlot()` / `gotoMathSlot()` | `Tab` navigation |
| `inMathContext()` | Whether to show the bar |
| `updateMathBar()` | Swaps the two toolbars |

## Known rough edges

- **Layout is unverified.** It was written without being able to see it render. Button wrapping, popup placement and glyph alignment are the likely problems.
- **`$` in prose** can trigger the bar, as above.
- **Matrix templates use `{}` in cells** so `Tab` can walk them. Valid LaTeX, slightly unusual to read in source.
- **No recently-used section.** Worth adding: in practice people reach for the same dozen symbols, and the quick row is currently a fixed guess at which dozen.
- **The palette is not keyboard-navigable** beyond the filter box. Arrow keys through the grid would be a real improvement.

## Related

- `media/math.js` — the engine, and `SYMBOLS`
- [MATH_ENGINE.md](MATH_ENGINE.md) — design of the LaTeX to MathML compiler
- `samples/math-samples.md` — the fixture every construct is checked against
