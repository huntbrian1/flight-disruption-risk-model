# Flight Disruption Risk Model: Three-Season Holiday / Winter Operating Window Analysis

This project uses public BTS / DOT airline on-time performance data to analyze disruption risk across matched November-January holiday/winter operating windows from 2023-2024, 2024-2025, and 2025-2026. The goal is to identify recurring disruption patterns and build an operations-focused risk-screening framework for monitoring and extra on-call staffing prioritization.

The analysis is intentionally scoped to holiday/winter operating windows. It is not an all-year airline delay model.

## Business / Operations Question

Across matched November-January operating windows, do scheduled flight characteristics, holiday-period context, and departure-time patterns identify higher-risk flights and operating windows before disruption is realized?

## Data Source

Source: Bureau of Transportation Statistics / U.S. DOT  
Dataset: On-Time: Reporting Carrier On-Time Performance (1987-present)

Months used:

- `2023_11`
- `2023_12`
- `2024_1`
- `2024_11`
- `2024_12`
- `2025_1`
- `2025_11`
- `2025_12`
- `2026_1`

Required BTS monthly CSV files:

- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2023_11.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2023_12.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2024_1.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2024_11.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2024_12.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2025_1.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2025_11.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2025_12.csv`
- `On_Time_Reporting_Carrier_On_Time_Performance_(1987_present)_2026_1.csv`

The raw monthly BTS files and the generated combined file are intentionally excluded from Git because they are large. The notebook combines the nine monthly files into `bts_on_time_performance_3season_nov_jan.csv` for local analysis.

## Scope

This project isolates November, December, and January across three matched operating seasons:

- 2023-2024: November 2023, December 2023, January 2024
- 2024-2025: November 2024, December 2024, January 2025
- 2025-2026: November 2025, December 2025, January 2026

The narrow scope is deliberate. Holiday travel patterns, winter weather exposure, and January recovery conditions differ from normal operating months, so the analysis compares like-for-like seasonal windows instead of blending unrelated months into a general model.

## Methodology

- Data import and validation across nine monthly BTS files
- Disruption target construction: `ArrDel15`, `Cancelled`, or `Diverted`
- Season-aware holiday/baseline windows
- Holiday/baseline performance comparison
- Daily spike analysis
- Scheduled departure time-block analysis
- Random Forest models:
  - Base RF
  - Holiday-Aware RF
  - Season-Calibrated RF
- Random split validation
- Temporal holdout validation
- January holdout validation
- Staffing-window aggregation and prioritization

## Key Findings

Across all 5,084,031 flights in the three November-January operating seasons, the overall disruption rate was 21.21%.

Within the defined holiday/baseline windows, Christmas Week was the highest-risk window overall, with a 25.40% disruption rate. The January 15-30 baseline was close behind at 24.32%, followed by New Year's Week at 23.87% and Thanksgiving Week at 19.69%.

The January baseline showed a cancellation-heavy disruption pattern. Across all seasons, the January 15-30 baseline had a 4.74% cancellation rate, higher than the holiday windows. In the 2025-2026 season, the January baseline reached a 28.60% disruption rate and a 7.78% cancellation rate.

The largest daily disruption spike occurred on January 25, 2026, during the January baseline window. That day reached a 69.70% disruption rate, including a 46.48% cancellation rate.

Scheduled departure time-block analysis showed higher late-day risk in the latest season. In 2025-2026, late-day disruption reached 35.99% during Christmas Week and 40.39% during New Year's Week. This supports late-day monitoring and staffing prioritization during high-pressure operating windows.

In random split validation, the highest-scored 10% of flights disrupted at 38.63%, compared with a 21.21% baseline, or 1.82x baseline risk. The Season-Calibrated RF had the strongest random split performance, with ROC AUC of 0.666 and PR AUC of 0.363.

In temporal holdout validation, the Holiday-Aware RF trained on 2023-2024 and 2024-2025 and tested on the unseen 2025-2026 season. The highest-scored 10% of flights disrupted at 33.67%, compared with a 25.17% latest-season baseline, or 1.34x baseline risk.

In January holdout validation, the model trained on prior history plus latest-season November/December and tested on latest-season January. The highest-scored 10% of January flights disrupted at 30.26%, compared with a 24.68% January baseline, or 1.23x baseline risk. The top 5% performed better, at 33.11%, or 1.34x baseline risk.

The staffing-window recommendation layer translated flight-level risk scores into date / airport / departure-block groups. Windows flagged for extra on-call coverage had a weighted realized disruption rate of 38.75%, compared with 35.61% for monitor-closely windows and 27.30% for normal coverage windows.

## Model Interpretation

The Random Forest models are used as risk-ranking tools, not calibrated probability engines. The strongest interpretation is whether higher-score buckets concentrate more realized disruption than the baseline. This makes the project best suited for operations decision support, risk-ranking, and extra on-call staffing prioritization rather than automated staffing decisions.

## Repository Structure

```text
.
|-- README.md
|-- flight_disruption_3season_final_polished.ipynb
|-- outputs/
|   |-- period_summary_by_season.csv
|   |-- period_summary_all_seasons.csv
|   |-- daily_summary_by_season.csv
|   |-- top_daily_disruption_spikes.csv
|   |-- timeblock_summary_by_period.csv
|   |-- late_day_summary_by_season_period.csv
|   |-- model_comparison.csv
|   |-- random_split_lift_table.csv
|   |-- temporal_holdout_metrics.csv
|   |-- temporal_holdout_lift_table.csv
|   |-- january_holdout_metrics.csv
|   |-- january_holdout_lift_table.csv
|   |-- staffing_recommendations.csv
|   `-- staffing_recommendation_distribution.csv
`-- data/
    `-- raw/        # local only; large BTS CSV files are not committed
```

## How to Reproduce

1. Download the nine BTS monthly CSVs from BTS TranStats for the months listed above.
2. Place the files in `data/raw/`.
3. Open and run `flight_disruption_3season_final_polished.ipynb` top-to-bottom.
4. The notebook will build `bts_on_time_performance_3season_nov_jan.csv` locally and write summary outputs to `outputs/`.

## Skills Demonstrated

- Python
- pandas
- scikit-learn
- feature engineering
- classification modeling
- temporal validation
- operational analytics
- data validation
- portfolio-ready business interpretation
