# Design: WS AI Builder Application Video Page

**Date:** 2026-03-02
**Status:** Approved

## Summary

Add a standalone video page at `akhil-krishnan.com/ws-ai-builder-application` that hosts a local screen recording as a committed MP4 file, styled to match the main site's dark warm theme.

## URL

`/ws-ai-builder-application/` → served from `ws-ai-builder-application/index.html`

GitHub Pages resolves directory `index.html` files automatically, giving a clean URL without `.html`.

## Files

| File | Action |
|------|--------|
| `videos/ws-ai-builder-application.mp4` | New — converted from source `.mkv` via ffmpeg |
| `ws-ai-builder-application/index.html` | New — standalone video page |

## Page Design

**No nav.** Standalone page, not part of the main site's navigation.

**Layout (top to bottom):**
1. Back link — `← akhil-krishnan.com` in JetBrains Mono, copper accent, top-left
2. Title — `WS AI BUILDER APPLICATION` in Bebas Neue display font
3. Video — native `<video controls>` element, `max-width: 900px`, `width: 100%`

**Style:**
- Background: `#0f0e0d` (same as main site)
- Grain texture overlay via `body::before` (same SVG noise pattern)
- CSS variables inlined in a `<style>` block — no external stylesheet
- Same Google Fonts import (Bebas Neue, DM Sans, JetBrains Mono)
- No animations, no transforms, no hover effects beyond back link color

**Video element attributes:** `controls`, `preload="metadata"`, `width="100%"`

## Video Conversion

Source: `~/Videos/ws-ai-builder-2026-03-02_22.00.17.mkv` (4.6MB, MKV container)
Target: `videos/ws-ai-builder-application.mp4` (H.264/AAC, MP4 container)
Tool: `ffmpeg` with `-c:v libx264 -c:a aac -movflags +faststart`

`faststart` moves the MP4 metadata to the front of the file so playback can begin before the full file downloads.

## Out of Scope

- No sitemap update (this is a private/application page, not for indexing)
- No robots meta tag changes
- No link from the main site
