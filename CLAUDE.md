# CLAUDE.md — Digital Signage

## Project Overview

A zero-dependency, static-HTML kiosk application that loops a Google Slides presentation fullscreen on any browser. Designed for Amazon Firestick / Silk browser deployments across multiple physical locations. Hosted free on GitHub Pages — no server, no build step, no subscriptions.

This project is intentionally minimal. **Do not introduce build tools, frameworks, npm packages, TypeScript, or any backend.** The entire application is ~136 lines of HTML with embedded CSS and JavaScript.

---

## Repository Structure

```
digital-signage/
├── index.html          # The entire application — HTML + CSS + JS
├── README.md           # User-facing setup guide (Google Slides → GitHub Pages → Firestick)
├── be/index.html       # Short-URL redirect → location "be"
├── cb/index.html       # Short-URL redirect → location "cb"
├── co/index.html       # Short-URL redirect → location "co"
├── he/index.html       # Short-URL redirect → location "he"
├── hu/index.html       # Short-URL redirect → location "hu"
├── mt/index.html       # Short-URL redirect → location "mt"
└── ro/index.html       # Short-URL redirect → location "ro"
```

No `src/`, `dist/`, `node_modules/`, or config files exist or should be created.

---

## How the Application Works

### URL Parameters (`index.html`)

The app is configured entirely via query string:

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `?id=` | Yes | — | Google Slides Presentation ID (the long string between `/d/` and `/edit` in the Slides URL) |
| `?delay=` | No | `10000` | Milliseconds per slide |

Example full URL:
```
https://shawnbishop.github.io/digital-signage/?id=1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgVE2upms&delay=8000
```

### Google Slides Embed (`index.html:69`)

```js
const src = `https://docs.google.com/presentation/d/${id}/embed?start=true&loop=true&delayms=${delay}`;
document.getElementById('slides').src = src;
```

The presentation must be published to the web in Google Slides (File → Share → Publish to web → Embed) for this to display.

### Toolbar Hiding (`index.html:22`)

Google's Slides embed includes a toolbar at the bottom. The iframe is deliberately oversized — `height: calc(100% + 60px)` — so the toolbar falls below the visible viewport and is never shown.

### Screen Wake Strategy (`index.html:84–109`)

Two independent mechanisms keep the display on:

1. **Wake Lock API** — `navigator.wakeLock.request('screen')`. Works in Firefox for Fire TV and modern browsers. Re-acquired on `visibilitychange`.
2. **Silent looping video** — A 1×1 transparent MP4 encoded inline as a base64 data URI plays on a loop. This signals display activity in Amazon Silk browser, which ignores the Wake Lock API.

Both are needed; neither alone covers all target devices.

### Hourly Slide Refresh (`index.html:113–126`)

```js
setInterval(reloadSlides, 3600000);
```

The iframe reloads every hour so edits made in Google Slides appear on screen without manual intervention. A 1.5s CSS opacity fade prevents a jarring black flash during reload.

### WiFi Recovery (`index.html:129–132`)

```js
window.addEventListener('online', () => { reloadSlides(); });
```

If a Firestick loses WiFi and reconnects, slides reload automatically.

### Error Display (`index.html:50–58`)

If `?id=` is missing from the URL, the iframe is hidden and a styled error message instructs the user to add the presentation ID.

---

## Location Short-URL Redirects

Each subdirectory provides a short, memorable URL per physical display. Each contains only a 6-line `index.html` that redirects to the main app with the location's presentation ID pre-filled.

**Redirect pattern:**
```html
<!DOCTYPE html>
<html>
<head>
<script>location.replace('../?id=PRESENTATION_ID&delay=10000');</script>
</head>
</html>
```

**Current locations:**

| Short URL | Dir | Presentation ID |
|-----------|-----|----------------|
| `.../be/` | `be/` | `1hbFoai6DLKDUSCu7ZVVZXhY_tU6vfOcKxNxNnTfbIQE` |
| `.../cb/` | `cb/` | `1kUdg5slVqNlmXjH5f7nWwO85kWwLtSCABcqd353Bnck` |
| `.../co/` | `co/` | `1ooGsHrEEw3XJ5VC9xujX35u79JFLxjKiRYU8fH0LpMc` |
| `.../he/` | `he/` | `1qDHtbCiQMHsPOnloKs6ddWv_nzfQq2icKws84ZXhDik` |
| `.../hu/` | `hu/` | `1lTE5fFvYq2fdNQATrK-K5ApPse_RKS6U4Ud_ZvEOUQg` |
| `.../mt/` | `mt/` | `1Z-3LxAhy73kfueQfQe5roWwtIvBU1lnYUkPSueZ71JA` |
| `.../ro/` | `ro/` | `1amxIqop61R5kHl14rvcol0WMa6JCgbQRSwLLXwxTGcA` |

### Adding a New Location

1. Create a new directory: `mkdir XX/`
2. Create `XX/index.html` using the redirect pattern above, substituting the Google Slides Presentation ID
3. Commit and push — GitHub Pages serves it immediately after deployment

### Updating a Presentation ID

Edit the `location.replace(...)` call in the relevant `XX/index.html`. The `?id=` value must exactly match the ID from the Google Slides URL.

---

## Development Workflow

### Making Changes

There is no build step. Edit files directly and commit.

```bash
# Edit index.html or a location redirect
git add index.html          # or: git add he/index.html
git commit -m "Description of change"
git push origin main
```

GitHub Pages deploys automatically within ~60 seconds of a push to `main`.

### Testing Changes

There is no test suite. Verification is manual:

1. **In a desktop browser:** Open `index.html` directly (via `file://` or a local HTTP server) with a valid `?id=` parameter. Confirm the presentation loads and fullscreen works.
2. **On a Firestick:** Navigate to the GitHub Pages URL on Silk Browser or Firefox for Fire TV. Verify the presentation loops, the screen stays on, and the toolbar is hidden.
3. **WiFi recovery:** Disconnect WiFi, wait for the iframe to blank, reconnect — slides should reload within seconds.
4. **Hourly refresh:** Advance the system clock or temporarily reduce the `setInterval` delay to test the fade-refresh cycle.

### Local HTTP Server (optional)

Browser security blocks some APIs (Wake Lock, fullscreen) on `file://` URLs. Use any static server for accurate testing:

```bash
python3 -m http.server 8080
# Then open: http://localhost:8080/?id=YOUR_ID
```

---

## Conventions for AI Assistants

**Preserve the zero-dependency philosophy.** This project's reliability comes from having no moving parts. Do not add:
- npm / package.json / node_modules
- TypeScript or any transpilation
- CSS preprocessors (Sass, Less, etc.)
- JavaScript frameworks (React, Vue, etc.)
- Backend services or databases
- CI/CD pipelines (unless the user explicitly requests them)
- Linting or formatting configs (unless the user explicitly requests them)

**Keep it in one HTML file.** All application logic lives in `index.html`. Do not split it into separate JS or CSS files unless the user explicitly asks.

**Location redirects are data, not code.** The `XX/index.html` files are trivially simple by design. Do not add logic to them; the main `index.html` handles everything.

**Presentation IDs are sensitive configuration.** Do not log them, expose them in error messages, or modify them unless explicitly instructed.

**The base64 video in `index.html:107` must not be modified.** It encodes a specific minimal valid MP4 required for the Silk browser wake-lock workaround. Altering it will break screen-wake on Firestick.

**Deployment is GitHub Pages.** Changes take effect ~60 seconds after pushing to `main`. No build or release process is needed.
