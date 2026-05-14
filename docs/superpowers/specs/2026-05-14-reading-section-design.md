# Reading Section — Design Spec

**Date:** 2026-05-14
**Target:** `index.html` on `akhil-krishnan-site`
**Status:** Approved, ready for implementation plan

## Purpose

Add a new section to the personal site that shows what Akhil is currently reading and what he has just finished reading. The section is editorial in voice and minimal in maintenance: title + author only, no covers, no notes, no progress bars.

## Decisions

| Question | Decision |
|---|---|
| Scope | Current read + 3 most recently finished |
| Per-book info | Title + author only |
| Placement | New `<section id="reading">`, inserted between `#writing` and `#contact` |
| Section number | `05` (Writing is `04`, footer is unchanged) |
| Layout direction | Ledger / Index aesthetic (the "C" mockup from brainstorm) |
| Intro | One framing line: *"A small record of what's been holding my attention."* |
| Desktop nav | Add a 6th link between **Writing** and **Contact** |
| Mobile pill nav | Unchanged (stays at About / Work / Contact) |
| Animations | None |
| Data source | Hardcoded HTML inside `index.html` — same approach as every other section |

## Content shape

```
[05]  READING
———
A small record of what's been holding my attention.
———
// NOW
       SEEING LIKE A STATE              James C. Scott
———
// FIN
F.03   THE BOX                          Marc Levinson
F.02   ANTIFRAGILE                      Nassim Taleb
F.01   THE GOAL                         Eliyahu Goldratt
```

(Book titles above are placeholders — actual entries will be filled in during implementation.)

### Index code convention

- NOW entry has no numeric code. The `// NOW` tag is sufficient.
- FIN entries use `F.NN` codes, newest at the highest number, ordered most-recent-first in display.
- When a new book finishes: it becomes `F.04`, the existing `F.03` drops off. The remaining two retain their numbers. This means codes are stable for the life of an entry — they never get renumbered downward.

## Visual treatment

### Structure

Two ledger blocks (`.now-block` and `.fin-block`), each opening with:

1. A mono `// NOW` or `// FIN` tag
2. A hairline rule that extends to the right edge of the container

Each entry inside a block is a 3-column grid: `index code` · `title` · `author`.

### Type & colour

All values pulled from existing `:root` CSS variables. No new tokens.

| Element | Font | Size (desktop) | Colour |
|---|---|---|---|
| `// NOW` tag | `var(--font-mono)` | 10px, 0.18em tracking | `var(--accent-copper)` |
| `// FIN` tag | `var(--font-mono)` | 10px, 0.18em tracking | `var(--text-secondary)` |
| NOW title | `var(--font-display)` | ~30px, 0.06em tracking | `var(--text-primary)` |
| NOW author | `var(--font-body)` italic | 14px | `var(--text-secondary)` |
| FIN index code | `var(--font-mono)` | 10px | `var(--text-muted)` |
| FIN title | `var(--font-display)` | ~17px, 0.06em tracking | `var(--text-primary)` |
| FIN author | `var(--font-body)` italic | 12px | `var(--text-secondary)` |

### Spacing

- Section padding follows the existing `.section` class
- Section header (`05 READING`) matches the existing `.section-header` pattern used by Writing
- Framing line sits just below the section header, before the first ledger block
- NOW block has more vertical padding around its single entry than FIN block rows
- Hairline rules use `var(--border)` for the rule next to each `// NOW` / `// FIN` tag; row separators inside the FIN block use `var(--border-subtle)`

## Mobile behavior (≤ 768px)

The 3-column grid collapses. Each row becomes a stacked 2-line layout:

```
F.03  THE BOX
      Marc Levinson
```

- Line 1: index code + title
- Line 2: author, indented to align with the title's left edge

The NOW entry stays larger than FIN entries. Font sizes shrink modestly. Nothing scrolls horizontally.

The framing line stays the same — it's short enough to read on a phone without re-flow.

## Accessibility

- Section uses `id="reading"` and `<h2 class="section-title">READING</h2>`
- NOW is a single content block (one entry, not a list)
- FIN is a `<ul>` of `<li>` rows for semantic list structure
- Decorative index codes (`F.03`, etc.) wrapped with `aria-hidden="true"` so screen readers don't pronounce them
- The mono tags `// NOW` and `// FIN` are also `aria-hidden="true"`; the `<h2>` carries the section's accessible name
- All text contrast meets the same baseline as the rest of the site (warm off-white on near-black)

## Navigation

Add one item to the desktop `<ul class="nav-links">`, between Writing and Contact:

```html
<li><a href="#reading">Reading</a></li>
```

Mobile pill nav (`.mobile-nav`) is unchanged.

## Sitemap

The existing `sitemap.xml` only lists the root URL — no anchors. Don't add an anchor entry. Bump `<lastmod>` on the root URL to the deploy date so crawlers know the page changed.

## Out of scope

These are deliberately not part of this design:

- Book cover images
- Personal notes or pulled quotes per book
- Progress percentage, dates started/finished, ratings
- "Want to read" / TBR pile
- Linking each entry to Goodreads, Storygraph, or the publisher
- A separate `/reading` page
- Any JS-driven interaction (accordion, modal, expand-on-hover)
- A JSON or YAML data file — entries are HTML in `index.html`

## File touch list

- `index.html` — new section markup, new CSS rules, one new nav link
- `sitemap.xml` — bump root URL's `<lastmod>` only; no anchor entry added
- `CLAUDE.md` — short note documenting the Reading section pattern and how to update entries
- No new files

## Acceptance criteria

1. New section appears between `#writing` and `#contact` with section number `05` and title `READING`.
2. Desktop nav shows a `Reading` link between Writing and Contact. Mobile pill nav is unchanged.
3. NOW block shows one current book; FIN block shows three finished books, ordered most-recent-first.
4. Index codes are `F.03 / F.02 / F.01` for finished books, none for NOW.
5. All colours, fonts, and borders use existing `:root` CSS variables.
6. At ≤ 768px, the grid collapses to stacked rows with author below title; nothing scrolls horizontally.
7. No new animations are introduced. No JS is added for this section.
8. Screen readers announce "Reading" as the section heading, do not announce the `F.NN` codes, and do not announce the `// NOW` / `// FIN` decorative tags.
