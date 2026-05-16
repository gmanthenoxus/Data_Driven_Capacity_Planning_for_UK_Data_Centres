# Data-Driven Capacity Planning for UK Data Centres Using Predictive Modelling

**Author:** Moses Mana Gana  
**Environment:** Google Colab  
**Open In Colab:** [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/your-username/your-repo/blob/main/notebook.ipynb)

## Project Overview

This project implements a **data‑driven approach to capacity planning** for data centres, with a focus on UK infrastructure. It compares four time‑series forecasting models—**ARIMA**, **Random Forest (RF)**, **XGBoost (XGB)**, and **LSTM**—on real‑world cloud workload traces and synthetic UK data. The goal is to accurately predict resource usage (CPU, memory, disk, network) to enable efficient capacity provisioning.

## Datasets

| Dataset | Source | Metrics | Period |
|---------|--------|---------|--------|
| Alibaba 2018 | [Alibaba Cluster Trace v2018](http://aliopentrace.oss-cn-beijing.aliyuncs.com/v2018Traces/machine_usage.tar.gz) | CPU, memory, disk I/O, network I/O | First 8 days (5‑min bins) |
| Google 2019 | [Google Cluster Data](https://cloud.google.com/blog/products/compute/google-cluster-data-v3) | CPU (cluster average) | First 8 days (5‑min bins) |
| Azure V2 | [Azure Public Dataset](https://github.com/Azure/AzurePublicDataset) | CPU (VM readings) | First 8 days (5‑min bins) |
| Synthetic UK | Generated with daily/weekly/trend/noise patterns | CPU, memory, storage I/O | 8 days (5‑min bins) |
| UK Power Networks (validation) | Real power demand profiles | Power ratio (load/max capacity) | 8 days (5‑min bins) |

> **Note:** The UKPN CSV file must be uploaded manually when prompted (filename `ukpn-data-centre-demand-profiles.csv`).

## Methodology

### Preprocessing
- Resampling to 5‑minute intervals.
- Linear interpolation for missing values.
- **MinMax scaling** (normalisation) per metric.

### Feature Engineering (for RF and XGBoost)
- Lag features: `t-1, t-2, t-3, t-6, t-12`
- Rolling statistics: mean & std over windows of 12 (1 hour) and 72 (6 hours)
- Time features: hour, day of week, weekend flag

### Models
- **ARIMA** (5,1,2) – statistical baseline
- **Random Forest** (100 estimators)
- **XGBoost** (100 estimators, max depth 3, learning rate 0.1)
- **LSTM** (two LSTM layers with dropout, sequence length 12)

### Evaluation Metrics
- **MAE** (Mean Absolute Error)
- **RMSE** (Root Mean Square Error)

## Results

### Model Performance (MAE – lower is better)

| Dataset        | ARIMA   | RF     | XGB    | LSTM   |
|----------------|---------|--------|--------|--------|
| Alibaba CPU    | 0.2915  | 0.0755 | 0.0767 | **0.0560** |
| Alibaba Memory | 0.1606  | 0.0546 | 0.0560 | 0.0574   |
| Alibaba Disk   | 0.1235  | 0.0449 | 0.0468 | 0.0474   |
| Alibaba Net    | 0.0322  | 0.0981 | 0.1035 | **0.0066** |
| Google         | 0.1597  | 0.0398 | 0.0402 | 0.0417   |
| Azure          | 0.2124  | 0.0352 | 0.0380 | 0.0441   |
| Synthetic UK   | 0.1301  | 0.0737 | 0.0726 | 0.0811   |

- **LSTM** achieves the lowest MAE on 5 out of 7 datasets (especially Alibaba CPU, Net, Google, Azure).
- **Random Forest** is competitive on memory, disk, and Azure CPU.
- **ARIMA** consistently underperforms, confirming the need for non‑linear models.

### Visualisations

The notebook generates:

1. **MAE Comparison Bar Chart** – side‑by‑side model performance.
2. **Heatmap** – colour‑coded MAE across models and datasets.
3. **LSTM Forecast vs Actual** – for Alibaba CPU test set.
4. **Synthetic vs Real UK Power Pattern** – correlation check.
5. **Interactive Dashboard** – select dataset & model to see forecast plot with MAE/RMSE.

## How to Run

1. **Open the notebook** in Google Colab (click the badge at the top).
2. **Run the first cell** – it installs all required libraries.
3. **Execute cells sequentially** – data will be downloaded automatically (Alibaba, Google, Azure).  
   - For UKPN, you will be prompted to upload the CSV file.
4. **All results** (tables, plots, interactive dashboard) will appear inline.

> ⚠️ **Large downloads** – The Alibaba trace is ~1.7 GB, Azure ~50 GB (only first 60 files are downloaded). Ensure you have sufficient disk space.

## Dependencies

The notebook installs:
- `pandas, numpy, matplotlib, seaborn`
- `scikit‑learn, xgboost, statsmodels`
- `tensorflow` (for LSTM)
- `plotly` (interactive dashboard)
- `google‑colab` (file upload, widgets)


## Key Findings

- **LSTM** excels at capturing complex, non‑linear patterns in cloud workloads, especially network I/O (MAE 0.0066 on Alibaba Net).
- **Tree‑based models** are strong baselines and require less tuning.
- **Synthetic UK CPU** shows moderate correlation with real UKPN power demand, suggesting it can be used for preliminary capacity planning when real data is scarce.
- The **interactive dashboard** allows stakeholders to explore forecasts for any metric and model without rerunning the entire pipeline.

## Future Work

- Extend to multi‑step ahead forecasting (e.g., 1‑hour, 6‑hour).
- Incorporate exogenous variables (weather, holidays, real power prices).
- Deploy best model as a lightweight API for real‑time capacity alerts.
- Test on more recent traces (e.g., Alibaba 2022, Google 2020).

## License

This project is open‑source under the MIT License.

## Contact

**Moses Mana Gana** – [GitHub Profile](https://github.com/your-username)
