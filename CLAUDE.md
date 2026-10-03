# CLAUDE.md

## Project

Personal site for Akhil Krishnan. Static HTML/CSS/JS, no build step. Hosted on GitHub Pages via `gh-pages` branch.

## Structure

```
index.html          # Single-page site (all CSS and JS inline)
images/             # Portrait and assets
  headshot.jpg      # Hero portrait image (1153×1536px, no sensitive EXIF)
favicon.svg         # AK monogram, copper-on-charcoal
```

## Deployment

1. Commit to `main`
2. Push `main` to origin
3. Checkout `gh-pages`, merge from `origin/main`, push, then delete local `gh-pages`
4. Return to `main`

```bash
git push origin main
git checkout gh-pages && git merge origin/main && git push origin gh-pages && git checkout main && git branch -d gh-pages
```

## Version Tags

| Tag | Description |
|-----|-------------|
| `v4-live-2026-02` | Pre-minimalist version with particles, ray, fixed portrait |
| `v5-minimalist-2026-02` | First minimalist — removed particles/ray, mobile nav, proof lines |

## Design System

### Theme: Dark, warm, editorial

- Background: near-black warmth (`#0f0e0d`)
- Text: warm off-white (`#f0ece4`)
- Accent: copper (`#e8935a`), rust (`#c4694a`), sage (`#8a9b82`), sand (`#e5d5b5`)
- Grain texture overlay at 4% opacity on `body::before`

### Typography

| Role | Font | Usage |
|------|------|-------|
| Display | `Bebas Neue` | Headings, section titles, nav logo |
| Body | `DM Sans` | Paragraphs, descriptions |
| Mono | `JetBrains Mono` | Tags, labels, section numbers, code |

### CSS Variables

All colours, fonts, borders, and easings are defined in `:root`. Always use variables, never hardcode values.

### Borders

Use `var(--border)` for default, `var(--border-subtle)` for minimal, `var(--border-strong)` for hover states. All are low-opacity white.

### Glass effect

Cards and nav use `var(--bg-glass)` + `backdrop-filter: blur(10px)`. Nav uses `blur(12px)`.

## Portrait

The hero portrait (`images/headshot.jpg`) is a `position: relative` flex item at the bottom of the hero section, rendered after the hero text content in the DOM.

### Current settings

```css
position: relative;
opacity: 0.58;
filter: brightness(1.25) contrast(1.12);
mix-blend-mode: normal;
mask-image: radial-gradient(ellipse 58% 66% at 50% 42%, #000 40%, transparent 78%);
fetchpriority: high  /* on the img element */
```

### Masking

Single radial gradient — no `mask-composite`. The 3-layer composite approach (`intersect` / `source-in`) was removed due to cross-browser inconsistency. The single radial keeps the face crisp in the centre and fades gracefully to transparent at all edges.

### Mobile

Portrait opacity scales to `0.29` via `.hero-loaded .hero-portrait` override in mobile media query. Nav links hidden; mobile bottom pill nav shown instead.

## Reading Section (§05)

Ledger-style section showing the current book and three most recently finished. Located between `#writing` and `#contact`.

### Structure

- `.reading-block.reading-now` — one entry with empty index, copper `// NOW` tag
- `.reading-block.reading-fin` — `<ul>` of three entries with fixed `F.03 / F.02 / F.01` codes (newest at top)

### Updating entries

Edit the `<span class="reading-title">` and `<span class="reading-author">` text in `index.html`. To add a newly-finished book:

1. The current NOW entry moves into the `F.03` slot
2. The previous `F.03` and `F.02` entries shift down to `F.02` and `F.01`
3. The old `F.01` drops off
4. NOW is updated to the next current book

Codes are positions, not a running count — the ledger is a snapshot, not a catalogue. They never change; only the books in each slot do.

### What this section is not

Hardcoded HTML, no JSON, no API. No covers, notes, or progress bars. Decorative tokens (`// NOW`, `// FIN`, `F.NN`) are `aria-hidden="true"`.

After any content change, bump `<lastmod>` in `sitemap.xml` to the deploy date.

## Radio Section (§06)

The Arman and Akhil Show (CiTR 101.9 FM). Located between `#reading` and `#contact`. Reuses the Reading ledger classes (`.reading-block`, `.reading-entry`, etc.) rather than duplicating them; radio-only CSS is the `a.reading-title` link state, `.radio-listen`, and a wider mobile index column for date codes.

### Structure

- `.reading-now` block, copper `// LATEST` tag — the newest episode
- `.reading-fin` block, `// SELECTED` tag — three hand-picked guest interviews, newest at top
- `.radio-listen` — Spreaker · CiTR · Apple Podcasts · Spotify links

Index codes are the episode's publish month (`YYYY.MM`). Titles are the topic part of the Spreaker episode title, uppercased; the guest goes in `.reading-author`. Each title links to the episode's Spreaker page.

### Updating entries

- **New episode:** replace the LATEST entry (date, title, guest, link). The old latest drops off — it does not move into SELECTED.
- **SELECTED** changes only by deliberate choice. Pick guest interviews with substance; skip the pointed commentary episodes, since the site's reader is recruiters.
- Episode data (titles, dates, URLs): `curl -s "https://api.spreaker.com/v2/shows/5817649/episodes?limit=10"`

No embedded player — it would pull third-party JS and cookies onto a static page.

## Mobile Nav

Fixed bottom pill navigation for viewports ≤ 768px. Implemented as `<div role="navigation">` (not `<nav>`) to avoid inheriting desktop `nav {}` CSS.

```html
<div class="mobile-nav" role="navigation" aria-label="Section navigation">
    <a href="#about" class="mobile-nav-link">About</a>
    <a href="#work" class="mobile-nav-link">Work</a>
    <a href="#contact" class="mobile-nav-link">Contact</a>
</div>
```

## Animations

Stripped to essentials only. All entrance choreography, particles, ray, cursor spotlight, card tilt, and scroll reveals have been removed.

**What remains:**
- Writing accordion: `grid-template-rows: 0fr → 1fr` CSS transition on click
- Hover states: `color`, `border-color`, `box-shadow` transitions on links/cards (no transforms)
- Smooth anchor scroll: JS `scrollIntoView`
- Hero link entrance delays: `nth-child` stagger, cleared via `setTimeout` after 2s so hover is instant thereafter

**What was removed:**
- Hero entrance stagger (`hero-loaded` class orchestration) — all content visible on load
- Scroll reveals (`.reveal` / IntersectionObserver)
- Cursor spotlight
- Floating particles
- Rotating ray
- Card 3D tilt (mousemove + perspective)
- Magnetic nav links (mousemove translate)
- Scroll indicator pulse

## Performance Notes

- `backdrop-filter` blur: 12px on nav
- Portrait has `fetchpriority="high"` for reliable LCP

## Commit Style

```
type: short description

Optional body explaining why.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

Types: `style`, `fix`, `feat`, `perf`, `chore`, `refactor`
