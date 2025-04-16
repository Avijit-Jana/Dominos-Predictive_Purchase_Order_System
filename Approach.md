<h1 align="center">Approach to this Project</h1>

<h2>Table of Contents</h2>

- [Data Preprocessing](#data-preprocessing)
- [EDA\_\&\_Visualization](#eda__visualization)
- [Model Training \& Evaluation](#model-training--evaluation)
 
## Data Preprocessing
  - **Load Data:** Import Ingredients and Sales data using Pandas.
  - **Data Types:** Change datatypes of categorical variables.
  - **Time Extraction:** Derive day, month, hour, etc. from the `order_time` column.
  - **Deduplication:** Drop duplicate values.
  - **Explode & Merge:** Explode the `ingredients` values in the sales table and merge with the Ingredients table.
  - **Missing Values:** Fill missing data using ffill, mean, and interpolation.
  - **Outlier Removal:** Eliminate outliers via Z-score normalization.
  - **Encoding:** Encode categorical variables using Label Encoding.

## EDA_&_Visualization
**1. Loading Preprocessed Data**  
  - 🔍 **Data Ingestion:** Reads the preprocessed CSV file (`processed_data.csv`) into a Pandas DataFrame.  
  - 🕒 **Datetime Conversion:** Merges timestamp components (Year, Month, Day, etc.) into a single, well-formatted datetime column.  
  - 📈 **Indexing & Sorting:** Sets the datetime column as the index and sorts the DataFrame chronologically.

**2. Exploratory Data Analysis (EDA)**  
  - 📊 **Data Overview:** Presents the dataset’s shape, info, and statistical summary to understand its structure and content.  
  - 🛠 **Feature Engineering:** Enhances the dataset by adding features like `day_of_week` and `is_weekend` for deeper analysis.

**3. Visualization & Trend Analysis**  
  - 📉 **Daily Sales Trends:** Utilizes line plots to capture overall sales patterns over time.  
  - 🌦 **Seasonality Analysis:**  
    - **Monthly Trends:** Examines fluctuations in sales on a monthly basis.  
    - **Day-of-Week Trends:** Investigates variations in sales across different days of the week.  
  - ⏰ **Weekday vs. Weekend Analysis:** Compares sales performance between weekdays and weekends.  
  - 📦 **Category-Wise Sales:** Analyzes sales distribution across various ingredient categories.  
  - 🏆 **Top Ingredients by Quantity:** Identifies and highlights the most-used ingredients based on their total quantity.

**4. Encoding Categorical Values**  
  - 🔢 **Data Transformation:** Converts categorical variables into numerical formats, preparing the data for further modeling and analysis.

---
## Model Training & Evaluation

**1. Data Loading and Preprocessing**

  - Loaded data using `pandas`.
  - Removed an unnecessary column (`Unnamed: 0`) often generated during CSV exports.
  - Converted the `date` column into `datetime` format to support time series operations.
  - Suppressing warnings improves readability of output, especially useful in exploratory stages.

**2. Checking Stationarity**

  - Applied the ADF test from `statsmodels` on the `quantity` time series.
  - The Augmented Dickey-Fuller (ADF) test is a statistical test that determines if a time series is stationary.
  - **Conclusion:**
    - The ADF statistic is significantly below the critical threshold.
    - The extremely low p-value confirms that we can reject the null hypothesis of non-stationarity.
    - Hence, the data is **stationary** and suitable for time series modeling.

**3. Model Building & Selection**

**Evaluation Metric(MAPE):** For consistency in comparing models, the Mean Absolute Percentage Error (MAPE) is used as the primary performance metric. Each model’s forecast is evaluated on how closely it predicts the actual quantities, with lower MAPE values indicating better accuracy.

A suite of models has been developed and evaluated to choose the best forecasting approach. The overall evaluation metric is the Mean Absolute Percentage Error (MAPE). Here’s a breakdown of the models:

  **a) ARIMA Model**

  - **Library and Rationale:**  Utilizes the ARIMA model from `statsmodels` and compares different combinations of AR (p), differencing (d), and MA (q) parameters.
    
  - **Model Selection:** Iterates over a grid of parameters using AIC as a selection metric—choosing the parameters that lead to the lowest AIC.

  **b) SARIMA Model**

  - **Incorporating Seasonality:** SARIMA extends ARIMA by including seasonal terms (seasonal order parameters) to capture periodic patterns typically seen in weekly or seasonal data.
    
  - **Model Fit and Forecast:** The SARIMAX formulation is used for fitting, and forecast generation is handled using its out-of-sample prediction capabilities.

  **c) Prophet Model**

  - **Model Setup:** The Prophet forecasting tool is used after transforming data into Prophet’s expected tidy format (with columns `ds` for date and `y` for quantity).
    
  - **Key Features:** Incorporates weekly seasonality by default and is especially useful for data with multiple seasonalities or trend changes.

  **d) LSTM Model**

  - **Deep Learning Approach:** A sequential LSTM (Long Short-Term Memory) neural network is built using TensorFlow/Keras. This approach is particularly useful when non-linear relationships are suspected in the time series data.
    
  - **Data Preparation Specifics:** Time series data are scaled (using MinMaxScaler) and structured into input sequences (with a set look-back period) to capture temporal dependencies.
    
  - **Training and Evaluation:** After training over a number of epochs, the model makes predictions that are then inverse transformed to the original scale. MAPE is used as the evaluation metric.

  **e) XGBoost Model**

  - **Gradient Boosted Trees:** Uses the XGBoost framework, which is effective for regression tasks. Data is aggregated by week and relevant features (like holiday flags) are included.
    
  - **Custom Evaluation:** A custom MAPE evaluation function is defined to assess performance during training.
    
  - **Model Training:** The model leverages early stopping and tuned hyperparameters (such as learning rate and max_depth) to optimize performance.

  **4. Visualization and Reporting**

  - **Visual Analysis:** Each model’s forecast is plotted against the actual test data using `matplotlib` and `seaborn`.  
    - Consistent styling, markers, and labeling are applied to enhance clarity.
    
  - **Interpreting Visuals:** These plots support the numerical MAPE evaluations and help visualize where forecasts may deviate from actual trends.

  **5. Predict Next Week Sales🚀**

  - **Iterate over Each Pizza DataFrame:** The loop processes each DataFrame stored in dfs_by_pizza.
  - **Data Preparation:** 
    - The data is sorted by `week_number` for consistency.
    - The `holiday` column is converted to an integer if it is boolean (so that the XGBoost model can work correctly).
    - The features (`week_number` and `holiday`) and target (`quantity`) are extracted.
  - **Model Training:**
      - The data is converted to an XGBoost `DMatrix`.
      - The model is trained on the entire historical data (you could perform a train-test split or cross-validation if needed).
  - **Prediction:**
      - The next week is defined as one week after the maximum week in your data.
      - A new feature vector is created for the next week (assuming non-holiday with a 0 flag, modify as necessary).
      - The model predicts the next week's sales, and the result is stored in a dictionary keyed by `pizza_name_id`.
  - **Output:** Finally, the script prints the predicted sales for the next week for each pizza type.
