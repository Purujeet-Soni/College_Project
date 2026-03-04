# College_Project
This project predicts Reliance Industries stock prices using the Facebook Prophet model with 15-minute OHLC and volume data from 3 Mar 2025 to 3 Mar 2026. The model analyzes trends, seasonality, and volatility to forecast short-term price movements and visualize future predictions with confidence intervals.
Reliance Stock Prediction Custom Report: Quantitative Analysis and Predictive Modeling

1. Executive Summary

The objective of this quantitative study is to construct a high-fidelity predictive framework for Reliance Industries Limited (RIL), suitable for institutional-grade equity forecasting. By leveraging high-frequency intraday data, this analysis seeks to decompose non-stationary price action into deterministic components to inform strategic positioning. Based on the provided longitudinal data—which demonstrates a price appreciation from 1,183.55 (March 2025) to 1,394.9 (February 2026)—the model yields a definitive Bullish stance. This represents a nominal appreciation of approximately 17.8%, reflecting a robust Compounded Annual Growth Rate (CAGR) within the observed period. The directional thesis is supported by a persistent upward trend component isolated via multiplicative decomposition. This specific architectural choice—utilizing a multiplicative model—is necessitated by the heteroscedastic nature of equity prices, where seasonal variations and volatility tend to scale proportionally with the absolute price level. The resulting forecast suggests a continuation of the primary trend, contingent upon the structural integrity of the identified liquidity cycles.

2. Data Overview & Preprocessing

Capturing intraday volatility and latent liquidity patterns in a high-cap equity like RIL requires high-frequency sampling. This study utilizes 15-minute interval data to mitigate the smoothing effects of daily aggregations and to expose the microstructure of price discovery.

Dataset Characteristics: Relience_15m_full_year.csv

Attribute	Specification
Temporal Range	March 03, 2025 – February 27, 2026
Observation Frequency	15-Minute Intervals
Dimensionality	12,130+ Observations (Rows)
Features	Open, High, Low, Close, Volume
Timezone Reference	UTC +05:30 (IST)

The preprocessing pipeline involves a critical transformation of the datetime index into a ds (datestamp) format to comply with the Facebook Prophet API requirements. While the script initiates an 80/20 Train-Test split logic (Train_Size = int(len(df)*0.8)), the implementation test = df.iloc[Train_Size] in the source code effectively isolates a single-point test instance, while df = df[1230:] indicates a manual slicing of the series for specific regime analysis. To maintain statistical validity, practitioners must account for the potential of look-ahead bias during these transformations. Missing value analysis (df.isna().sum()) confirmed a clean baseline, and descriptive statistics (df.describe()) provided the requisite moments for normalizing the Open-High-Low-Close (OHLC) distribution.

3. Methodology & Model Architecture

Modeling RIL’s price action necessitates a hybrid econometric approach to handle non-stationarity and stochastic noise. The framework utilizes Facebook Prophet for structural time-series decomposition and proposes ARIMA for residual refinement.

A pivotal component of this architecture is the seasonal_decompose function using a Multiplicative model with a period of 249. In the context of the Indian equity market, where one trading day consists of roughly 25 15-minute candles, a period of 249 effectively isolates a 10-day (bi-weekly) micro-cycle. This allows the analyst to separate:

* Trend: The underlying deterministic growth trajectory of RIL.
* Seasonality: Recurring liquidity patterns within the bi-weekly cycle.
* Resid (Noise): The irregular, stochastic component.

By decomposing all four OHLC features, the model isolates structural signals from session-start volatility. The inclusion of the Augmented Dickey-Fuller (ADF) test serves as a diagnostic gateway for stationarity, informing the 'd' parameter for the proposed ARIMA pipeline. The ultimate objective is a Prophet-ARIMA residual sequence: Prophet captures the macro-trend and bi-weekly seasonality, while ARIMA models the remaining auto-correlated residuals to tighten the predictive window.

4. Model Evaluation & Performance Metrics

Rigorous error quantification is paramount for institutional risk management. The framework evaluates predictive accuracy using three primary estimators from sklearn.metrics: Mean Absolute Error (MAE), Mean Squared Error (MSE), and Root Mean Squared Error (RMSE).

From a quantitative perspective, the reliance on MSE must be critiqued for financial time series. MSE’s quadratic penalty on outliers can over-correct for the "fat tails" (leptokurtosis) typically observed during the market opening bell (9:15 IST), where RIL experiences its highest volatility. RMSE is favored for providing an error metric denominated in the stock’s currency (INR), facilitating direct risk-budgeting. The evaluation of the model against the 20% slice (or the specified regime) aims to detect overfitting; however, a senior analyst would further require Walk-Forward Validation to ensure the model’s "confidence floor" remains stable as the data window evolves, avoiding the pitfalls of look-ahead bias inherent in simple splits.

5. Forecasting Results

The resulting forecast synthesizes the historical price action—characterized by the ascent from the ~1,183.55 support floor—into a forward-looking projection. The deterministic trend, derived from Opening_SD.trend, maintains a positive slope, suggesting sustained institutional accumulation throughout the forecasted timeline.

The model generates predicted 'y' values along the 'ds' timeline, accompanied by uncertainty intervals (typically at the 80% and 95% default widths). For the quantitative analyst, these intervals represent more than a margin of error; they function as a proxy for Value at Risk (VaR). A widening of these bands indicates an expansion in the volatility regime, signaling that while the primary directional thesis is bullish, the risk-adjusted returns may be under pressure. The actionable insight derived here is the identification of the "Trend-Seasonality" consensus, allowing for positioning that exploits bi-weekly liquidity cycles while remaining anchored to the year-long appreciation trajectory.

6. Limitations & Risk Factors

Despite its technical depth, this univariate model operates within strict boundaries. It relies exclusively on historical price and volume data, which limits its responsiveness to non-linear shifts in market structure.

Technical Risks & Limitations:

* Exogenous Volatility: The model is "blind" to fundamental catalysts such as corporate earnings, shifts in Gross Refining Margins (GRMs) relevant to RIL’s energy sector, or telecom regulatory changes affecting Jio.
* Residual Heteroscedasticity: Analysis of the "Noise" component suggests that if residuals exhibit time-varying variance (heteroscedasticity), the Prophet trend becomes statistically unreliable, potentially leading to spurious signals.
* Black Swan Events: As a historically driven framework, it cannot account for macro-economic "Black Swan" events or sudden shifts in global crude oil prices, which historically exhibit high correlation with RIL’s price action.

In summary, this model provides a high-precision technical baseline for RIL forecasting. However, for a holistic institutional strategy, these quantitative outputs must be integrated with a fundamental overlay to mitigate the risks inherent in univariate time-series modeling.
