# EV Adoption Panel Study / 跨国新能源汽车渗透率面板研究

Cross-country panel-data study of the drivers of electric-vehicle (EV) adoption:
53 countries, 2015–2024, country fixed-effects and two-way fixed-effects
regressions of the EV share of new passenger-car sales on GDP per capita (PPP),
population, Brent oil price and public charging-infrastructure density, plus
robustness and income-group heterogeneity checks. The repository holds the
analysis scripts, the derived panel data, result tables, figures and the paper
(LaTeX source and compiled PDF, in Chinese).

## Technologies

- Python: `pandas`, `numpy`, `linearmodels` (`PanelOLS`), `matplotlib`,
  `openpyxl` (reads the IEA Excel file); `analysis_refresh.py` also uses
  `wbgapi` (World Bank API client).
- LaTeX for the paper (`05_paper_source/论文.tex`). The exact TeX engine/build
  command is not recorded in the repo.
- Data sources (see `05_paper_source/METHODOLOGY.md`): IEA Global EV Outlook
  2025 (EV Data Explorer), World Bank WDI (`NY.GDP.PCAP.PP.CD`, `SP.POP.TOTL`),
  U.S. EIA Brent spot price.

## Directory map

| Path | Contents |
|---|---|
| `01_code/analysis.py` | Original pipeline (charger **growth rate**, lagged, as infrastructure variable). |
| `01_code/analysis_refresh.py` | Refreshed pipeline (interpolated charger **density** as main variable); writes all tables/figures. |
| `02_data/` | Raw inputs `wb_gdp.csv`, `wb_pop.csv`, `Europe_Brent_Spot_Price_FOB.csv`; derived `panel_final.csv` (501 rows), `regression_sample_main.csv` (N=346), `variable_dictionary.csv`, `data_coverage_metrics.csv`, `density_form_robustness.csv`; `cross_country_panel_with_interpolated_density.csv` is byte-identical to `panel_final.csv`. |
| `03_results/` | `regression_main.csv` (M1–M5), `regression_heterogeneity.csv` (HIC vs non-HIC), `descriptive_stats.csv`. |
| `04_figures/` | `fig1`–`fig4` PNGs used by the paper. |
| `05_paper_source/` | `论文.tex`, `METHODOLOGY.md` (data/method notes, World Bank PowerShell download commands), `README.md` (handoff notes and main findings). |
| `06_paper_compiled/论文.pdf` | Compiled paper. |
| `模范论文.zip` | Three third-party example papers (PDF) kept as writing references; not part of the analysis. Redistribution permission has not been verified. |

## Input data paths

Both scripts read and write **bare file names in the current working
directory**; they do not use the `01_`–`06_` folders. Required inputs:

| File | In repo? |
|---|---|
| `EVDataExplorer2025(4).xlsx` (IEA EV Data Explorer, sheet `GEVO_EV_2025`) | **No** – must be obtained from the IEA separately. |
| `wb_gdp.csv`, `wb_pop.csv` | Yes, `02_data/` |
| `Europe_Brent_Spot_Price_FOB.csv` | Yes, `02_data/` |

## Original vs. refresh script

| | `analysis.py` | `analysis_refresh.py` |
|---|---|---|
| Infrastructure regressor | `chargers_growth_lag1` (charger growth, t-1) | `chargers_per_mn_itp` (chargers per million people, linear interpolation over gaps ≤ 2 years) |
| World Bank data | Local `wb_gdp.csv` / `wb_pop.csv` only | Fetched with `wbgapi`, falls back to the local CSVs; income groups are always fetched online (`wb.economy`), so network access is required |
| Income groups | Hard-coded 2023 WB classification | From the World Bank API |
| Derived-variable window | Filters to 2015–2024 before computing growth | Computes on 2013–2024, models 2015–2024 |
| Missing chargers | Fast+slow with missing treated as 0 | Total left missing when both are missing |
| Outputs written | Only `panel_final.csv` (tables printed to console; the "save tables" section is omitted despite the docstring) | Panel, regression sample, M1–M5 and heterogeneity tables, descriptive stats, variable dictionary, coverage metrics, density-form robustness, four figures |

The committed outputs (e.g. `regression_sample_main.csv`,
`density_form_robustness.csv`, density-based tables) correspond to the file
names and variables of `analysis_refresh.py`.

## Running (not verified)

Run in a scratch directory so the committed outputs are not overwritten:

```bash
pip install pandas numpy openpyxl linearmodels matplotlib wbgapi
work=$(mktemp -d)
cp 01_code/analysis_refresh.py 02_data/wb_gdp.csv 02_data/wb_pop.csv \
   02_data/Europe_Brent_Spot_Price_FOB.csv "$work"/
cp /path/to/'EVDataExplorer2025(4).xlsx' "$work"/   # obtain from IEA
cd "$work" && python analysis_refresh.py
```

Then compare the generated files with `02_data/`, `03_results/` and
`04_figures/`. **Reproducibility status:** the scripts have not been
re-executed for this README because the IEA input file is not in the
repository (and the refresh script needs live World Bank API access).
Results may also differ if the IEA or World Bank data have been revised.

## Main findings

See `05_paper_source/README.md` and the paper for the reported coefficients
and their caveats (oil-price effect not robust; policy and electricity-price
variables excluded because the data were unavailable).
