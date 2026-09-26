---
title: epímonos math engine — design spec
date: 2026-09-26
status: draft for review
---

# epímonos math engine — design spec

## Context

Math in epímonos is styled, not typeset. The engine finds `$…$` and `$$…$$`, HTML-escapes the contents and wraps them in a span with an italic serif face:

```js
// media/markdown.js:314  (inline)
s.replace(/\$…\$/g, (_, m) => '<span class="math-inline">' + esc(m) + '</span>');
// media/markdown.js:577  (block)
return '<div class="math-block">' + esc(body) + '</div>';
```

So `$\frac{a+b}{c+d}$` renders as the literal text `\frac{a+b}{c+d}` in italics. No fraction bar, no stretched delimiters, no limits above or below operators. This is documented in Known Limitations and is the single most visible gap in a product that sells itself as a real Markdown editor.

**Decision taken:** build the engine in-house rather than bundle KaTeX, target broad LaTeX parity rather than a subset, and hold the marketplace launch until it ships. The licensing objection behind that choice (that bundling KaTeX would compromise proprietary status) was examined and is not correct — KaTeX is MIT and bundles freely into closed commercial software — but the decision stands on its own terms and this spec implements it.

## Goals

- Real typesetting for inline and display math written in LaTeX.
- Broad LaTeX coverage: fractions, radicals, scripts, big operators with limits, matrices and environments, stretchy delimiters, accents, the standard symbol set, and user macros.
- Correct inter-atom spacing — thin at binary operators, wider at relations — because this is what separates typeset math from arranged glyphs.
- Entirely first-party source. No third-party runtime library.
- A malformed expression never damages the surrounding document.

## Non-goals

- TeX document processing: no `\usepackage`, counters, labels, cross-references, or page layout.
- Typesetting prose. Only the contents of math spans.
- Mermaid or other diagramming. Separate decision.
- Editing math visually. The source of truth stays the Markdown text, as everywhere else in this editor.

## Verified constraints

These were measured on this machine, not assumed.

| Fact | Evidence |
| --- | --- |
| VS Code webviews run **Chromium 150** | `ELECTRON_RUN_AS_NODE=1 Code.exe -e 'process.versions'` → `chrome 150.0.7871.250`, `electron 43.6.0` |
| MathML Core is supported | Shipped in Chromium 109; confirmed visually — fraction bars, stretched radicals, matrix delimiters grown to fit |
| Extensible glyphs need a font with an OpenType `MATH` table | Side-by-side demo: same MathML in Cambria Math (correct) vs a handwriting face (delimiters stay one line tall) |
| `font-family` must be set **on the `<math>` element** | Chromium's MathML UA stylesheet declares it there; a declaration on the element beats an inherited value, so styling an ancestor does nothing |
| Cambria Math cannot be redistributed | Ships with Windows/Office; proprietary. Bundle STIX Two Math (OFL) instead |

**The consequence that shapes this design:** Chromium already performs box-and-glue math layout, reads the font's `MATH` table, and assembles extensible glyphs. None of that needs building. The entire project is the front end — LaTeX text in, MathML out.

## Architecture

```
LaTeX source ──► lexer ──► parser ──► macro expansion ──► MathML emitter ──► DOM
                                            │
                                      symbol table
```

Five modules under `media/math/`, each with one responsibility and no VS Code dependency, so all of them test under plain Node like the rest of the suite.

### `lexer.js`
TeX tokenisation: control sequences (`\alpha`, `\frac`), grouping braces, `^` and `_`, primes, digits and letters as separate atoms, whitespace rules (TeX collapses it), and `\text{…}` mode switches. Emits a flat token stream with source offsets, so errors can point at a column.

### `parser.js`
Token stream to AST. Handles precedence and the structural forms: scripts (`x^2_i`, order-independent), `\frac` and friends, `\sqrt` with optional index, `\left…\right` pairs, `\begin{env}…\end{env}` with `&` and `\\` separators. Produces nodes carrying an **atom class** (Ord, Op, Bin, Rel, Open, Close, Punct, Inner) — the property that drives spacing later.

### `macros.js`
Expansion of `\newcommand` and `\def`, plus the built-in macro table (`\tfrac`, `\binom`, `\pmatrix`, `\implies`…) defined as LaTeX source where possible rather than special-cased in the parser. Recursion-depth capped to stop runaway expansion.

### `symbols.js`
The data table: LaTeX name → Unicode codepoint → atom class → default `mathvariant`. Roughly two thousand entries. **This is where "parity" actually lives** — the parser is a few hundred lines, but coverage is a function of this table's completeness. It is also the piece most amenable to being filled in incrementally and verified by test.

### `mathml.js`
AST to MathML. Structural mapping is direct — `\frac` → `<mfrac>`, `\sqrt` → `<msqrt>`/`<mroot>`, scripts → `<msup>`/`<msub>`/`<msubsup>`, limits → `<munderover>`, environments → `<mtable>`. The judgement is in the details: emitting `<mo>` with the right `form`, `stretchy`, `largeop` and `movablelimits` so Chromium applies TeX-equivalent spacing and growth; `<mi>` with the correct `mathvariant` for italic variables vs upright function names.

### Integration

One call site changes. `esc(m)` at `markdown.js:314` and `esc(body)` at `577` become `renderMath(m, { display })`. Detection, block splitting and source-line ranges already work and are untouched.

## Error handling

**Failure is scoped to one expression, never the document.** An unknown control sequence, unbalanced brace, or unclosed environment causes that span to fall back to today's styled-text output, with the reason in a `title` attribute so hovering explains it. A half-typed formula must not blank a note while the user is mid-keystroke, which is the normal case in a live editor.

The renderer is also given a node-count ceiling, so a pathological expression cannot hang the webview.

## Fonts and settings

Bundle **STIX Two Math** (OFL) as `media/fonts/`, woff2 only. Licence text ships at `media/fonts/LICENSE`, and a root `THIRD-PARTY-NOTICES.md` records it — the same compliance shape any bundled font or library needs.

New setting on the existing card: `mathFont`, default `STIX Two Math`, alternatives `Latin Modern Math` (classic LaTeX look) and `Fira Math` (sans). Applied as an inline `font-family` on every emitted `<math>` element, because inheritance does not reach it.

Math colour follows the VS Code theme via `currentColor`, so it themes like the rest of the document.

## Testing

**Golden tests** — LaTeX in, MathML string out, compared exactly. Pure Node, no VS Code host, matching the existing suite's approach. One case per construct, growing with the symbol table.

**Fixture** — `math-samples.md` covers twelve construct families and serves as the visual acceptance check: every section renders as real mathematics when the engine is complete.

**Spacing tests** — assert the emitted `<mo>` attributes for each atom class, since spacing correctness is invisible to a structural diff but is the most noticeable defect in practice.

## Milestones

Everything ships before launch, but the work needs staging to stay buildable:

| # | Deliverable |
| --- | --- |
| **M1** | Lexer, parser, emitter end-to-end for the core: scripts, `\frac`, `\sqrt`, a starter symbol set. `math-samples.md` sections 1–4 render correctly. |
| **M2** | Symbol table breadth, atom classes and spacing. Sections 8 and 10 correct. |
| **M3** | Environments, `\left…\right`, macros. Sections 5–7 correct. |
| **M4** | Font bundling, `mathFont` setting, accents, `\text`, error-fallback polish, Known Limitations updated. |

## Risks

**Symbol table drudgery is the bulk of the effort** and is easy to underestimate — it is data entry with a correctness requirement, not interesting work. It is also the difference between "handles my notes" and "parity".

**Chromium's MathML is good, not TeX.** Fine detail — italic correction around large operators, optical delimiter sizing — may differ from LaTeX output. Acceptable for a notes editor; worth knowing before comparing screenshots against a LaTeX PDF.

**MathML rendering varies slightly between browsers.** It is not a Chromium dependency — Firefox, Safari and Chromium 109+ all render it — but exported HTML will not be pixel-identical across them. The `<annotation>` fallback means nothing is lost regardless; see Resolved §1.

## Resolved

### 1. Exported HTML must render outside Chromium — and mostly already does

The risk noted earlier overstated this. MathML is not Chromium-specific: Firefox has rendered it for two decades, Safari/WebKit supports it, and Chromium joined at 109. Every current browser handles the output.

The fallback is still adopted, because it costs almost nothing and is what every other tool emits: each expression is wrapped as

```xml
<math><semantics>
  …presentation MathML…
  <annotation encoding="application/x-tex">\frac{a+b}{c+d}</annotation>
</semantics></math>
```

The original LaTeX therefore travels inside the document. A browser too old for MathML shows the source instead of nothing, and anything extracting the math gets usable LaTeX back rather than a pile of markup. Applies to the editor, `Export as HTML` and Print alike. No meaningful change to M4's scope.

### 2. Bundle STIX Two Math; no fully unrestricted math font exists

The requirement was a font that is genuinely free with no licence restrictions. **No OpenType math font is CC0 or public domain** — a `MATH` table represents years of specialist work and none has been dedicated to the public domain. OFL is the freest available, and its obligations do not reach this product:

| OFL requires | Impact here |
| --- | --- |
| Ship the licence text alongside the font | One file: `media/fonts/LICENSE` |
| The font may not be sold on its own | Not applicable — the product is an editor |
| Modified fonts stay OFL and drop the Reserved Font Name | Only applies if the font files are edited. They will not be. |
| Anything about the embedding software's licence | **Nothing. epímonos stays proprietary.** |

**Decision:** bundle **STIX Two Math** (OFL) so every user has a working math font with no download and no dependency on what happens to be installed. Setup does not need to fetch or install anything.

`mathFont` defaults to the bundled face. `Latin Modern Math` (GUST Font License — likewise permissive) and `Fira Math` (OFL) are offered as alternatives that resolve from the system if present, and fall back to the bundled font if not. Only one face ships, keeping the package near 1 MB rather than 3 MB.

Compliance is three files: `media/fonts/LICENSE`, a root `THIRD-PARTY-NOTICES.md` recording font name, version, copyright and licence, and one line in `LICENSE` pointing at it.

## Related

- `math-samples.md` — acceptance fixture and current baseline
- `math-font-demo.html` — the probe establishing the constraints above
