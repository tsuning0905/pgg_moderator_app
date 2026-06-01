# PGG Moderator App

A small, installable web app (PWA) used by the **session moderator** to run a
public goods game (PGG) field-experiment session from the front of the room. It
walks through the spoken instruction flow and logs the things that happen during a
session.

Part of the field-experiment toolkit:

| App | Folder | Role |
|-----|--------|------|
| Field App | `field-app/` | Per-group contribution recording |
| **Moderator App** | `moderator-app/` | Session-level console (this app) |
| Survey App | `survey-app/` | Post-experiment participant questionnaire |

## What it does

- **Setup & home** — village details, then Start Experiment / History Record /
  Total Village Fund / End Experiment.
- **Instruction flow** — the moderator script, screen by screen, with a per-round
  decision **timer** (1 minute for the first five rounds, then 30 seconds).
- **Issue log** — timestamped events: a generic issue/problem, **chief speech
  start**, and **participant left** (with ID).
- **Chief tracking** — where the chief is (front / walking / at a specific colour
  group) with a quick note and a segment timer.
- **End-of-session** — material checklist, receipt cross-check, and **cash
  reconciliation** (starting float − paid out = remaining).
- **History Record** and CSV export of session data.

All data is kept **on the device** in the browser's `localStorage`, so the app works
fully offline and survives reloads. Export to CSV for the data hand-off.

## Files

```
moderator-app/
├── moderator.html   # the app (all HTML/CSS/JS inline, no build step)
├── manifest.json    # PWA metadata (name, colours, icons)
├── sw.js            # service worker — offline cache
├── icon-192.png     # home-screen icon (192×192)
├── icon-512.png     # home-screen icon / splash (512×512)
└── README.md
```

## Run it locally

A service worker only runs over `http(s)://`, **not** `file://`, so serve the folder:

```bash
cd moderator-app
python3 -m http.server 8000
# then visit http://localhost:8000/moderator.html
```

## Deploy on GitHub Pages

1. Push the repository to GitHub.
2. **Settings → Pages →** deploy from your default branch.
3. The app entry point is:

   ```
   https://<user>.github.io/<repo>/moderator-app/moderator.html
   ```

> **Tip:** the entry file is `moderator.html`, not `index.html`, so the bare folder
> URL won't open it. Either bookmark the full URL above, or rename `moderator.html`
> to `index.html` (and update the same name in `manifest.json` → `start_url` and in
> `sw.js` → `APP_SHELL` / navigation fallback) so the folder URL works directly.
> Once **installed**, the home-screen icon already opens the right page.

## Install on a phone ("package as an app")

1. Open the entry URL in **Chrome (Android)** or **Safari (iOS)**.
2. Android: menu **⋮ → Add to Home screen / Install app**.
   iOS: **Share → Add to Home Screen**.
3. It launches full-screen with its own icon and works offline.

## Updating the app

When you change `moderator.html` (or any cached file), bump the version in `sw.js`:

```js
const CACHE_VERSION = 'pgg-moderator-v1';  // -> 'pgg-moderator-v2'
```

## Notes & limitations

- HTTPS (or `localhost`) is required for the service worker and "Install" prompt.
- Data lives in `localStorage` on this device — export to CSV before clearing site
  data or removing the installed app.
- Designed for portrait phone screens.
