# 📈 OptionsTradingStrategyML

## 1. 🌐 Environmental Setup

- **Platform**: Google Colab / Jupyter Notebook  
- **Python Version**: Tested on Python 3.11.13 (default in Google Colab)  
- **GPU/CPU**:  
  - **Recommended**: Use GPU runtime for faster training  
  - *In Colab*: Go to `Runtime > Change runtime type > Select GPU`  
- **Data Location**:  
  [options_data_2023.csv](https://drive.google.com/file/d/1hdItv8l8Qj102oAB75la9XubqYydxbkD/view?usp=sharing)

---

## 2. 📦 Dependencies

- `python` version: **3.11.13**  
- `pandas`: **2.2.2**  
- `numpy`: **2.0.2**  
- `tensorflow`: **2.18.0**  
- `keras`: **3.8.0**  
- `scikit-learn`: **1.6.1**  
- `matplotlib`: **3.10.0**

📁 **Installation:**

```bash
pip install -r requirements.txt
```
```python
!pip install -r requirements.txt
```
(requirements.txt file provided)

---
## 3. Script Usage Examples

### Run in Google Colab

Click the button below to open the notebook in Colab:  
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/YOUR_NOTEBOOK_ID)

---

## 📊 Technical Indicators Used

### 1. 📈 EMA-200 (Exponential Moving Average)
- Tracks the **long-term trend**.
- Applies **greater weight to recent prices**, making it more responsive than the Simple Moving Average (SMA).
- Commonly used as a **trend filter**:
  - **Price above EMA-200** → Bullish bias  
  - **Price below EMA-200** → Bearish bias

-

### 2. ⚡ Relative Strength Index (RSI)
- Measures **momentum** to identify whether an asset is **overbought or oversold**.
- RSI values range between **0 and 100**:
  - **RSI > 50** → Overbought (potential **sell** zone)  
  - **RSI < 50** → Oversold (potential **buy** zone)
- Often used in **mean-reversion strategies**.

-

### 3. 🔄 Moving Average Convergence Divergence (MACD)
- Combines **trend-following** and **momentum** analysis.
- Calculated using:
  - `MACD Line = EMA(12) - EMA(26)`
  - `Signal Line = EMA of MACD Line`
- **Usage**:
  - **Bullish crossover**: MACD Line crosses above Signal Line → **Buy**
  - **Bearish crossover**: MACD Line crosses below Signal Line → **Sell**
- Detects **trend reversals** and **momentum shifts**.

-

### 4. 🌪️ Average True Range (ATR)
- Measures **market volatility**, not direction.
- Higher ATR → More **volatile** market conditions.
- Lower ATR → More **stable** market.
- **Usage**:
  - `ATR > 0.5` → Filter out **low-volatility** periods (ideal for momentum strategies).
  - `ATR < 0.5` → Market may be too **quiet or range-bound**.
 

## 📉 Indicator Roles

- **EMA**: Indicates the **direction of the trend**.
- **RSI**: Shows whether a **price move is overdone** (overbought or oversold).
- **MACD**: Helps **confirm** whether the trend is real.
- **ATR**: Measures **volatility** to help manage **risk and noise**.


---

## 🧠 LSTM Model Overview

- The model captures **time-dependent patterns** and handles **noisy sequences** effectively using an LSTM architecture.
- The target variable is the **closing price**, based on the **past 63 observations** (approximately 3 months of trading days).
- This window is sufficient to **capture short- and medium-term trends and momentum**.


## 🛑 Early Stopping

- **EarlyStopping** is used to automatically stop training when the **validation loss** stops improving.
- Benefits:
  - ✅ Prevents **overfitting**
  - ✅ Saves **training time**
  - ✅ Restores the **best-performing weights** with `restore_best_weights=True`


## ⚙️ Optimizer: RMSprop

- **RMSprop** stands for **Root Mean Square Propagation**.
- It uses an **adaptive learning rate**, making it well-suited for **RNNs and LSTMs**.
- It divides the learning rate by an **exponentially decaying average of squared gradients**.
- Helps keep weight updates **balanced and stable**, especially when gradients are large or noisy.

---

## 🔢 Backtesting Parameters

The confidence score combines technical indicators and ML model output:

| Component           | Weight | Signal Logic                                                                 |
|---------------------|--------|------------------------------------------------------------------------------|
| **EMA + RSI**       | 0.5    | +1: Close > EMA-200 & RSI > 50  <br> -1: Close < EMA-200 & RSI < 50 <br> 0: Mixed |
| **MACD + ATR**      | 0.3    | +1: MACD > 0 & ATR > 0.5 <br> -1: MACD < 0 & ATR > 0.5 <br> 0: ATR too low/mixed |
| **LSTM Prediction** | 0.2    | +1: Predicted Close > Current <br> -1: Predicted Close < Current              |

---

## ⚙️ Why These Weights?

| Component       | Weight | Justification                              |
|------------------|--------|---------------------------------------------|
| EMA + RSI        | 0.5    | Core trend and momentum signal              |
| MACD + ATR       | 0.3    | Confirms strength and volatility            |
| LSTM Prediction  | 0.2    | Adds predictive edge, lower weight due to potential noise |
---

## 📈 Signal Generation

| Confidence Score   | Signal | Options Strategy         |
|--------------------|--------|--------------------------|
| ≥ 0.6              | Buy    | Sell ATM **Put Option**  |
| ≤ -0.6             | Sell   | Sell ATM **Call Option** |
| -0.6 < score < 0.6 | Hold   | No Trade                 |

---

## 📌 Additional Notes

- NaNs in indicators are safely skipped to avoid false signals.
- Expiry mapping, ATM strike selection, and PnL computation are handled in the backtesting engine.

  ---
  ## Result and Interpretation
- Sharpe Ratio: -0.7914946668693227
- Max Drawdown (in %): -59174.095437860095
- Total Return: -52891.749999999054

  ---
  










