# Compound — Strength Training Tracker

A single-file (`index.html`) strength training website designed to be **easily maintained by AI assistants**. No build tools, no backend, no hidden complexity — just a plain HTML file, a GitHub repo for data storage, and your phone's notes app.

---

## Philosophy

| Principle | How it's implemented |
|---|---|
| AI-maintainable | Single `index.html`; well-commented JS; no build step |
| Data ownership | Raw `.txt` files live in your GitHub repo; parsed live in the browser |
| Offline-friendly input | Record workouts in your phone's notes app, paste later |
| Double progression | Progress graphs show weight (y-axis) and reps (colour-coded) over time |

---

## Getting Started

### 1. Create the GitHub Repository

1. Go to [github.com/new](https://github.com/new) and create a repository named `Compound` (or any name you prefer).
2. You don't need to initialise it with any files — the website will create them as needed.

### 2. Generate a Personal Access Token

1. Go to **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)**.
2. Click **Generate new token**.
3. Give it a name (e.g. `compounding`), set an expiry, and tick the **`repo`** scope.
4. Copy the token — you won't see it again.

### 3. Host the Website

Enable **GitHub Pages** on your repo:

1. Upload `index.html` to your repo root.
2. Go to **Settings → Pages → Source → Deploy from branch → `main` / `root`**.
3. Make a small commit (e.g. add a blank line to README) to trigger the first build.
4. Your site will be at `https://<your-username>.github.io/Compound/`.

Alternatively, open `index.html` directly in any browser — it works locally too.

### 4. First Login

Open the website. You'll see a login screen:

- **GitHub Username** — your GitHub username (case-sensitive).
- **Repository Name** — the repo you created (default: `Compound`).
- **Personal Access Token** — the token you just generated.

Click **Enter**. Your credentials are stored in `localStorage` (never sent anywhere except GitHub's API).

---

## Recording Workouts

### Shorthand Format

Type (or paste) your workout anywhere — your phone's notes app is ideal — using this format:

```
YYYY-MM-DD | optional notes
Exercise name: weightxreps, weightxreps, weightxreps
Exercise name: weightxreps, weightxreps
```

**Example:**
```
2026-05-25 | felt strong today
Bench press: 100x3, 100x4
Bicep curl: 8x18, 8x16
Squat: 120x5, 120x5, 125x3
```

- **Date** is required on line 1.
- **Notes** (after the `|`) are optional.
- **Weight × reps**: use `x` or `X` — e.g. `100x5` means 100 kg for 5 reps.

### Recording Bodyweight

Use the same input box with this format:

```
YYYY-MM-DD | weight
```

**Example:** `2026-05-25 | 82.4`

The website detects a single number after the `|` and treats it as a bodyweight entry, plotting it on the Dashboard's bodyweight graph.

### Committing

1. Open the **Record** panel via the **+** button in the header.
2. Paste your shorthand into the text box.
3. Press **Commit Record**.

The raw text is saved to `data/raw/YYYY-MM-DD-<timestamp>.txt` in your GitHub repo. The website re-reads all raw files and updates every tab automatically.

---

## Tabs

### Dashboard

- **Latest session** — exercises and sets from your most recent workout.
- **Stats** — sessions this week, volume this week (kg), total sessions, total volume (kg).
- **Cycle Tracker** — a prestige-style tube map showing your progress through a 24-session cycle. Three phases (blue → green → red). The bronze hexagon at the start increments each time you complete a full cycle of 24.
- **Bodyweight graph** — your bodyweight over time.

### Progress

One graph per exercise showing your progression over time:

- **X-axis** — date.
- **Y-axis** — weight (kg).
- **Dot colour** — encodes the exact rep count. Each rep value (1–20) is assigned a distinct, high-contrast colour. An inline colour key next to the exercise title shows only the rep counts that appear in that exercise's data.

The data point plotted per session is the **best set**: highest weight, ties broken by most reps. This means on a double-progression programme you'll see a staircase pattern — flat stretches while reps climb, then a step up in weight as reps reset.

### Log

All past workouts in reverse-chronological order. Each is numbered (`#1`, `#2`, …) with the latest marked **LATEST**.

### Record (+ button)

Accessible via the **+** in the header:

- **Entry box** — paste shorthand workouts or bodyweight entries.
- **Exercise Aliases** — manage synonym mappings (see below).
- **System** — Reprocess Archives and Reconfigure buttons.

### Exercise History (secret)

Double-tap the **Compound** logo in the header. This shows the **last 3 sessions** for every exercise, alphabetically, with sets and dates. Tap **Return** to go back.

---

## Exercise Aliases

The alias system merges variant exercise names onto a single canonical name. For example, if you sometimes write `Bicep curl` and sometimes `bicep curls`, define `Bicep curl` as the master with `bicep curls` as a variant — the website treats them as the same exercise everywhere.

**To add an alias:**

1. Open the Record panel → **Exercise Aliases → [ EDIT ]**.
2. Enter the master name and variant names (comma-separated).
3. Click **Add Alias**, then **Save to GitHub**.

Aliases are stored in `data/aliases.json`. After saving, all data is re-parsed with the new mappings.

**Typo detection:** When the site loads, it automatically checks whether any two exercise names differ by only 1–2 characters (e.g. `Squat` vs `Squats`). If so, a yellow warning banner appears at the top of the Record panel prompting you to add an alias.

---

## System Buttons (Record panel → System section)

| Button | What it does |
|---|---|
| **Reprocess Archives** | Re-reads all raw `.txt` files and refreshes every tab. Use after adding/changing aliases, or if the data looks wrong. No data is lost — raw files are never modified. |
| **Reconfigure** | Clears saved credentials from `localStorage` and returns to the login screen. Use if you rotate your token or change repos. |

---

## Data Layout in Your Repository

```
Compound/
├── index.html              ← The entire website
├── README.md
└── data/
    ├── aliases.json        ← Exercise alias dictionary
    └── raw/                ← Raw shorthand .txt files (one per entry)
        ├── 2026-05-25-123456.txt
        └── ...
```

Raw files are parsed live in the browser on every load — there is no server-side processing and no compiled JSON cache. The **Reprocess Archives** button simply triggers a fresh re-read of all files.

---

## Technical Notes

- **File listing** uses the GitHub Git Tree API (`/git/trees/{sha}?recursive=1`) rather than the Contents API, so the archive scales beyond 1,000 files without silently dropping entries.
- **File contents** are fetched via `raw.githubusercontent.com` URLs, which are fast and don't count against the authenticated API rate limit. Note: these URLs are technically public (no auth header required) but are unguessable for private repos.
- **`b64e`** (used when writing files) uses `TextEncoder` / `btoa` — handles non-ASCII characters in exercise names and notes correctly.

---

## Updating the Website

Because it's a single file with no build step, AI maintenance is straightforward:

1. Open `index.html` from your repo.
2. Paste it into a conversation with an AI assistant along with your change request.
3. Get the updated file back and commit it to replace the old one.

---

## Security

- Your GitHub token is stored only in your browser's `localStorage` — never sent anywhere except `api.github.com`.
- Use a **private** repository to keep your training data private.
- Set a reasonable token expiry (90 days) and rotate it when prompted. Use **Reconfigure** to update the stored token.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| "Archive inaccessible" on Dashboard | Check token has `repo` scope; verify username and repo name match exactly (case-sensitive). |
| Workouts not appearing after commit | Wait 2–3 seconds (GitHub propagation), then click **Reprocess Archives**. |
| Two exercises showing separately | Add an alias mapping the variant to the master name, save, then reprocess. |
| Yellow warning banner in Record tab | Two exercise names are suspiciously similar — likely a typo. Add an alias to merge them. |
| Token expired | Click **Reconfigure** in the System section and enter your new token. |