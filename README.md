# Water Quality Prediction — Machine Learning Benchmark

Final-year Computer Science dissertation (KV6013, Northumbria University). A controlled comparison of Ridge Regression, Random Forest, MLP and XGBoost across five water-quality targets.

[Explore the project](https://nawafbalmutairi.github.io/ml-water-quality-benchmark/)

## What I built

I prepared Environment Agency monitoring data in Python and benchmarked four regression models across five water-quality targets, using IBM SPSS Modeler in the modelling workflow and Power BI to present the results.

The source is Environment Agency monitoring data from 2000–2025 across 14 areas in England. Approximately 6.7 million training samples and 1.6 million testing samples are summed across five target datasets. Each target has its own valid subset; these totals are not distinct sampling events and are not multiplied by the number of models.

## Why this benchmark

Which model works best depends on what it is predicting. This study holds the inputs, chronological split and evaluation metrics consistent within each target to compare four model families fairly.

## Workflow

1. **Environment Agency records.** Monitoring records from 2000–2025 across 14 Environment Agency areas in England, organised by area and year.

2. **Manual rename & review.** I renamed the downloaded files manually and spotted my own naming mistake: the Thames 2000 and 2001 files were swapped. I corrected them before constructing the final dataset.

3. **Handle censored measurements.** Retain the five selected parameters. Treat readings marked < or > as missing, because detection limits are not exact measurements.

4. **Long to wide.** Reshape individual measurements into columns for each sampling point and date. Each target uses its own valid subset, so sample counts differ between targets.

5. **Water measurements + calendar features.** Predict one parameter using the other four selected water-quality measurements plus year, month and day extracted from the observation date.

6. **Evaluate on later observations.** Train on 2000–2017 and test on 2018–2025. Approximately 6.7 million training and 1.6 million testing samples across the five target datasets, not multiplied by four models.

7. **Four models × five targets.** Benchmark Ridge, Random Forest, MLP and XGBoost within the IBM SPSS Modeler workflow. Python, Pandas and NumPy support preparation; scikit-learn and XGBoost support modelling and evaluation.

8. **Metrics, plots and Power BI.** Compare R², RMSE and MAE for every combination. Present the results in the report and an interactive Power BI dashboard. XGBoost leads on R² for four targets; Ridge leads on BOD, although all BOD R² scores are negative.

## Results

XGBoost has the highest R² on four targets. Water temperature is strongest: XGBoost 0.785 and Random Forest 0.729 both exceed 0.7. All BOD R² scores are negative; Ridge is best on BOD R², while XGBoost is best on BOD MAE. These findings apply to this dataset and setup, not every water-quality problem.

| Target | Model | R² | RMSE | MAE |
|---|---|---:|---:|---:|
| Nitrate as N | Ridge | -0.007 | 14.279 | 4.709 |
| Nitrate as N | Random Forest | -0.322 | 16.357 | 5.031 |
| Nitrate as N | MLP | -1.420 | 22.130 | 7.905 |
| Nitrate as N | XGBoost | 0.021 | 14.075 | 4.344 |
| BOD: 5 Day ATU | Ridge | -0.007 | 369.339 | 95.691 |
| BOD: 5 Day ATU | Random Forest | -3.487 | 779.768 | 83.334 |
| BOD: 5 Day ATU | MLP | -0.065 | 379.928 | 89.534 |
| BOD: 5 Day ATU | XGBoost | -0.269 | 414.710 | 75.232 |
| Water Temperature | Ridge | 0.106 | 4.315 | 3.441 |
| Water Temperature | Random Forest | 0.729 | 2.377 | 1.815 |
| Water Temperature | MLP | -3.611 | 9.799 | 8.161 |
| Water Temperature | XGBoost | 0.785 | 2.116 | 1.607 |
| Dissolved Oxygen | Ridge | 0.357 | 1.861 | 1.334 |
| Dissolved Oxygen | Random Forest | 0.179 | 2.103 | 1.214 |
| Dissolved Oxygen | MLP | 0.460 | 1.706 | 1.211 |
| Dissolved Oxygen | XGBoost | 0.503 | 1.636 | 1.129 |
| pH | Ridge | -0.012 | 0.418 | 0.294 |
| pH | Random Forest | 0.163 | 0.380 | 0.261 |
| pH | MLP | 0.083 | 0.398 | 0.281 |
| pH | XGBoost | 0.225 | 0.366 | 0.247 |

## Tools and deliverables

Python, Pandas and NumPy support data preparation. The report describes scikit-learn and XGBoost for modelling and evaluation, IBM SPSS Modeler in the benchmark workflow, Matplotlib for plots, and Power BI for the interactive results dashboard.

## Limitations and future work

The inputs exclude site and area context, rainfall, river flow, land use and discharge. Future work could add these variables, tune models per target and use further time-preserving evaluation. These are recommendations, not completed improvements.

The models estimate a target using other measurements and calendar features. A chronological holdout tests later observations; it does not establish forecasting from past-only inputs. Target sample sizes differ. Model-specific tuning was limited, and the results should not be generalised beyond the tested setting.

## Repository contents and reproducibility

This checkout contains the project presentation in `docs/`, this README, `requirements.txt` and the licence. It does not currently contain the submitted notebooks, model execution files, raw data or Power BI file. No end-to-end reproduction command or runtime is claimed here. The submitted report is the source for the workflow and Table 1 results above.

## Source of corrections

Reviewed against `AI Water Quality Prediction Project - Submission Folder/01_Report/AI WATER QUALITY.docx`, especially sections 6–8 and Table 1. Nawaf clarified that the Thames naming error arose during manual renaming and that he found it himself. The affected years were 2000 and 2001.
