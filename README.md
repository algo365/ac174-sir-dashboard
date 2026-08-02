# AC-174 Mahadevapura — SIR 2026 Digitization Gap vs MP 2024 Voters

Interactive, fully client-side dashboard (HTML + JSON, no backend). Hosted free on
**GitHub Pages**.

- **Live site:** `https://<your-user>.github.io/<repo>/` (fill in after first deploy)
- **Offline copy:** open `AC174_SIR_Dashboard_offline.html` — data embedded, opens by
  double-click, no server needed.

## What it shows
Four drill-down levels (Assembly → Mandala → Ward → Booth) with a KPI strip,
Coverage % and Digitized % headline gauges, MP-Voted-vs-Submitted and
Total-vs-Submitted area charts, a 5-way deletion breakdown, clickable cards, a
sortable booth table, per-booth detail, a Mandala filter and ward/booth search.
527 booths · 30 wards · 2 mandalas.

## Files
```
index.html                       the dashboard (fetches ./data/ac174.json)
data/ac174.json                  the data (single source of truth)
AC174_SIR_Dashboard_offline.html self-contained offline copy (data embedded)
.nojekyll                        serve files as-is (no Jekyll processing)
README_DEPLOY.md                 alternative hosting notes (AWS S3/EC2)
```

## Updating the numbers
Regenerate `data/ac174.json` from the source CSV and commit — the page picks it up
on next load; no HTML changes needed.
```
python build_ac174.py    # (script lives one level up in the runs/ folder)
```

## Deploy to GitHub Pages
1. Push this folder to a GitHub repo (see the commands your assistant provided).
2. Repo **Settings → Pages → Source: Deploy from a branch → `main` / root**.
3. Wait ~1 min; the site publishes at `https://<user>.github.io/<repo>/`.

Because `index.html` fetches `./data/ac174.json` with a **relative** path, it works
under the repo subpath with no extra configuration.
