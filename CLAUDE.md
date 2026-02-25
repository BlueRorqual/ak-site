# CLAUDE.md

## Project

Personal site for Akhil Krishnan. Static HTML/CSS/JS, no build step. Hosted on GitHub Pages via `gh-pages` branch.

## Structure

```
index.html          # Single-page site (all CSS and JS inline)
images/             # Portrait and assets
  headshot.jpg      # Hero portrait image
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

Cards and nav use `var(--bg-glass)` + `backdrop-filter: blur()`.

## Portrait

The hero portrait (`images/headshot.jpg`) is a fixed background element with layered CSS masks.

### Current settings

```css
opacity: 0.58;
filter: brightness(1.25) contrast(1.12);
mix-blend-mode: normal;
```

### Masking philosophy

Three masks combined with `mask-composite: intersect`:

1. **Vertical** (`linear-gradient to bottom`): Subtle top fade, solid through center, dissolves into About section at bottom
2. **Horizontal** (`linear-gradient to right`): Soft side edges, solid center column
3. **Radial** (`radial-gradient ellipse`): Centered high on face, keeps core crisp, softens corners

Principles:
- The top stays clean and confident. Eyes and face must remain clear.
- The bottom sinks gently into darkness, transitioning into the About section.
- The center is crisp. Only outer edges dissolve.
- Subtle seam management, not cinematic atmosphere.

### Mobile

Portrait opacity scales to ~50% of desktop value. Mobile hides cursor spotlight and nav links.

## Animations

- Hero entrance: orchestrated staggered reveals with `hero-loaded` class
- Scroll reveals: `.reveal` class with IntersectionObserver
- Cursor spotlight: follows mouse in hero section only, disabled past hero
- Particles: CSS keyframe floating particles in hero
- Card tilt: JS mousemove 3D perspective on project cards
- Marquee: currently commented out

All animations pause when tab is hidden via `animation-paused` class.

## Commit Style

```
type: short description

Optional body explaining why.

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

Types: `style`, `fix`, `feat`, `perf`, `chore`
