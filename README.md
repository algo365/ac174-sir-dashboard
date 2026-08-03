# AC-174 Mahadevapura — SIR 2026 Digitization Gap vs MP 2024 Voters

Interactive, fully client-side dashboard (HTML + JSON, no backend). Hosted on GitHub Pages.

- **Live:** https://algo365.github.io/ac174-sir-dashboard/
- **Offline copy:** `AC174_SIR_Dashboard_offline.html` — all data (incl. deletion rosters)
  embedded; opens by double-click, no server.

## Levels & features
Four drill-down levels: **Assembly → Mandala → Ward → Booth**.

- **KPI strip** at every level: Total Voters, **MP Voted 2024**, SIR Submitted, To Delete,
  Not-yet-submitted, **MP %** (coverage = SIR Submitted ÷ MP Voted 2024), Digitized %.
- **MP Voted % Coverage** and **Digitized %** headline gauges (green/amber/red).
- Area charts: MP-Voted vs SIR-Submitted (gap) and Total vs SIR-Submitted (pending).
- **Ward tiles** show Submitted and To-Delete as highlighted numbers plus a filled
  composition bar (Submitted + Permanently Shifted + Untraceable + EF Refused + Death +
  Enrolled Elsewhere) with per-category counts.
- **Booth table** (sortable) with BLO and Supervisor; click a booth for its report.
- **Booth deletion report:** the individual deleted / to-be-deleted voters (name, EPIC,
  relative, age, reason) with **filter by reason** and **search** within the booth.
- Mandala filter (Urban/Rural/All) and ward/booth search.

## Data & sources
- Booth aggregates: `data/ac174.json` (from `AC174_SIR_LS_BoothWise_Full.csv`, 527 booths).
- Per-booth deletion rosters: `data/booth/<n>.json` (fetched on demand).
- `data/booth_index.json` — per-booth deletion category counts.
- `data/snapshots/` — dated data snapshots (previous dumps).

**Individual deletion entries** are from the **SIR draft ASDD list dated 13-07-2026**,
published by the ERO for public claims & objections. A booth's *aggregate* to-delete
count comes from the current booth-wise dump and can exceed the number of named entries
in the 13-07 list (the dashboard states this per booth). MP Voted 2024 = votes polled in
the 2024 parliamentary (Lok Sabha) election.

## Updating data
Regenerate JSON from source and push; the page picks it up on next load.
```
python build_ac174.py          # aggregates + BLO + ward-president slots
python build_booth_reports.py  # per-booth deletion rosters
python build_singlefile.py     # offline single-file
```
Ward-president names: fill the `president` column in `ward_meta.csv` and re-run.

## Deploy
Pushed to `main`; GitHub Pages serves from root (`.nojekyll` keeps `data/` intact).
`index.html` fetches with **relative** paths, so it works under the repo subpath.
