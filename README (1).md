# Maan ki Baat — Setup Guide
### PW Vidyapeeth Events (Aabhar / Aagaz)

Anonymous, region-wise feedback form for ground teams. No name, no phone number, no login — just region + 4 short questions + one tap rating.

3 files:
- `index.html` — the form itself (this is the only file GitHub Pages needs)
- `google-apps-script.gs` — backend code, goes into Google Sheets
- `README.md` — this file

Total setup time: ~10 minutes.

---

## Step 1 — Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) → new blank sheet.
2. Name it something like **"Maan ki Baat — Responses"**.
3. Rename the first tab (bottom-left) to exactly **`Responses`** — the script looks for this name.
4. Don't type any headers yourself — the script creates them automatically on the first submission.

## Step 2 — Add the backend script

1. In the Sheet: **Extensions → Apps Script**.
2. Delete whatever's in `Code.gs`, paste the entire contents of **`google-apps-script.gs`** in.
3. Click **Save** (Ctrl/Cmd+S).

## Step 3 — Deploy it as a Web App

1. Top-right: **Deploy → New deployment**.
2. Click the gear icon next to "Select type" → choose **Web app**.
3. Settings:
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy**.
5. Google will ask you to authorize — click through (it'll warn "Google hasn't verified this app" since it's your own script; click **Advanced → Go to [project name]** → **Allow**).
6. Copy the **Web app URL** it gives you (looks like `https://script.google.com/macros/s/AKfycb.../exec`). You'll need this in Step 4.

> ⚠️ Every time you **edit and re-deploy** the script later, you need to create a **new deployment** (or use "Manage deployments → Edit → New version") — editing the code alone doesn't update the live URL's behaviour.

## Step 4 — Connect the form to your sheet

1. Open `index.html` in any text editor.
2. Near the top of the `<script>` section, find:
   ```js
   var SHEET_ENDPOINT = "PASTE_YOUR_GOOGLE_APPS_SCRIPT_URL_HERE";
   ```
3. Replace the placeholder with the URL you copied in Step 3:
   ```js
   var SHEET_ENDPOINT = "https://script.google.com/macros/s/AKfycb.../exec";
   ```
4. The region list is already set to:
   ```js
   var REGIONS = ["NCR + UK", "UP", "South", "Rajasthan", "PB + J&K", "Maharashtra", "MP + CG", "Bihar + JH", "Delhi + HR", "Gujarat", "AP + TS"];
   ```
   Edit this line only if your region names change later.
5. Save the file.

## Step 5 — Publish on GitHub Pages

1. Create a new GitHub repo (public or private — Pages works on both, private needs GitHub Pro/Team/Enterprise for Pages).
2. Upload `index.html` to the repo root (drag-and-drop on the GitHub web UI works fine — you don't need git installed).
3. Go to **Settings → Pages**.
4. Under "Build and deployment": **Source: Deploy from a branch** → Branch: `main` → folder: `/ (root)` → **Save**.
5. Wait ~1 minute, refresh the page — GitHub will show your live URL, something like:
   ```
   https://<your-username>.github.io/<repo-name>/
   ```
6. Open that URL and submit a test response — confirm a new row lands in your Google Sheet.

## Step 6 — Share it

Share the GitHub Pages link directly on WhatsApp groups (regional groups work well — one message, one link). Nothing to install, opens straight in the phone browser.

---

## Good to know

- **What actually stays anonymous:** no name, phone, email, or login is ever asked or stored. The sheet only gets region, the four answers, and the rating.
- **Technical honesty:** like any web form, the request technically passes through Google's servers and Google *could* see the submitter's IP address at the infrastructure level — the same as any Google Form. The script deliberately does **not** log or store IP, device, or browser info, so nothing identifying ends up in your sheet. Worth knowing this if anyone on the ground asks "sach mein anonymous hai?"
- **Editing questions later:** each question lives in the `steps` array inside `index.html`'s `<script>` — you can reorder, reword, or add one by copying an existing step's block.
- **Response counter on the landing screen** ("N saathiyon ne...") only appears once `SHEET_ENDPOINT` is set and at least 1 response exists — it fails silently otherwise, so nothing breaks if you skip it.
- **Analyzing responses:** once data is flowing into the `Responses` tab, you can pull it straight into Power BI / a pivot table by Region — same pattern as your other dashboards.
