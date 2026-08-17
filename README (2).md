# Maan ki Baat — Setup Guide
### PW Vidyapeeth Events (Aabhar / Aagaz)

Anonymous, region-wise feedback form for ground teams. No name, no phone number, no login — just region + 4 short questions + one tap rating.

This version stores responses using a **Google Form as the backend** — no Apps Script, no deployments, no versioning. For ~300 responses this is simpler and more reliable. People never see the actual Google Form — they only ever see your `index.html` page; their answers get quietly saved into the Form's response spreadsheet behind the scenes.

Total setup time: ~5 minutes using the automated script below (recommended), or ~10 minutes doing it by hand.

---

## Fastest path — run `create-form.gs` once (recommended)

This one script creates the Google Form, creates the linked response spreadsheet, and writes out every ID you need — no manual form-building, no hunting for entry IDs.

1. Go to [script.new](https://script.new) — opens a blank Apps Script project.
2. Delete any starter code, paste in the entire contents of **`create-form.gs`**, and save.
3. In the toolbar, next to the **Run** button, pick the function **`createMaanKiBaatForm`** from the dropdown → click **Run**.
4. It'll ask you to authorize — click **Advanced → Go to [project name] → Allow** (standard warning for your own script).
5. Wait a few seconds, then open **Google Drive** → open the new spreadsheet named **"Maan ki Baat — Responses"** → open its **Setup Info** tab.
6. Copy `FORM_ACTION_URL` and the 6 `entry.XXXXXXXXX` IDs straight from that tab into the `CONFIG` section near the top of `index.html`'s `<script>` block:

   ```js
   var FORM_ACTION_URL = "PASTE_YOUR_GOOGLE_FORM_FORMRESPONSE_URL_HERE";

   var ENTRY_IDS = {
     region:    "PASTE_ENTRY_ID_HERE",
     problem:   "PASTE_ENTRY_ID_HERE",
     challenge: "PASTE_ENTRY_ID_HERE",
     improve:   "PASTE_ENTRY_ID_HERE",
     support:   "PASTE_ENTRY_ID_HERE",
     rating:    "PASTE_ENTRY_ID_HERE"
   };
   ```
7. Save `index.html`, then skip straight to **Step 5 — Publish on GitHub Pages** below. Nothing about the page's design or style changes — this only fills in where it sends data.

> The entry IDs this generates are the same numbers Google Forms uses internally — this is a well-tested technique, but it's still worth doing one real test submission (Step 5.5 below) before sharing the link widely, just to confirm each answer lands in the right column.

If you'd rather build the Form yourself by hand instead of running a script, the manual method is below.

---

## Manual path (alternative to the script above)

## Step 1 — Create the Google Form

1. Go to [forms.google.com](https://forms.google.com) → blank form.
2. Title it anything (e.g. "Maan ki Baat — internal, do not share this link").
3. Add exactly **6 questions**, in this order, matching these types:

   | # | Question (can be worded however you like) | Type |
   |---|---|---|
   | 1 | Region | Short answer |
   | 2 | Biggest problem | Paragraph |
   | 3 | Challenge | Paragraph |
   | 4 | Improvement | Paragraph |
   | 5 | Support needed | Paragraph |
   | 6 | Support rating | Short answer |

   Don't mark any as "Required" — your `index.html` already enforces that on its own screens.

4. Go to the **Responses** tab → click the green Sheets icon → **Create a new spreadsheet**. This is where every submission will land, automatically, with no extra setup.

> ⚠️ Don't share this actual Google Form's link with anyone. It's only a backend — the real thing people fill is your `index.html` page.

## Step 2 — Get the entry IDs (one-time, no code reading needed)

Each question inside a Google Form has a hidden ID like `entry.123456789`. Here's the easiest way to find all 6 without touching any code:

1. Open your form → click the **⋮ (three dots)** menu (top right) → **Get pre-filled link**.
2. It opens your form again — fill in an obviously-fake, distinct value in each of the 6 fields, e.g.:
   - Region → `TESTREGION`
   - Biggest problem → `TESTPROBLEM`
   - Challenge → `TESTCHALLENGE`
   - Improvement → `TESTIMPROVE`
   - Support needed → `TESTSUPPORT`
   - Support rating → `TESTRATING`
3. Click **Get link** (bottom) → **Copy link**.
4. Paste that copied link into a notepad. It'll look like:
   ```
   https://docs.google.com/forms/d/e/1FAIpQLS.../viewform?usp=pp_url&entry.111111111=TESTREGION&entry.222222222=TESTPROBLEM&entry.333333333=TESTCHALLENGE&entry.444444444=TESTIMPROVE&entry.555555555=TESTSUPPORT&entry.666666666=TESTRATING
   ```
5. Match each `entry.NNNNNNNNN=` number to the test value right after it — that tells you exactly which entry ID belongs to which question.
6. Delete the test response this created later (Step 5 covers cleanup).

## Step 3 — Get the form's submission URL

1. Still on your form, click **Send** → the link icon → copy the normal form link. It looks like:
   ```
   https://docs.google.com/forms/d/e/1FAIpQLS.../viewform
   ```
2. Change `/viewform` at the end to `/formResponse`:
   ```
   https://docs.google.com/forms/d/e/1FAIpQLS.../formResponse
   ```
   This is the URL your page actually submits to.

## Step 4 — Fill in `index.html`

Open `index.html` in a text editor and find this block near the top of the `<script>` section:

```js
var FORM_ACTION_URL = "PASTE_YOUR_GOOGLE_FORM_FORMRESPONSE_URL_HERE";

var ENTRY_IDS = {
  region:    "PASTE_ENTRY_ID_HERE",
  problem:   "PASTE_ENTRY_ID_HERE",
  challenge: "PASTE_ENTRY_ID_HERE",
  improve:   "PASTE_ENTRY_ID_HERE",
  support:   "PASTE_ENTRY_ID_HERE",
  rating:    "PASTE_ENTRY_ID_HERE"
};
```

Replace the placeholders using what you found in Steps 2–3:

```js
var FORM_ACTION_URL = "https://docs.google.com/forms/d/e/1FAIpQLS.../formResponse";

var ENTRY_IDS = {
  region:    "entry.111111111",
  problem:   "entry.222222222",
  challenge: "entry.333333333",
  improve:   "entry.444444444",
  support:   "entry.555555555",
  rating:    "entry.666666666"
};
```

Save the file.

## Step 5 — Publish on GitHub Pages

1. Create a new GitHub repo.
2. Upload `index.html` to the repo root (drag-and-drop on the GitHub web UI works — no git needed).
3. **Settings → Pages** → Source: **Deploy from a branch** → Branch: `main` → folder: `/ (root)` → **Save**.
4. Wait ~1 minute, refresh — GitHub shows your live URL: `https://<username>.github.io/<repo>/`
5. Open that link, submit a real test response, then check your response spreadsheet — a new row should appear within a few seconds.
6. Go back to your Google Form's Responses tab and **delete the test rows** (the ones from Step 2 and this test) before sharing the real link.

## Step 6 — Share it

Share the **GitHub Pages link** (not the Google Form link) on your regional WhatsApp groups.

---

## Good to know

- **Why this is more reliable than Apps Script for this use case:** Apps Script Web Apps only run your code *after* redirecting to a second URL — and browsers automatically convert a redirected POST into a GET, so a form's POST submission can silently never reach your code. Google Forms doesn't have this problem: it saves the answer on the very first request, before any redirect happens.
- **What actually stays anonymous:** no name, phone, email, or login is ever asked or stored. The response sheet only gets whatever the 6 form questions capture.
- **300 responses is well within limits** — Google Forms and Sheets handle this easily; no rate limits to worry about at this scale.
- **If you ever add a question:** add it as a new question in the Google Form too, grab its entry ID the same way (Step 2), and add a matching line to `ENTRY_IDS` and the `steps` array in `index.html`.
- **Analyzing responses:** once data is flowing into the response spreadsheet, pull it into Power BI / a pivot table by Region — same pattern as your other dashboards.

## About `google-apps-script.gs`

This file is no longer needed if you're using the Google Forms method above — it was the earlier approach, kept only for reference. You can ignore or delete it.
