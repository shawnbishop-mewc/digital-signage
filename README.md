# Digital Signage — Free Posterbooking Replacement

Loops a Google Slides presentation fullscreen on any browser (Amazon Firestick, TV, tablet, etc.). Free forever via GitHub Pages.

---

## How It Works

A single HTML page embeds your Google Slides presentation using Google's own embed player. Each screen gets its own URL with the presentation ID and slide timing baked in. No subscriptions. No servers. No app to install.

---

## Step 1 — Get Your Google Slides Presentation ID

Open your Google Slides deck. The URL looks like:

```
https://docs.google.com/presentation/d/1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgVE2upms/edit
```

The long string between `/d/` and `/edit` is your **Presentation ID**. Copy it.

---

## Step 2 — Publish Your Slides to the Web

In Google Slides:
1. File → Share → Publish to web
2. Click the **Embed** tab
3. Set "Auto-advance slides every" to your desired interval (e.g. 10 seconds)
4. Check **Restart the slideshow after the last slide**
5. Click **Publish** → confirm

> This makes your slides accessible publicly. The embed player reads your timing settings from here. Your slides must be published for the display app to show them.

---

## Step 3 — Deploy to GitHub Pages (One Time)

1. Create a free account at [github.com](https://github.com) if you don't have one
2. Click **New repository** → name it `digital-signage` → set to **Public** → Create
3. Upload `index.html` to the repository (drag and drop on the repository page)
4. Go to **Settings** → **Pages** (left sidebar)
5. Under "Source", select **Deploy from a branch** → branch: `main` → folder: `/ (root)` → Save
6. Wait ~60 seconds. Your app is now live at:
   ```
   https://YOUR-GITHUB-USERNAME.github.io/digital-signage/
   ```

---

## Step 4 — Build Each Screen's URL

For each location, create a URL in this format:

```
https://YOUR-GITHUB-USERNAME.github.io/digital-signage/?id=PRESENTATION_ID&delay=10000
```

| Parameter | Description |
|-----------|-------------|
| `id`      | Your Google Slides Presentation ID (from Step 1) |
| `delay`   | Milliseconds per slide (10000 = 10 sec, 8000 = 8 sec) |

**Example URLs for 7 locations:**

| Location       | URL |
|----------------|-----|
| Phenix Suite 1 | `https://shawnbishop.github.io/digital-signage/?id=PASTE_ID_HERE&delay=8000` |
| Phenix Suite 2 | `https://shawnbishop.github.io/digital-signage/?id=PASTE_ID_HERE&delay=8000` |
| Phenix Suite 3 | `https://shawnbishop.github.io/digital-signage/?id=PASTE_ID_HERE&delay=8000` |
| Phenix Suite 4 | `https://shawnbishop.github.io/digital-signage/?id=PASTE_ID_HERE&delay=8000` |
| Phenix Suite 5 | `https://shawnbishop.github.io/digital-signage/?id=PASTE_ID_HERE&delay=8000` |
| Phenix Suite 6 | `https://shawnbishop.github.io/digital-signage/?id=PASTE_ID_HERE&delay=8000` |
| Phenix Suite 7 | `https://shawnbishop.github.io/digital-signage/?id=PASTE_ID_HERE&delay=8000` |

---

## Step 5 — Set Up Each Amazon Firestick

Do this once per Firestick:

### Enable sideloading
1. Settings → My Fire TV → Developer Options
2. Turn **Apps from Unknown Sources** ON

### Install a browser
- **Option A (Free): Silk Browser** — already installed on most Fire TVs. Find it in Apps.
- **Option B (Free): Firefox for Fire TV** — search "Firefox" in the Amazon App Store.
- **Option C ($6.90 one-time): Fully Kiosk Browser** — best kiosk experience. Auto-relaunches if the browser crashes. Install via the Downloader app (see below).

### Navigate to your URL
1. Open the browser
2. Type or paste your screen's URL into the address bar
3. The slides will start looping automatically

### Keep the screen awake
- Install **Stay Alive! Keep Screen Awake** from the Amazon App Store (free)
- Or: Settings → Display → Sleep → Never

### Bookmark the URL
In Silk Browser: tap the star icon to bookmark so you can relaunch easily after a reboot.

---

## Updating Your Content

| What changed | What to do |
|---|---|
| Edited slides (text, images, order) | Nothing — updates appear automatically within ~1 minute |
| Want different slide timing | Update `?delay=` in the URL on the Firestick |
| Switching to a completely different presentation | Update `?id=` in the URL on the Firestick |

---

## Troubleshooting

**Slides not showing / blank screen**
- Make sure the presentation is published to web (Step 2)
- Confirm the `?id=` in the URL matches the presentation ID exactly

**Screen goes to sleep**
- Install Stay Alive! app or set Fire TV sleep to Never

**Browser crashes or shows wrong content after reboot**
- Use Fully Kiosk Browser — it auto-relaunches and locks to your URL

**Need to exit fullscreen**
- Click/tap anywhere on the screen, then press the browser's Back button
