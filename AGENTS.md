# AGENTS.md

Cross-country EV adoption panel study (Python + LaTeX). Overview: `README.md`.

## Map

- `01_code/` – `analysis.py` (original, charger growth) and `analysis_refresh.py` (current, interpolated charger density)
- `02_data/` – raw WB/EIA CSV inputs and derived panel/sample/metadata CSVs
- `03_results/` – regression and descriptive tables
- `04_figures/` – paper figures
- `05_paper_source/` – `论文.tex`, `METHODOLOGY.md`, handoff `README.md`
- `06_paper_compiled/` – compiled PDF

## Run

Scripts use bare file names in the working directory. Copy the script and its
inputs into a temp dir and run there (see README "Running"). The IEA input
`EVDataExplorer2025(4).xlsx` is not in the repo; `analysis_refresh.py` needs
network access to the World Bank API.

## Test

No automated tests. Verification = run `analysis_refresh.py` in a temp dir and
diff its outputs against `02_data/`, `03_results/`, `04_figures/`.

## Constraints

- Never run scripts inside `01_code/` or the repo root: they overwrite outputs.
- Do not fabricate policy/electricity-price data (see handoff notes).
- `模范论文.zip` holds third-party papers; redistribution rights unverified.
- Do not claim reproducibility without an actual run.

## Definition of done

Docs match the actual tree and script paths; any re-run result (or why it was
not run) is stated; paper numbers, tables and figures stay consistent.
