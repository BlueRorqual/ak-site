# CLAUDE.md

## Project

Personal site for Akhil Krishnan. Static HTML/CSS/JS, no build step. Hosted on GitHub Pages via `gh-pages` branch.

## Structure

```
index.html          # Single-page site (all CSS and JS inline)
images/             # Portrait and assets
  headshot.jpg      # Hero portrait image (1153×1536px, no sensitive EXIF)
styles.css          # Unused legacy light theme (do not modify)
versions/           # Historical versions (do not modify)
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

- `getBoundingClientRect()` cached on `mouseenter` for card tilt and magnetic nav (was causing reflows on every `mousemove`)
- `IntersectionObserver` calls `unobserve()` after each reveal fires
- `backdrop-filter` blur reduced from 20px to 12px on nav
- Portrait has `fetchpriority="high"` for reliable LCP

## Commit Style

```
type: short description

Optional body explaining why.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

Types: `style`, `fix`, `feat`, `perf`, `chore`, `refactor`
