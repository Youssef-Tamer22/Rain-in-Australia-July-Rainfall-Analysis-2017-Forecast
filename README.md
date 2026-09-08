# Rain-in-Australia-July-Rainfall-Analysis-2017-Forecast

Analysis of the [`weatherAUS`](https://www.kaggle.com/datasets/jsphyg/weather-dataset-rattle-package) dataset, focused on June/July rainfall patterns and an "analog year" estimate of daily rain probability for **July 2017**, plus an interactive Dash dashboard to explore the results.

**Authors:** Mahmoud Abdelghany, Abdulrahman Ateia, Youssef Tamer, Moaz Elsaeed, Mostafa Mohammed

## What this notebook does

1. **Load & clean data** — reads `weatherAUS.csv`, parses `Date` into `year`/`month`/`day`, and renames a couple of columns (`Rainfall(mm)`, `WindGustSpeed(km/h)`) for clarity.
2. **Filter to June & July** — subsets the data to `month` 6 and 7 (`july_data`).
3. **Handle missing values** — fills numeric columns (`Evaporation`, `Sunshine`, `Cloud9am/3pm`, wind speeds, humidity, pressure, etc.) with the monthly/column median.
4. **Encode targets** — maps `RainTomorrow` and `RainToday` from Yes/No to 1/0.
5. **Exploratory visuals** — rain-day counts, rain frequency by location/year/month, histograms of numeric features, and a correlation heatmap.
6. **Daily climatology** — average % of days with rain per calendar day of July (2009–2016), plotted with Plotly.
7. **Analog-year method for 2017**:
   - Builds per-year June stats (rain-day %, avg rainfall, avg humidity, avg pressure) for the first 25 days of June.
   - Standardizes (Z-scores) each historical June against June 2017 and computes a Euclidean distance — the closest years are the best "analogs" for 2017.
   - Converts distances into similarity weights.
   - Applies those weights to each historical July's daily rain % to produce a **weighted daily rain-probability estimate for July 2017**, alongside a plain (unweighted) average.
   - Saves the result to `july2017_final.csv`.
8. **Validation** — prints actual `RainTomorrow` counts for June/July 2017 to sanity-check the estimate against ground truth.
9. **Historical value imputation helper** — a `old_value()` function that fills missing values for a given location/date/column using historical means/modes, optionally conditioned on whether it rained that day.
10. **Dashboard (Dash + Bootstrap)** — a dark-themed Dash app (`DARKLY` theme) that displays:
    - A data table of `july_data`.
    - Figure 1: daily rain % climatology (2009–2016).
    - Figure 2: July 2017 daily rain probability estimate.
    - Figure 3: line comparison of plain vs. analog-weighted vs. final estimate.

## Requirements

```
numpy
pandas
seaborn
matplotlib
plotly
dash
dash-bootstrap-components
```

Install with:
```bash
pip install numpy pandas seaborn matplotlib plotly dash dash-bootstrap-components
```

## Data

This notebook uses **`weatherAUS.csv`**, the "Rain in Australia" dataset (~10 years of daily weather observations from Australian weather stations, originally sourced from the Bureau of Meteorology):

- Kaggle: https://www.kaggle.com/datasets/jsphyg/weather-dataset-rattle-package

Download it, then update the file path in the data-loading cell (currently `C:\Users\Lenovo\Downloads\weatherAUS.csv`) to point to wherever you save it.

## Running

1. Update the CSV path in the data-loading cell to match your machine.
2. Run all cells top to bottom. The EDA and analog-weighting cells must run before the dashboard cell, since the dashboard reuses `july_data`, `fig1`, `fig2`, and `fig3` from earlier in the notebook.
3. The dashboard cell starts a local Dash server:
   ```
   app.run(debug=True, port=8020)
   ```
   Open **http://127.0.0.1:8020/** in your browser to view it.

## Output

- `july2017_final.csv` — per-day table with `plain_rain_%`, `analog_weighted_%`, and `final_estimate_%` for July 2017.
- Interactive Plotly charts (inline) and a Dash web dashboard summarizing the analysis.

## Notes / limitations

- The analog-year approach assumes years with similar June conditions will have similar July rainfall patterns — a heuristic, not a statistical forecast model.
- Missing-value imputation uses simple medians/historical means rather than a fitted model.
- File paths are hardcoded to a specific local machine and will need to be changed to run elsewhere.
