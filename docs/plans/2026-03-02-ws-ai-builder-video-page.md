# WS AI Builder Video Page Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add a standalone video page at `akhil-krishnan.com/ws-ai-builder-application` hosting a local screen recording.

**Architecture:** Convert the local `.mkv` to `.mp4`, commit it to the repo under `videos/`, and create a self-contained `ws-ai-builder-application/index.html` that matches the main site's dark warm theme. GitHub Pages serves directory `index.html` files at clean URLs automatically.

**Tech Stack:** Static HTML/CSS, native `<video>` element, ffmpeg (conversion only)

---

### Task 1: Install ffmpeg

ffmpeg is not installed on this machine. It is needed to convert the `.mkv` source file to a browser-compatible `.mp4`.

**Files:** None (system dependency only)

**Step 1: Install ffmpeg**

```bash
sudo apt install -y ffmpeg
```

Expected output: `ffmpeg is already the newest version` or installation progress ending with no errors.

**Step 2: Verify**

```bash
ffmpeg -version
```

Expected: prints version line starting with `ffmpeg version ...`

---

### Task 2: Convert video to MP4

Source file: `/home/arcanist/Videos/ws-ai-builder-2026-03-02_22.00.17.mkv` (4.6MB, MKV container)
Target: `videos/ws-ai-builder-application.mp4` in the repo

Browsers do not support MKV natively. H.264 in an MP4 container plays everywhere. The `-movflags +faststart` flag moves metadata to the front so playback can start before the full file downloads.

**Files:**
- Create: `videos/ws-ai-builder-application.mp4`

**Step 1: Create the videos directory**

```bash
mkdir -p /home/arcanist/Projects/akhil-krishnan-site/videos
```

**Step 2: Convert**

```bash
ffmpeg -i /home/arcanist/Videos/ws-ai-builder-2026-03-02_22.00.17.mkv \
  -c:v libx264 -preset slow -crf 23 \
  -c:a aac -b:a 128k \
  -movflags +faststart \
  /home/arcanist/Projects/akhil-krishnan-site/videos/ws-ai-builder-application.mp4
```

Flag notes:
- `-crf 23`: quality level (0=lossless, 51=worst). 23 is the default, good for screen recordings.
- `-preset slow`: slower encode = smaller file. Fine for a one-time conversion.
- `-movflags +faststart`: puts moov atom at start of file for progressive playback.

Expected: ffmpeg processes the file and exits with code 0. No error lines.

**Step 3: Verify output**

```bash
ls -lh /home/arcanist/Projects/akhil-krishnan-site/videos/ws-ai-builder-application.mp4
```

Expected: file exists, size is reasonable (likely 2–8MB for a screen recording).

**Step 4: Commit the video**

```bash
cd /home/arcanist/Projects/akhil-krishnan-site
git add videos/ws-ai-builder-application.mp4
git commit -m "$(cat <<'EOF'
feat: add ws-ai-builder screen recording

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
EOF
)"
```

---

### Task 3: Create the video page

The page is entirely self-contained — its own `<style>` block with the full CSS variable set from `index.html`. No nav, no footer. Just back link, title, and video.

**Files:**
- Create: `ws-ai-builder-application/index.html`

**Step 1: Create the directory and file**

```bash
mkdir -p /home/arcanist/Projects/akhil-krishnan-site/ws-ai-builder-application
```

Then create `ws-ai-builder-application/index.html` with this exact content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="robots" content="noindex, nofollow">
    <title>WS AI Builder — Akhil Krishnan</title>
    <link rel="icon" type="image/svg+xml" href="../favicon.svg">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:ital,wght@0,400;0,500;0,600;1,400&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">

    <style>
        :root {
            --bg-primary: #0f0e0d;
            --text-primary: #f0ece4;
            --text-secondary: #a9a49a;
            --text-muted: #5c5955;
            --accent-copper: #e8935a;
            --border: rgba(255, 255, 255, 0.04);
            --font-display: 'Bebas Neue', Impact, sans-serif;
            --font-body: 'DM Sans', -apple-system, sans-serif;
            --font-mono: 'JetBrains Mono', monospace;
            --ease-out-quart: cubic-bezier(0.25, 1, 0.5, 1);
            --grain: url("data:image/svg+xml,%3Csvg viewBox='0 0 512 512' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.7' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
        }

        *, *::before, *::after {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: var(--font-body);
            background-color: var(--bg-primary);
            color: var(--text-primary);
            -webkit-font-smoothing: antialiased;
            min-height: 100vh;
            padding: 3rem 2rem 4rem;
        }

        body::before {
            content: '';
            position: fixed;
            inset: 0;
            background-image: var(--grain);
            opacity: 0.04;
            pointer-events: none;
            z-index: 1000;
        }

        ::selection {
            background: var(--accent-copper);
            color: var(--bg-primary);
        }

        .page {
            max-width: 900px;
            margin: 0 auto;
        }

        .back {
            display: inline-block;
            font-family: var(--font-mono);
            font-size: 0.75rem;
            letter-spacing: 0.08em;
            color: var(--text-muted);
            text-decoration: none;
            margin-bottom: 3rem;
            transition: color 0.2s var(--ease-out-quart);
        }

        .back:hover {
            color: var(--accent-copper);
        }

        h1 {
            font-family: var(--font-display);
            font-size: clamp(2rem, 6vw, 3.5rem);
            letter-spacing: 0.06em;
            line-height: 1;
            color: var(--text-primary);
            margin-bottom: 2rem;
        }

        .video-wrap {
            width: 100%;
            border: 1px solid var(--border);
            border-radius: 4px;
            overflow: hidden;
            background: #000;
        }

        video {
            display: block;
            width: 100%;
            height: auto;
        }
    </style>
</head>
<body>
    <div class="page">
        <a href="https://akhil-krishnan.com" class="back">← akhil-krishnan.com</a>
        <h1>WS AI Builder Application</h1>
        <div class="video-wrap">
            <video controls preload="metadata">
                <source src="../videos/ws-ai-builder-application.mp4" type="video/mp4">
                Your browser does not support the video element.
            </video>
        </div>
    </div>
</body>
</html>
```

**Step 2: Commit the page**

```bash
cd /home/arcanist/Projects/akhil-krishnan-site
git add ws-ai-builder-application/index.html
git commit -m "$(cat <<'EOF'
feat: add ws-ai-builder application video page

Standalone page at /ws-ai-builder-application/ with native video player.
noindex meta tag keeps it out of search results.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
EOF
)"
```

---

### Task 4: Deploy

Deploy to GitHub Pages by merging `main` into `gh-pages` and pushing. This is the standard deploy for this repo (see CLAUDE.md).

**Step 1: Push main and deploy**

```bash
git push origin main && git checkout gh-pages && git merge origin/main && git push origin gh-pages && git checkout main && git branch -d gh-pages
```

Expected: both `main` and `gh-pages` push successfully. GitHub Actions will deploy within ~1 minute.

**Step 2: Verify live**

Open `https://akhil-krishnan.com/ws-ai-builder-application/` in a browser.

Expected:
- Dark background with grain
- `← akhil-krishnan.com` back link at top
- `WS AI BUILDER APPLICATION` title in Bebas Neue
- Video player with controls, video loads and plays

---

## Notes

- The page uses `noindex, nofollow` so it won't appear in search results.
- The `../` relative paths work because the page lives one directory deep — `../favicon.svg` and `../videos/...` both resolve correctly from `/ws-ai-builder-application/`.
- GitHub Pages has a 100MB per-file limit. The source is 4.6MB so well within limits.
