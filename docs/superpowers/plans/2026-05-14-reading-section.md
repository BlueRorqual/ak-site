# Reading Section Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a new `§05 READING` section to `index.html` showing the current book and three most recently finished books, using a "ledger / index" aesthetic that matches the existing dark warm editorial theme.

**Architecture:** Single-page static site. All HTML, CSS, and any JS live inline in `index.html`. No build step, no tests. Every change is verified visually in the browser plus a manual accessibility check. Books are hardcoded markup — no data file, no fetch.

**Tech Stack:** Vanilla HTML + inline CSS using existing `:root` custom properties (Bebas Neue, DM Sans, JetBrains Mono; copper / rust / sage / sand palette).

**Reference spec:** `docs/superpowers/specs/2026-05-14-reading-section-design.md`

---

## File Structure

All work happens in **one repository** with no new directories. Touch list:

| File | Change |
|---|---|
| `index.html` | (a) one `<li>` added to desktop nav, (b) new `<section id="reading">` block, (c) new CSS rules in `<style>` |
| `sitemap.xml` | Bump `<lastmod>` of root URL to today |
| `CLAUDE.md` | Add short note about the Reading section pattern |

No new files are created. No JS is added.

---

## Task 1: Add desktop nav link

**Files:**
- Modify: `index.html` (around line 1106, inside `<ul class="nav-links">`)

- [ ] **Step 1: Insert the nav link**

Find the existing list of nav links:

```html
<ul class="nav-links">
    <li><a href="#about">About</a></li>
    <li><a href="#work">Work</a></li>
    <li><a href="#projects">Projects</a></li>
    <li><a href="#writing">Writing</a></li>
    <li><a href="#contact">Contact</a></li>
</ul>
```

Insert a new `<li>` between Writing and Contact:

```html
<ul class="nav-links">
    <li><a href="#about">About</a></li>
    <li><a href="#work">Work</a></li>
    <li><a href="#projects">Projects</a></li>
    <li><a href="#writing">Writing</a></li>
    <li><a href="#reading">Reading</a></li>
    <li><a href="#contact">Contact</a></li>
</ul>
```

- [ ] **Step 2: Verify in the browser**

Open `index.html` in a browser. Confirm the nav now shows six links and "Reading" is between Writing and Contact. Hover should match the existing underline/colour transition for the other nav links (no CSS work needed — the link inherits `.nav-links a` styles).

Clicking "Reading" will scroll to the top of the page until the section exists (Task 2). That's expected at this point.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "$(cat <<'EOF'
feat: add Reading link to desktop nav

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 2: Add the Reading section markup

**Files:**
- Modify: `index.html` (insert after line 1338 — the closing `</section>` of `#writing` — and before the `<footer id="contact">` on line 1340)

- [ ] **Step 1: Insert the section block**

Find the closing tag of the Writing section. It's followed immediately by the footer:

```html
            <div class="writing-ongoing">
                <p class="collection-label">Ongoing</p>
                <p class="collection-desc">More work is being written. This section will grow.</p>
            </div>
        </div>
    </section>

    <footer id="contact">
```

Insert the new section between `</section>` and `<footer id="contact">`:

```html
    <section id="reading" class="section reading-section">
        <div class="container">
            <div class="section-header">
                <span class="section-number">05</span>
                <h2 class="section-title">READING</h2>
            </div>
            <p class="section-intro">A small record of what's been holding my attention.</p>

            <div class="reading-block reading-now">
                <div class="reading-block-head">
                    <span class="reading-tag" aria-hidden="true">// NOW</span>
                    <span class="reading-rule" aria-hidden="true"></span>
                </div>
                <div class="reading-entry reading-entry-now">
                    <span class="reading-index" aria-hidden="true"></span>
                    <span class="reading-title">SEEING LIKE A STATE</span>
                    <span class="reading-author">James C. Scott</span>
                </div>
            </div>

            <div class="reading-block reading-fin">
                <div class="reading-block-head">
                    <span class="reading-tag" aria-hidden="true">// FIN</span>
                    <span class="reading-rule" aria-hidden="true"></span>
                </div>
                <ul class="reading-list">
                    <li class="reading-entry">
                        <span class="reading-index" aria-hidden="true">F.03</span>
                        <span class="reading-title">THE BOX</span>
                        <span class="reading-author">Marc Levinson</span>
                    </li>
                    <li class="reading-entry">
                        <span class="reading-index" aria-hidden="true">F.02</span>
                        <span class="reading-title">ANTIFRAGILE</span>
                        <span class="reading-author">Nassim Taleb</span>
                    </li>
                    <li class="reading-entry">
                        <span class="reading-index" aria-hidden="true">F.01</span>
                        <span class="reading-title">THE GOAL</span>
                        <span class="reading-author">Eliyahu Goldratt</span>
                    </li>
                </ul>
            </div>
        </div>
    </section>
```

Notes for the implementer:
- The four titles above (Seeing Like a State / The Box / Antifragile / The Goal) are **placeholders**. They will be replaced with Akhil's actual current and recent reads in Task 6. Do not change them in this task — the spec calls out content swap as a separate, user-driven step.
- The empty `<span class="reading-index">` on the NOW entry is intentional — it reserves the first grid column so the title aligns with the FIN block's titles below.
- `// NOW`, `// FIN`, and the `F.NN` codes are decorative; `aria-hidden="true"` keeps them out of the accessibility tree.

- [ ] **Step 2: Verify the section renders in the right place**

Open `index.html` in a browser. Scroll past Writing. You should see the new section as unstyled HTML — section header showing "05 READING", then "A small record…", then four lines of text. Don't worry that it looks unstyled; CSS comes in Task 3.

Click the "Reading" nav link from Task 1. The page should now scroll to this section. Confirm it does.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "$(cat <<'EOF'
feat: add Reading section markup

Inserts §05 between Writing and the footer. Content is placeholder
and styled in the following commit.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 3: Add desktop CSS for the section

**Files:**
- Modify: `index.html` (insert a new CSS block in `<style>`, immediately after the `.writing-ongoing` rules near line 809, and before the `/* Footer */` comment on line 811)

- [ ] **Step 1: Insert the desktop CSS rules**

Find this stretch in the `<style>` block:

```css
        .writing-ongoing .collection-desc {
            margin-top: 0.75rem;
        }

        /* Footer */
        footer {
```

Insert the Reading section's CSS between the closing `}` of `.writing-ongoing .collection-desc` and the `/* Footer */` comment:

```css
        /* Reading */
        .reading-section .section-intro {
            margin-bottom: 3.5rem;
        }

        .reading-block {
            margin-bottom: 2.5rem;
        }

        .reading-block:last-child {
            margin-bottom: 0;
        }

        .reading-block-head {
            display: flex;
            align-items: center;
            gap: 1rem;
            margin-bottom: 1rem;
        }

        .reading-tag {
            font-family: var(--font-mono);
            font-size: 0.7rem;
            letter-spacing: 0.18em;
            text-transform: uppercase;
            white-space: nowrap;
        }

        .reading-now .reading-tag {
            color: var(--accent-copper);
        }

        .reading-fin .reading-tag {
            color: var(--text-secondary);
        }

        .reading-rule {
            flex: 1;
            height: 1px;
            background: var(--border);
        }

        .reading-list {
            list-style: none;
            padding: 0;
            margin: 0;
        }

        .reading-entry {
            display: grid;
            grid-template-columns: 4rem 1fr auto;
            align-items: baseline;
            column-gap: 1.5rem;
            padding: 0.75rem 0;
            border-bottom: 1px solid var(--border-subtle);
        }

        .reading-entry:last-child {
            border-bottom: none;
        }

        .reading-entry-now {
            padding: 1.25rem 0 1.5rem;
            border-bottom: 1px solid var(--border);
        }

        .reading-index {
            font-family: var(--font-mono);
            font-size: 0.65rem;
            letter-spacing: 0.12em;
            color: var(--text-muted);
        }

        .reading-title {
            font-family: var(--font-display);
            letter-spacing: 0.06em;
            color: var(--text-primary);
        }

        .reading-entry-now .reading-title {
            font-size: 2rem;
        }

        .reading-fin .reading-title {
            font-size: 1.05rem;
        }

        .reading-author {
            font-family: var(--font-body);
            font-style: italic;
            color: var(--text-secondary);
        }

        .reading-entry-now .reading-author {
            font-size: 0.95rem;
        }

        .reading-fin .reading-author {
            font-size: 0.8rem;
        }
```

- [ ] **Step 2: Verify in the browser**

Reload `index.html`. The Reading section should now look like the mockup from the design brainstorm:

- `05 READING` header in the same style as other section numbers and titles
- A short italic-ish framing line under it
- A `// NOW` tag in copper, followed by a hairline rule
- One large title ("SEEING LIKE A STATE") with an italic author to its right
- A `// FIN` tag in muted off-white, followed by a hairline rule
- Three smaller rows: `F.03 THE BOX  Marc Levinson`, etc., with thin separators between them

Specifically check:
- The `// NOW` tag is copper-coloured (`#e8935a`), not muted
- The FIN titles line up vertically with the NOW title (the empty NOW index reserves the column)
- The hairline rules extend to the right edge of the container

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "$(cat <<'EOF'
style: add ledger styles for Reading section

Three-column grid (index / title / author), copper NOW tag, muted FIN
tag, hairline rules. All values via existing :root variables.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 4: Add mobile CSS overrides

**Files:**
- Modify: `index.html` (inside the existing `@media (max-width: 768px) { ... }` block, which starts around line 887; insert the new rules near the end of that block, just before its closing `}`)

- [ ] **Step 1: Locate the mobile media query**

Scroll to the responsive section. You'll see:

```css
        /* Responsive */
        @media (max-width: 768px) {
            nav {
                top: 1rem;
                padding: 0.65rem 1.25rem;
            }
            ...
```

The block contains rules for nav, hero, sections, etc. Find the closing `}` of this `@media` block. Insert the Reading mobile rules immediately before that closing brace.

- [ ] **Step 2: Insert the mobile CSS**

```css
            /* Reading — mobile */
            .reading-entry {
                grid-template-columns: 3rem 1fr;
                grid-template-rows: auto auto;
                column-gap: 1rem;
                row-gap: 0.15rem;
                padding: 0.65rem 0;
            }

            .reading-entry-now {
                padding: 1rem 0 1.25rem;
            }

            .reading-author {
                grid-column: 2;
            }

            .reading-entry-now .reading-title {
                font-size: 1.6rem;
            }

            .reading-fin .reading-title {
                font-size: 0.95rem;
            }
```

The 3-column grid (`4rem 1fr auto`) collapses to a 2-column / 2-row grid: index code stays on column 1 of row 1, title goes on column 2 of row 1, author drops to column 2 of row 2 (aligned under the title). The empty NOW index keeps the layout consistent.

- [ ] **Step 3: Verify in the browser**

Reload the page. Resize the browser window down to ~360px wide (or use DevTools mobile emulation).

Check:
- Nothing scrolls horizontally
- For each FIN entry: index code + title sit on one line, author wraps to a second line indented under the title
- The NOW entry shows its title large, with the author italic on the line below
- The framing line ("A small record…") still reads cleanly
- The desktop nav links are still hidden (existing `.nav-links { display: none; }` rule)
- The mobile pill nav still shows only About / Work / Contact

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "$(cat <<'EOF'
style: collapse Reading ledger to 2-line rows on mobile

Author drops below title, aligned to the title's column. No horizontal
scroll.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 5: Accessibility check

**Files:** (none modified — verification only)

- [ ] **Step 1: Walk the section with a screen reader or VoiceOver**

On macOS: enable VoiceOver (`Cmd + F5`) and arrow through the page. On Linux: `orca`. Otherwise, use the browser's accessibility tree (Chrome DevTools → "Accessibility" pane → Inspect each element).

Expected behaviour:
- The section's heading is announced as **"Reading, heading level 2"** (or equivalent)
- The intro paragraph reads as its text content
- The `// NOW` and `// FIN` tags are **not** announced (they're `aria-hidden="true"`)
- The `F.03` / `F.02` / `F.01` codes are **not** announced
- The NOW entry reads as **"Seeing Like a State, James C. Scott"** (or your actual book — see Task 6) with no extraneous tokens
- Each FIN `<li>` reads as **"The Box, Marc Levinson"** etc., and the list is announced as a list of three items

If anything in the decorative tokens leaks through, double-check that the `aria-hidden="true"` attributes survived from Task 2.

- [ ] **Step 2: Keyboard navigation**

Tab through the page from the top. There are no interactive elements inside the Reading section, so focus should jump from the last nav link (or from whatever interactive element precedes the section in source order) to the next interactive element (the email link in the footer). The Reading section text should be reachable by tab only if a user has "tab to all elements" enabled.

This is intentional — entries are not links.

- [ ] **Step 3: No commit needed**

This task is a verification gate. If something fails, jump back to Task 2 and fix the markup, then come back.

---

## Task 6: Replace placeholder books with real entries

**Files:**
- Modify: `index.html` (the four `<span class="reading-title">…</span>` and `<span class="reading-author">…</span>` pairs inside the Reading section)

This step is a **user-driven content swap**. The implementer (or Akhil) supplies the actual current book and three most recently finished books. The structure is fixed; only title and author text changes.

- [ ] **Step 1: Confirm the four entries with the user**

Before making any edits, ask the user to confirm:

1. Current read — what's in your hands today?
2. Most recently finished (becomes `F.03`)
3. Second most recently finished (becomes `F.02`)
4. Third most recently finished (becomes `F.01`)

Do not invent titles. If the user is not available, leave placeholders and surface this task as outstanding.

- [ ] **Step 2: Edit the four title/author pairs**

For the NOW entry, replace:

```html
<span class="reading-title">SEEING LIKE A STATE</span>
<span class="reading-author">James C. Scott</span>
```

with the user's current book (title in uppercase, author in title case).

For each of the three FIN entries, replace the title and author similarly. Keep the index codes as `F.03`, `F.02`, `F.01` in display order (most recent at top).

Title text should be in **uppercase** (it's set in Bebas Neue, which is uppercase-feeling; explicit uppercase keeps it consistent with the rest of the site's display headings). Author text is **title case** in DM Sans italic.

- [ ] **Step 3: Verify visually**

Reload the page. Check that long titles don't break the layout — if a title wraps onto two lines, that's fine on the NOW entry but should be uncommon on FIN entries (the FIN row size is tuned for short titles). If a FIN title is too long, abbreviation (e.g., "Stillness Is the Key" → "Stillness") is acceptable.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "$(cat <<'EOF'
content: populate Reading section with real entries

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 7: Update sitemap and CLAUDE.md

**Files:**
- Modify: `sitemap.xml`
- Modify: `CLAUDE.md`

- [ ] **Step 1: Bump sitemap lastmod**

Open `sitemap.xml`. It contains a single `<url>` entry. Change the `<lastmod>` date to today.

Before:

```xml
<url>
    <loc>https://akhil-krishnan.com/</loc>
    <lastmod>2026-02-26</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
</url>
```

After (replace `YYYY-MM-DD` with the deploy date in ISO format, e.g. `2026-05-14`):

```xml
<url>
    <loc>https://akhil-krishnan.com/</loc>
    <lastmod>YYYY-MM-DD</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
</url>
```

Do not add a new `<url>` entry — the design spec calls this out explicitly. The site is single-page and anchors are not separately indexed.

- [ ] **Step 2: Add a Reading section note to CLAUDE.md**

Open `CLAUDE.md`. Just before the existing `## Mobile Nav` heading, insert this new section:

```markdown
## Reading Section (§05)

Ledger-style section showing the current book and three most recently finished. Located between `#writing` and `#contact`.

### Structure

- `.reading-block.reading-now` — one entry with empty index, copper `// NOW` tag
- `.reading-block.reading-fin` — `<ul>` of three entries with `F.03 / F.02 / F.01` codes (newest at top)

### Updating entries

Edit the `<span class="reading-title">` and `<span class="reading-author">` text in `index.html`. To add a newly-finished book:

1. The current NOW entry becomes the new top of FIN with code `F.04`
2. The existing `F.01` (oldest of three) drops off
3. The two retained entries keep their codes (`F.03` stays `F.03`, `F.02` stays `F.02`)
4. NOW is updated to the next current book

Codes are sticky for the life of an entry — they never get renumbered downward.

### What this section is not

Hardcoded HTML, no JSON, no API. No covers, notes, or progress bars. Decorative tokens (`// NOW`, `// FIN`, `F.NN`) are `aria-hidden="true"`.
```

- [ ] **Step 3: Verify**

Open `CLAUDE.md` in a renderer (or just re-read in plain text) and confirm the section reads cleanly and sits between the previous section and `## Mobile Nav`.

- [ ] **Step 4: Commit**

```bash
git add sitemap.xml CLAUDE.md
git commit -m "$(cat <<'EOF'
docs: document Reading section and bump sitemap lastmod

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

---

## Task 8: Final full-page verification

**Files:** (none modified — verification only)

- [ ] **Step 1: Desktop pass**

Open `index.html` at full width (≥ 1200px).

Confirm:
- All six nav links visible and ordered: About / Work / Projects / Writing / Reading / Contact
- Each nav anchor scrolls to its section
- Reading section sits between Writing and the footer
- Section number `05` shows in the copper-bordered pill
- NOW entry: large title, italic author, copper tag, hairline rule
- FIN entries: three smaller rows with stable index codes, separators between them
- No layout shift, no overlapping elements, no console errors

- [ ] **Step 2: Mobile pass**

Resize to 360px wide (or DevTools mobile emulation).

Confirm:
- Mobile pill nav still shows three links (About / Work / Contact) — Reading is not in the pill nav
- Reading section header and intro line read cleanly
- Each entry: index + title on line 1, author on line 2 (indented to title column)
- Nothing scrolls horizontally
- Tap targets feel fine — text doesn't crowd against viewport edges

- [ ] **Step 3: Tablet pass (≈ 900px)**

Confirm the desktop layout is in effect and nothing breaks at the middle width.

- [ ] **Step 4: No commit needed**

This task is a verification gate. If you spot issues, jump back to the relevant earlier task, fix, then re-run this pass.

---

## Task 9: Deploy

**Files:** (none modified — git operations only)

Deploy follows the project's standard flow documented in `CLAUDE.md`.

- [ ] **Step 1: Push main**

```bash
git push origin main
```

- [ ] **Step 2: Merge to gh-pages and push**

```bash
git checkout gh-pages
git merge origin/main
git push origin gh-pages
git checkout main
git branch -d gh-pages
```

- [ ] **Step 3: Smoke-test the live site**

Open `https://akhil-krishnan.com/` after GitHub Pages rebuilds (typically 1–2 minutes). Confirm:
- Reading section is visible at the live URL
- Nav anchor works
- Mobile view (open on a phone or use mobile emulation) renders correctly

If anything is wrong on the live site that was fine locally, suspect a caching issue first — hard refresh (`Cmd/Ctrl + Shift + R`).

- [ ] **Step 4: Done**

No commit needed. The deploy is complete.

---

## Self-review notes (post-write)

After finishing the plan I scanned for:

- **Spec coverage:** Every acceptance criterion in the spec is covered by a task. (1) section structure → Task 2 + 3; (2) nav link → Task 1; (3) NOW + 3 FIN → Task 2; (4) `F.03 / F.02 / F.01` codes → Task 2 markup; (5) `:root` variables only → Task 3 CSS; (6) mobile collapse → Task 4; (7) no new animations / no JS → enforced by absence; (8) screen reader behaviour → Task 5.
- **Placeholders:** Book titles are flagged as placeholders in Task 2 and swapped in Task 6 — this is intentional, not a TBD plan smell. No other placeholders exist.
- **Type / class consistency:** Class names used across Tasks 2, 3, and 4 (`reading-section`, `reading-block`, `reading-now`, `reading-fin`, `reading-block-head`, `reading-tag`, `reading-rule`, `reading-list`, `reading-entry`, `reading-entry-now`, `reading-index`, `reading-title`, `reading-author`) match exactly. Grid template values in Task 3 and Task 4 are consistent (`4rem 1fr auto` → `3rem 1fr` collapse).
