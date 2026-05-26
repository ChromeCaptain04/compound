# Compound Interest — Strength Training Tracker

A single-file (`index.html`) strength training website designed to be **easily maintained by AI assistants**. No build tools, no backend, no hidden complexity — just a plain HTML file, a GitHub repo for data storage, and your phone's notes app.

---

## Philosophy

| Principle | How it's implemented |
|---|---|
| AI-maintainable | Single `index.html`; well-commented JS; no build step |
| Data ownership | Raw `.txt` files and parsed JSON both live in your GitHub repo |
| Offline-friendly input | Record workouts in your phone's notes app, paste later |
| Double progression | Progress graphs show weight (y-axis) and reps (colour-coded) over time |

---

## Getting Started

### 1. Create the GitHub Repository

1. Go to [github.com/new](https://github.com/new) and create a **private** repository named `compound-interest` (or any name you prefer).
2. You don't need to initialise it with any files — the website will create them as needed.

### 2. Generate a Personal Access Token

1. Go to **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)**.
2. Click **Generate new token**.
3. Give it a name (e.g. `compound-interest`), set an expiry, and tick the **`repo`** scope.
4. Copy the token — you won't see it again.

### 3. Host the Website

The simplest approach: enable **GitHub Pages** on your repo.

1. Upload `index.html` to your repo root.
2. Go to **Settings → Pages → Source → Deploy from branch → `main` / `root`**.
3. Your site will be at `https://<your-username>.github.io/<repo-name>/`.

Alternatively, open `index.html` directly in any browser — it works locally too.

### 4. First Login

Open the website. You'll see a login screen:

- **GitHub Username** — your GitHub username.
- **Repository Name** — the repo you created (default: `compound-interest`).
- **Personal Access Token** — the token you just generated.

Click **Enter**. Your credentials are stored in `localStorage` (never sent anywhere except GitHub's API).

---

## Recording Workouts

### Shorthand Format

Type (or paste) your workout anywhere — your phone's notes app is perfect — using this format:

```
YYYY-MM-DD | optional notes
Exercise name: weightxreps, weightxreps, weightxreps
Exercise name: weightxreps, weightxreps
```

**Example:**
```
2026-05-25 | felt strong today
Bench press: 100x3, 100x4
Bicep curls: 8x18, 8x16
Squat: 120x5, 120x5, 125x3
```

- **Date** is required on line 1.
- **Notes** (after the `|`) are optional.
- **Weight × reps**: use `x` or `X` — e.g. `100x5` means 100 kg for 5 reps.
- List as many exercises and sets as you like.

### Recording Bodyweight

Use the same input box with a slightly different format:

```
YYYY-MM-DD | weight
```

**Example:**
```
2026-05-25 | 82.4
```

The website detects a single number after the `|` and treats it as a bodyweight entry, plotting it on the Dashboard's bodyweight graph.

### Committing

1. Open the **Record** tab (or tap the **+** button in the header).
2. Paste your shorthand into the text box.
3. Press **Commit Record**.

The raw text is saved to `data/raw/YYYY-MM-DD-<timestamp>.txt` in your GitHub repo. The website then re-reads all raw files and updates all views automatically.

---

## Tabs

### Dashboard

- **Latest session** — exercises and sets from your most recent workout.
- **Stats** — sessions this week, volume this week (kg), total sessions, total volume (kg).
- **Cycle Tracker** — a prestige-style tube map showing your progress through a 24-session cycle. Three colour-coded phases (blue → green → red) with a bronze prestige hexagon that increments each full cycle.
- **Bodyweight graph** — your bodyweight over time.

### Log

All past workouts in reverse-chronological order. Each is numbered (`#1`, `#2`, …) with the latest marked **LATEST**.

### Progress

One graph per exercise showing your progression over time:

- **X-axis** — date.
- **Y-axis** — weight (kg).
- **Dot colour** — encodes rep range:
  - 🔵 **Blue** — 1–5 reps (heavy/strength work)
  - 🟠 **Amber** — 6–8 reps (moderate/hypertrophy)
  - 🟢 **Green** — 9+ reps (volume)

The data point plotted for each session is the **best set**: the set with the highest weight (ties broken by most reps). This reflects double-progression: you'll see weight climb as your reps hit the top of a range.

### Record

- **Entry box** — paste shorthand workouts or bodyweight entries.
- **Exercise Aliases** — manage synonym mappings (see below).
- **Reprocess Archives** — force a full re-read of all raw files.

### Exercise History (secret)

Double-tap the **Compound Interest** logo in the header. This shows the **last 3 sessions** for every exercise, alphabetically, with sets and dates. Tap **Return** to go back.

---

## Exercise Aliases

The alias system lets you map variant names onto a canonical name. For example, if you sometimes write `bicep curls` and sometimes `DB curls`, you can define `Bicep curl` as the master with both as variants — the website treats them as the same exercise everywhere.

**To add an alias:**

1. Go to **Record → Exercise Aliases → [ EDIT ]**.
2. Enter the master name (e.g. `Bicep curl`) and the variant names (e.g. `bicep curls, DB curls`).
3. Click **Add Alias**, then **Save to GitHub**.

Aliases are stored in `data/aliases.json` in your repo. After saving, all data is reprocessed with the new mappings.

---

## Data Layout in Your Repository

```
compound-interest/
├── index.html              ← The entire website
├── data/
│   ├── raw/                ← Raw shorthand .txt files (one per entry)
│   │   ├── 2026-05-25-123456.txt
│   │   └── ...
│   └── aliases.json        ← Exercise alias dictionary
```

The website parses raw `.txt` files directly in the browser — no GitHub Actions or server processing required. The **Reprocess Archives** button simply re-reads and re-parses everything.

---

## Updating the Website

Because it's a single file with no build step, AI maintenance is straightforward:

1. Open `index.html` from your repo.
2. Paste it into a conversation with an AI assistant along with your change request.
3. Get the updated file back and commit it to replace the old one.

The file is fully commented so an AI can understand every section at a glance.

---

## Security

- Your GitHub token is stored only in your browser's `localStorage` — never sent anywhere except GitHub's own API (`api.github.com`).
- Use a **private** repository to keep your training data private.
- Set a reasonable token expiry (90 days is a sensible default) and rotate it when prompted.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| "Archive inaccessible" on Dashboard | Check your token has `repo` scope; verify username and repo name are correct. |
| Workouts not appearing | Wait 2–3 seconds after committing (GitHub propagation), then reload. |
| Exercise names not merging | Add an alias in Record → Exercise Aliases and click Save. |
| Want to rename an exercise everywhere | Add an alias mapping old name → new master name, then click Reprocess Archives. |
| Token expired | Go to [ RECONFIGURE ] in the footer and enter your new token. |
