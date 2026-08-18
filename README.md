# Kiosk POS — Siluetas Art Studio

Mobile-first point-of-sale register for the kiosk business (ALFA1, ALFA2, CHARLIE, DELTA). Staff sign in with a personal code, ring up tattoos/braids/caps/other items, take payment, and every sale logs to a shared Google Sheet. Reports and Order History pull live totals across all kiosks, not just the local session.

## Running it
This is a single self-contained page — no build step, no npm install. Just open `index.html` in a browser, or serve the folder with any static file server.

For local testing:
```
python3 -m http.server 8080
```
then visit `http://localhost:8080/`.

## Deploying (GitHub Pages)
1. Push this folder's contents to the `main` branch of this repo.
2. In the repo's Settings → Pages, set the source to the `main` branch, root folder.
3. The app will be live at the Pages URL GitHub gives you (usually `https://<org>.github.io/SALE_APP/`).

The `.nojekyll` file is included so GitHub Pages serves files as-is (important since some file/folder names in this space can otherwise be mishandled by Jekyll's default processing).

## Configuration
All the tunable bits live at the top of `index.html` in the `CONFIG` object:
- `SHEET_URL` — the deployed Google Apps Script Web App URL this app talks to for products, staff, pricing, and sales logging.
- `adminPin` — PIN to unlock the admin panel (Kiosks tab → Admin options). Default `9999`.
- `idleTimeoutSeconds` — auto-logout delay after inactivity (paused whenever there's an open cart or the payment sheet is showing).
- Colors (`bgColor`, `surfaceColor`, `textColor`, `accentColor`, per-category item background colors) — matches the design's original "Tweaks" panel.

No build tools, frameworks, or `node_modules` are needed to change any of this — it's plain HTML/CSS/JS in one file.

## Backend (Google Apps Script)
The app expects a deployed Apps Script Web App (`/exec` URL) exposing these `GET` actions against a Google Sheet with `PRODUCT` and `KIOSK` tabs plus one sales tab per kiosk (`ALFA1`, `ALFA2`, `CHARLIE`, `DELTA`):
- `?number=<n>` — tattoo lookup by number
- `?staffCode=<code>` — staff login lookup
- `?lookup=cap&name=<name>` — cap price lookup
- `?lookup=all` — bulk product/staff/kiosk data (used to populate the local cache)
- `?lookup=dailyTotals` — today's aggregate totals across all kiosks
- `?lookup=todayOrders` — today's individual orders across all kiosks
- `?action=logSale&...` — appends a sale row to the given kiosk's tab

This backend already exists and is unchanged from the original design; this app just talks to it.
