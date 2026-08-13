# ETC3550 Retail Project - Time Series Forecasting
 
Forecasting monthly retail **turnover** for the **Food retailing** industry in the **Northern Territory** of Australia, using ETS and ARIMA models. The workflow covers data exploration, transformation and differencing, model building, residual diagnostics, model comparison, and validation against real ABS data.

---
 
## Overview
 
The project uses Australian Bureau of Statistics (ABS) retail turnover data (Apr 1988 – Dec 2018, 369 monthly observations) to:
 
1. Explore trend, seasonality, and autocorrelation in the series.
2. Stabilise variance (Box–Cox) and achieve stationarity (differencing).
3. Fit and compare **ETS** and **ARIMA** models.
4. Validate the chosen models against newer ABS data (through Mar 2023).
## Data
 
| Field | Description |
|-------|-------------|
| `State` | Northern Territory |
| `Industry` | Food retailing |
| `Series ID` | ABS series identifier (e.g. `A3349527K`) |
| `Month` | Monthly index (Apr 1988 onward) |
| `Turnover` | Retail turnover (numeric) |
 
## Exploratory Analysis
 
- **Trend:** Overall upward trend from 1988 to 2019, with slight dips around 1997 and 2000.
- **Seasonality:** Turnover typically dips in Jan–Feb and peaks around July each year; seasonal variation grows as the series level rises.
- **ACF/PACF:** Slow-decaying ACF (trend) with local peaks at lags 12, 24, 36 (yearly seasonality); PACF spikes support a yearly rather than half-yearly cycle.
## Transformation & Differencing
 
- **Box–Cox transformation** via Guerrero's method (λ ≈ −0.062, close to a log transform) to stabilise variance.
- **KPSS test** on the transformed data indicated non-stationarity.
- Applied **one seasonal difference**; a follow-up KPSS test (p-value > 0.05) confirmed stationarity, with no additional first differencing required.
## ETS Modeling
 
Candidate models (multiplicative seasonality, additive/damped trend):
 
- `ETS(M, A, M)` — Multiplicative error, Additive trend, Multiplicative season
- `ETS(M, Ad, M)` — Additive **damped** trend
- `ETS(A, A, M)` — Additive error
- Automatic `ETS()` selection
**Selected model:** `ETS(M, Ad, M)` — chosen automatically and confirmed by the lowest AIC/AICc and strong test-set accuracy.
## ARIMA Modeling
 
Candidate models (from the seasonally differenced series):
 
- `ARIMA(2,0,0)(1,1,0)[12]`
- `ARIMA(2,0,1)(1,1,1)[12]`
- `ARIMA(2,0,1)(1,1,0)[12]`
- Stepwise search: `ARIMA(1,0,2)(2,1,1)[12] w/ drift`
- Wider search: `ARIMA(4,0,0)(0,1,2)[12] w/ drift`

## Validation Against ABS Data
 
Forecasts were extended 4 years 3 months (to Mar 2023) and compared with actual ABS releases:

  
- **Limitations:** Evaluation relied on a single 24-month test set rather than time-series cross-validation (avoided due to computational cost). The ETS model was fitted on untransformed training data; using a transformation and/or cross-validation may yield improvements and warrants further testing.
---
 
* Analysis was performed in R using the `fpp` ecosystem.
