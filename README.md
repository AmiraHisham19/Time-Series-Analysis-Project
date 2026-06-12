# 🏥 Emergency Admissions Forecasting using Time Series Analysis
![Time](images/Time.png)

## 🚨 Why Emergency depertement Forcasting is important?

- Unplanned emergency admissions leads to significant stress on hospital resources and staff.
- Accurate forecasting enables hospitals to **allocate staff, beds, and resources proactively**.
- Predictive models can flag high-demand periods **before they occur**, supporting operational planning
  
## 📊 Dataset Overview
Daily hospital admissions data with the following characteristics:
- **Source**: HDHI Admission Data
- **Period**: April 2017 – December 2018 (~640 days)
- **Target variable**: Daily emergency admission count

## 🧠 Workflow Summary
 
### 🔹1- Data Preprocessing
- Parsed and converted admission dates to datetime format
- Grouped admissions by date and type (Emergency vs OPD)
- Reindexed to a continuous daily date range, filling missing dates with zero

![1](images/1.jpg)
![2](images/2.jpg)
![3](images/3.jpg)
![4](images/4.jpg)

### 🔹2- Exploratory Data Analysis
- Aggregated data at weekly, monthly, quarterly, and yearly levels
- Computed rolling mean, rolling standard deviation, and cumulative admissions
- Calculated month-over-month and quarter-over-quarter percentage changes
- Analysed ACF and PACF plots at daily and weekly levels
- Generated month plots and quarter plots to identify seasonal patterns

![5](images/5.jpg)![6](images/6.jpg)![7](images/7.jpg)![8](images/8.jpg)![10](images/10.jpg)![11](images/11.jpg)

### 🔹3- Decomposition
- Explored classical additive decomposition for initial visual inspection
- Applied **STL decomposition** (Seasonal-Trend decomposition using LOESS) directly to the original emergency series
  - `seasonal = 13`, `trend = 151`, `robust = True`
  - Compared multiple trend window sizes (91, 151, 201, 365) before selecting 151

**STL Components:**
 
| Component | Role |
|---|---|
| **Trend** | Long-run direction of admissions (~15–20/day) |
| **Seasonal** | Weekly recurring pattern (Monday peak, Sunday low) |
| **Residual** | Remaining noise passed to ARIMA |

### 🔹 4- Stationarity Testing
- Ran Augmented Dickey-Fuller (ADF) test on STL residuals
- **Result**: ADF = -12.92, p-value ≈ 0.000 → Residuals confirmed stationary ✅
- No differencing required (d = 0)

### 🔹 5- ARIMA Modelling
- Manually identified candidate orders from ACF/PACF plots
- Used `auto_arima` (pmdarima) to confirm optimal order
- **Selected model**: `ARIMA(3,0,0)` with no intercept
- Validated using:
  - Ljung-Box test (p = 0.90 → white noise confirmed ✅)
  - Diagnostic plots: standardised residuals, Q-Q plot, correlogram

![12](images/12.jpg)
![13](images/13.jpg)
![14](images/14.jpg)

### 🔹 Prophet Modelling
- Prepared data in Prophet format (`ds`, `y` columns)
- Configured with `weekly_seasonality=True`, `yearly_seasonality=True`
- Generated 90-day forecast with 95% confidence intervals

### 🔹 Model Evaluation
- **Prophet**: Built-in cross-validation (initial = 365 days, period = 30 days, horizon = 90 days)
- Metrics used: MAE, RMSE, sMAPE, MASE, Coverage
---
## ✅ Results & Insights
 
### Final Model Comparison
 
| Model | MAE | RMSE | sMAPE | Best For |
|---|---|---|---|---|
| **STL + ARIMA(3,0,0)** | 4.61 | 5.80 | 30.75% | Short-horizon (1–7 days) |
| **Prophet** | 4.95 | 6.13 | 29.5% | Long-horizon (30–90 days) |

### ✅ Selected Model: Prophet
- Better long-horizon performance
- Automatically handles weekly and yearly seasonality
- Easier to maintain, interpret, and present to clinical stakeholders
- Built-in cross-validation and uncertainty quantification

### 📅 90-Day Forecast Summary (Jan – Mar 2019)
- **Average daily forecast**: ~17 emergency admissions
- **Forecast range**: ~14–21 admissions/day
- **Coverage at 90 days**: 74.6% of actuals within 95% CI

### 🔍 Key Findings
 
| Finding | Detail |
|---|---|
| **Weekly pattern** | Mondays have the highest admissions; Sundays the lowest |
| **Trend** | Gradual upward trend in emergency admissions over the study period |
| **Inherent noise** | Daily emergency data has high natural variability (sMAPE ~30%)

## Author
Amira Hisham – Pharmacist & Healthcare Data Analyst
This project is part of my healthcare analytics portfolio. I’m passionate about bridging clinical insight with Machine learning to improve patient outcomes.
