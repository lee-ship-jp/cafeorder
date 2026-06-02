# ☁️ Cloud sync setup (Google Apps Script + Sheet)

This connects the JMCC Cafe app to **one shared database** so every device that
opens the link sees the same orders in real time. Your data lives in a Google
Sheet **inside your own Google Drive** — no third-party service, no staff logins.

Total time: ~5 minutes, done once.

---

## Step 1 — Create the Google Sheet
1. Go to <https://sheets.new> (this creates a new blank spreadsheet in your Drive).
2. Rename it something like **`JMCC Cafe Orders`**.
   > Tip: you can move this Sheet into your existing Drive folder afterwards —
   > the app connects to the **script**, not the folder, so the location is just
   > for your own organizing.

## Step 2 — Add the backend script
1. In that Sheet: **Extensions ▸ Apps Script**.
2. Delete the sample `function myFunction() {}`.
3. Open [`apps-script/Code.gs`](apps-script/Code.gs) from this repo, copy **all**
   of it, and paste it into the Apps Script editor.
4. Click **Save** (💾).

## Step 3 — Deploy as a Web App
1. Click **Deploy ▸ New deployment**.
2. Click the gear ⚙ next to "Select type" → choose **Web app**.
3. Set:
   - **Description:** `cafe`
   - **Execute as:** **Me**
   - **Who has access:** **Anyone**   ← important, so all devices can reach it
4. Click **Deploy**.
5. Click **Authorize access** → pick your Google account → on the
   "Google hasn't verified this app" screen choose **Advanced ▸ Go to … (unsafe)**
   → **Allow**. (It's your own script, this is normal.)
6. Copy the **Web app URL**. It looks like:
   ```
   https://script.google.com/macros/s/AKfycb................/exec
   ```

## Step 4 — Plug the URL into the app
Open [`index.html`](index.html) and find this line near the top of the `<script>`:

```js
const APPS_SCRIPT_URL = "PASTE_YOUR_WEB_APP_URL_HERE";
```

Replace the placeholder with your `/exec` URL:

```js
const APPS_SCRIPT_URL = "https://script.google.com/macros/s/AKfycb.../exec";
```

Save, commit, and push. Once GitHub Pages rebuilds, the header pill changes from
**📴 Local** to **☁️ Synced**, and all devices share the same orders.

> 💡 You can paste the URL and let me commit & deploy it for you — just send me
> the `/exec` URL. (The web-app URL is safe to commit; access is controlled by
> the deployment settings above, not by hiding the URL.)

---

## How it behaves
- **Live updates:** each device refreshes every ~4 seconds while its screen is on
  (Apps Script can't "push", so the app polls). New orders and completed items
  appear on every device within a few seconds.
- **Offline-safe fallback:** if the URL is missing or unreachable, the app keeps
  working in **local-only** mode on that device and the pill shows ⚠️/📴.

## Updating the script later
If you edit `Code.gs`, redeploy **without changing the URL**:
**Deploy ▸ Manage deployments ▸ ✏ edit ▸ Version: New version ▸ Deploy.**

## Notes on access
"Who has access: Anyone" means anyone who knows the `/exec` URL can read/write
orders — fine for an internal cafe order pad. If you later want it locked down,
options include adding a shared secret token check in `Code.gs`, or switching to
Firebase with auth. Ask and I'll wire it up.
