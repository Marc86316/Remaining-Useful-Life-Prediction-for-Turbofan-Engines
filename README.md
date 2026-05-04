# Remaining-Useful-Life-Prediction-for-Turbofan-Engines

## 🛠️ NASA 渦輪引擎預測性維護專案辭典 (Glossary)

<details>
<summary><b>🔍 核心商業邏輯與概念 (Core Concepts)</b></summary>

| 專有名詞 | 原理解釋 | 專案應用場景 |
| :--- | :--- | :--- |
| **Predictive Maintenance (PdM)** | 透過數據預測設備故障，在故障發生前精準進行維修。 | 取代傳統「定時維修」，為航司節省非預期停機成本。 |
| **Remaining Useful Life (RUL)** | 設備在發生嚴重故障前，剩餘可運行的循環次數。 | 專案的 **Target (Y)**，單位為 `Cycles`。 |
| **Piecewise Linear RUL** | 假設健康初期 RUL 為常數，待退化特徵出現後才線性遞減。 | 設定 `Max RUL = 130`，避免模型過度擬合初期的雜訊。 |
| **Data Leakage** | 訓練時不慎引入測試集資訊，導致模型表現虛高。 | 嚴防測試集 Scaler 污染，確保模型具備真實泛化能力。 |

</details>

<details>
<summary><b>🏗️ 預測模型與演算法比較 (Models & Algorithms)</b></summary>

### 傳統機器學習與特徵工程
* **Mutual Information (MI):** 用於計算感測器與 RUL 的非線性相關性，篩選最具影響力的特徵。
* **Random Forest / XGBoost:** 作為 **2D Baseline**，能快速捕捉特徵間的交互作用，適用於非時序特徵。
* **SVR (Support Vector Regression):** 傳統迴歸方法，但在處理本專案的高維大規模數據時，運算成本較高。

### 深度學習架構
* **LSTM (Long Short-Term Memory):** 核心 3D 模型，具備遺忘閘 (Forget Gate)，專門解決長序列退化軌跡捕捉問題。
* **Attention Mechanism:** 讓模型對「故障前夕」的關鍵起降次數分配更高權重，強化預測精準度。
* **GRU (Gated Recurrent Unit):** LSTM 的變體，計算量較小，適合作為輕量化對照組。

</details>

<details>
<summary><b>📊 性能評估指標 (Evaluation Metrics)</b></summary>

我們採用以下指標來評估模型在測試集上的表現：

### 1. RMSE (Root Mean Square Error)
$$RMSE = \sqrt{\frac{1}{n}\sum_{i=1}^{n}(y_i - \hat{y}_i)^2}$$
* **解釋：** 均方根誤差。對「大誤差」極為敏感。
* **應用：** 本專案的首要指標，數值越低代表模型對故障預警越精準。

### 2. MAE (Mean Absolute Error)
$$MAE = \frac{1}{n}\sum_{i=1}^{n}|y_i - \hat{y}_i|$$
* **解釋：** 平均絕對誤差。能直觀反映預測值偏離真實壽命的平均次數。

### 3. MAPE (Mean Absolute Percentage Error)
$$MAPE = \frac{100\%}{n}\sum_{i=1}^{n}|\frac{y_i - \hat{y}_i}{y_i}|$$
* **注意：** 當引擎接近故障（$y_i$ 趨近於 0）時，MAPE 會急遽飆升，解讀時需搭配 RMSE 使用。

</details>
