# Future Urban Heat in Greater Manchester: Warming Trends, Extreme Days and Machine Learning

Project 2 for **EART60702**  **Group 1**  
University of Manchester 2025

## Project overview

This project investigates how **Manchester’s urban daily maximum temperature** may change by the end of the 21st century under a **high-emission scenario (RCP 8.5)**, using climate-model data from the **CESM Large Ensemble (CESM-LENS)** and a set of machine learning models.

Our core research question is:

> **How will Manchester’s urban maximum temperature change by 2080 under a high-emission scenario — and which climate variables most strongly shape that change?**

The study combines:
- **exploratory data analysis (EDA)** of long-term warming and extreme-heat behaviour,
- **machine learning regression models** for temperature prediction,
- and **extreme heat diagnostics** to assess future urban heat risk.

The project uses the **Manchester grid cell (53.48°N, 2.24°W)** and focuses on the target variable **`TREFMXAV_U`**, the **urban daily maximum temperature**. The dataset spans **2006–2080**, using **six CESM-LENS initial-condition ensemble members** under **RCP 8.5**.

---

## Objectives

The project aims to:

1. quantify the long-term warming trajectory of Manchester urban maximum temperature,
2. assess whether extreme hot days increase faster than average conditions,
3. compare multiple machine learning models for daily urban temperature prediction,
4. identify which climate variables contribute most strongly to the warming signal,
5. evaluate how well different models detect extreme heat events.

---

## Data

### Source
- **CESM-LENS** climate model output
- **Scenario:** RCP 8.5
- **Period:** 2006–2080
- **Ensemble size:** 6 initial-condition members

The six-member design is used to separate the externally forced climate signal from internal variability. Ensemble averaging helps reveal the anthropogenic warming signal, while the spread across members provides a measure of uncertainty.
### Location
- Nearest climate-model grid cell to **Manchester**
- Approximate coordinates: **53.48°N, 2.24°W**

### Target variable
- **`TREFMXAV_U`**: urban daily maximum temperature (converted from Kelvin to °C)

### Climate predictors
The project uses the following physical variables from CESM-LENS:

- `TREFHT` — reference-height temperature
- `PRECT` — total precipitation
- `PRSN` — snowfall
- `QBOT` — near-surface water vapour mixing ratio
- `FLNS` — net surface longwave radiation
- `FSNS` — net surface shortwave radiation
- `UBOT` — zonal wind
- `VBOT` — meridional wind

These variables are unit-harmonised during preprocessing, including temperature conversion to °C, precipitation conversion to mm/day, and humidity conversion to g/kg for interpretability.
---

## Methodology

The project pipeline follows five stages: **ingestion, preprocessing, feature engineering, modelling, and evaluation**.

### 1. Data ingestion
- Read NetCDF climate files
- Extract the grid cell closest to Manchester
- Handle CESM’s **no-leap calendar**

### 2. Preprocessing
- Convert physical units
- Handle missing urban temperature values where necessary
- Keep all splits strictly chronological

### 3. Feature engineering
We construct a set of time-series predictors, including:
- lag features,
- rolling statistics,
- cyclical time features (`sin` / `cos` seasonality),
- and lagged climate-driver variables.

A key design rule is that **the target variable is never leaked into the predictors**, and all lag or rolling features are built with proper temporal shifting. 

### 4. Model comparison
We compare six predictive models:
- Random Forest
- XGBoost
- LightGBM
- LSTM
- 1D-CNN
- CNN-LSTM

We also test:
- **SVR**
- **SVR with PCA**
- a **weighted ensemble** based on model performance

### 5. Evaluation
Model performance is assessed using:
- **MAE**
- **RMSE**
- **R²**
- **MAPE**

For extreme heat events, additional event-based metrics are used:
- **Probability of Detection (POD)**
- **False Alarm Ratio (FAR)**
- **Threat Score (TS)** 
---

## Key findings

### 1. Clear warming signal
Manchester’s urban daily maximum temperature shows a strong upward trend under RCP 8.5. The ensemble analysis finds a warming rate of about **+0.039 °C per year**, and the forced signal emerges clearly from internal variability by around **2016**. 

### 2. Extreme heat becomes more frequent and more severe
The project finds that:
- the hottest urban day rises from **38.1 °C** in the baseline period to about **42.7 °C** by the late-century period,
- days above **25 °C** increase dramatically,
- and the hottest **1% of days** warm faster than the mean. 
This means future Manchester heat is not just warmer on average — it is also **more extreme and more unevenly distributed**.

### 3. Tree-based models perform best
Among the main regression models, **LightGBM** performs best overall, reaching approximately:
- **MAE = 0.51 °C**
- **R² = 0.984**

XGBoost performs similarly well, while Random Forest is slightly weaker. Tree-based models clearly outperform the deep-learning models on this task. 
### 4. SVR gives strong results, especially without PCA
The SVR experiments show that:
- **SVR (full features)** achieves **RMSE = 0.7058 °C** and **R² = 0.9823**
- **SVR (PCA-reduced)** achieves **RMSE = 0.9567 °C** and **R² = 0.9674**

This shows that PCA can reduce dimensionality while preserving strong performance, but the original SVR remains more accurate. The PCA results also suggest that the urban heat signal is governed by a relatively small number of dominant physical modes. 
### 5. Climate drivers of urban heat
The most important drivers are:
- **background temperature**
- **humidity**
- **solar radiation**

These variables most strongly shape future urban maximum temperature, while snowfall declines and precipitation remains more variable and uncertain. 
---

## How to run

### 1. Create the environment

```bash
conda env create -f environment.yml
conda activate manchester-temp-ml
```

### 2. Launch Jupyter

```bash
jupyter notebook
```

### 3. Run the notebook
Open the main notebook and run all cells in order.

---

## Outputs

The project produces:
- exploratory analysis figures,
- model comparison plots,
- SVR and PCA-SVR performance figures,
- future prediction tables,
- and best-model prediction CSV files.

These outputs support both the scientific interpretation and the presentation of results.

---

## Authors

**Group 1**  
Shiyu Wang，Xinhao Zhou，Hanjin Yue
---
