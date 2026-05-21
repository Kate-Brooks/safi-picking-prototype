# Safi — Production Management picking-list prototype

Interactive, browser-runnable prototype of the Shift Leader → Forklift Operator picking-list workflow on the Safi web app's Production Management page.

Built to be shared with colleagues for feedback before going into the Safi codebase.

## Running it locally

```bash
open index.html      # macOS — just opens in the default browser
# or drag index.html onto a browser tab
```

It's a single HTML file. React, ReactDOM, Tailwind, and Babel load from CDN — no install, no build step.

## What's in it

Two roles, one page (Production Management), switched via the **View as Shift Leader / View as Forklift Operator** toggle in the top bar.

**Shift Leader — Production & warehouse tab**

- Active production window summary at the top.
- Default view: read-only overview of the yard (Island → Bay), with capacity chips (`Zone full` / `Zone empty` / `Capacity`, and `Bay full` / `Bay empty` / `Capacity for N load(s)`).
- Filters above the warehouse: Material (single), Search loads, Supplier (single), Zone & bay (multi), Capacity (multi). Material is the most important and sits first.
- **+ Create picking list** primary button opens a focused workflow:
  - Tick individual loads or use **Select all** on a whole bay.
  - Blocked bays are flagged with a `Blocked by …` chip and disabled (only inside the create workflow).
  - Right-hand **Picking list card** aligns with the first zone card. Shows running totals, weighted predicted quality (Transparent PET / Coloured PET / Contaminants), the loads added with clickable `Isla X: Fila Y` links that scroll back to the row, a Print icon (tooltip *Print picking list*), and **Send to picker** / **Cancel** / **Schedule** actions. Card body scrolls internally so the footer is always visible.
  - **Send to picker** fires a bottom-centred success toast with **Undo**, scrolls back to the top, and surfaces a blue "Picking list is active" alert with **Edit list** and a destructive **Clear current list** (confirm dialog with **Clear** button).
- Each load row shows three AI result chips inline (Transparent PET / Coloured PET / Contaminants — green if in spec, red if not). Un-analysed loads show *Not analysed yet*.

**Production & warehouse (sticky) tab** — a duplicate of the warehouse view where bays use sticky section headers instead of accordions, for layout comparison. Includes a *Show all loads (N more)* expander when a bay has more than 3 loads (try Fila H5).

**Forklift Operator — Picker tab**

- Notification banner with the picking list name and progress.
- Only the zones / bays / loads on the picking list are shown.
- Per-load **Process full load** / **Partial…** (modal records N of M bales — the rest remain in the bay and are still tracked).
- **Process all in bay** shortcut.
- Processed loads disappear from the warehouse and appear in **History** (table view with supplier, order ref, source bay, bales / weight processed, spec averages, timestamp).

## Mock data

Modelled on the customer's `LAYOUT MP.xlsx` yard. Isla A has 40 bays (mix of full at 8 loads, partial, empty, blocked). Suppliers, lot refs, and weights are realistic — lot refs encode the island in their prefix. Two bays are deliberately blocked (`A4` by `A2`, `C17` by `C5`) so the workflow constraint is visible.

Spec thresholds used: Transparent PET ≥ 92%, Coloured PET ≤ 6%, Contaminants ≤ 2%. Confirm with the team before treating these as production values.

## Publishing to GitHub Pages

The repo is ready to push as-is. Three steps:

### 1. Create the empty repo on GitHub

Go to **https://github.com/new** and create a repo named `safi-picking-prototype` (or any name). Do **not** initialise it with a README or .gitignore — they'd conflict with the files already in this folder.

### 2. Push from this folder

From inside this directory, paste:

```bash
git init
git add .
git commit -m "Safi picking-list prototype"
git branch -M main
git remote add origin https://github.com/<your-username>/safi-picking-prototype.git
git push -u origin main
```

Replace `<your-username>` with your GitHub username (or the organisation name).

### 3. Enable Pages

On the repo page on github.com:

1. **Settings** (top-right).
2. In the left sidebar → **Pages**.
3. Under *Build and deployment* → **Source**: pick **Deploy from a branch**.
4. **Branch**: `main` / `/ (root)`. Click **Save**.
5. Wait 30–90 seconds. The published URL appears at the top of the Pages settings as `https://<your-username>.github.io/safi-picking-prototype/`.

Share that URL with colleagues. They open it in any modern browser — no install required.

### Updating later

Make changes locally, then:

```bash
git add .
git commit -m "Round N feedback"
git push
```

GitHub Pages rebuilds in ~1 minute.

## Files in this repo

- `index.html` — the entire prototype.
- `fonts/CircularXXTT-*.ttf` — Safi brand typeface, loaded via `@font-face`.
- `preview-shift-leader.png`, `preview-forklift-operator.png`, `preview-sticky-layout.png` — static screenshots for the README/repo.

## Notes for the next iteration

Things deliberately out of scope of this prototype but worth wiring up next:

- **FO touch UX** — there's an open question about whether FOs are allowed to operate tablets while on the forklift. The FO view is built so a second person (or the SL) can drive it on the FO's behalf — confirm the policy before designing for direct touch use.
- **Edit a sent picking list** — the prototype lets you Edit-list from the active picking list alert, which re-opens the create workflow with the previous selection. A production version may need finer-grained "add / remove without restarting".
- **Search and Supplier filter behaviour** — the dropdowns render but don't actually filter the load list in this prototype.
- **CSV export for audit** — flagged in customer feedback.
- **Material spec configuration** — currently hard-coded; should pull from the customer's spec settings.
- **Production window start** — moved out of the create workflow per feedback; rethink where this lives.
- **History** — currently shows individual processed loads. The live app groups by production window — consolidate when integrating.
