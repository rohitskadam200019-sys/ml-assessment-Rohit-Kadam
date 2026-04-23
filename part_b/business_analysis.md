# B1. Problem Formulation

## (a) Machine Learning Formulation

This problem can be formulated as a **supervised learning regression problem**.

- **Target Variable:**  
  items_sold (number of items sold per store per month)

- **Input Features (Independent Variables):**  
  - Store attributes: store_size, location_type  
  - Promotion details: promotion_type  
  - Temporal features: month, year, is_weekend, is_festival  
  - Market factors: competition_density  
  - Customer behavior: footfall, past sales (if available)

- **Type of ML Problem:**  
  Regression, because the target variable (items_sold) is continuous.

**Justification:**  
The objective is to predict a numerical value (sales volume), not classify categories. Hence regression is the correct formulation.

---

## (b) Why Items Sold is Better than Revenue

Using **items_sold** is more reliable than total revenue because:

- Revenue is influenced by **price variations, discounts, and promotion types**
- Different promotions (e.g., BOGO vs discount) distort revenue comparison
- Items sold directly reflects **customer response and demand**

**Broader Principle:**  
The target variable should align closely with the **actual business objective**.  
In this case, the goal is to maximize **sales volume**, not monetary value distorted by pricing strategies.

---

## (c) Alternative Modelling Strategy

Instead of a single global model, a better approach is:

- **Segmented or hierarchical modeling**
  - Separate models for urban, semi-urban, and rural stores  
  OR  
  - Include store-specific features (store_id as encoded variable)

**Justification:**  
Customer behavior and promotion effectiveness vary significantly by location.  
A single model may average out patterns and reduce accuracy, while segmented models capture localized behavior more effectively.

---

# B2. Data and EDA Strategy

## (a) Data Joining and Dataset Design

The four tables should be joined as follows:

- **transactions** → main table  
- Join with **store attributes** using store_id  
- Join with **promotion details** using promotion_id or promotion_type  
- Join with **calendar table** using transaction_date  

**Final Dataset Grain:**  
One row = one store for one month

**Aggregations:**
- Total items_sold per store per month  
- Average basket size  
- Total number of transactions  
- Promotion applied that month  
- Monthly averages for competition_density

---

## (b) Exploratory Data Analysis (EDA)

Before modeling, the following analyses should be performed:

1. **Sales Distribution Plot**
   - Understand distribution of items_sold
   - Detect skewness or outliers

2. **Promotion vs Sales Comparison (Bar Chart)**
   - Compare average sales across promotion types
   - Identify which promotions perform better

3. **Time Series Trend**
   - Plot monthly sales over time
   - Detect seasonality and trends

4. **Correlation Heatmap**
   - Identify relationships between variables
   - Helps in feature selection

**Impact on Modeling:**
- Detect important features
- Identify need for transformations
- Help engineer new features (seasonality, lag features)

---

## (c) Handling Promotion Imbalance

Since 80% of transactions have no promotion:

**Problems:**
- Model may become biased toward "no promotion"
- Poor learning of promotion impact

**Solutions:**
- Resampling (oversample promotion cases)
- Use weighted models
- Feature engineering (binary flag for promotion presence)
- Evaluate model separately on promotion vs non-promotion cases

---

# B3. Model Evaluation and Deployment

## (a) Train-Test Split and Metrics

**Train-Test Strategy:**
- Use **time-based split**
- Train on first ~2.5 years
- Test on last ~6 months

**Why not random split?**
- It causes **data leakage**
- Future data may influence past predictions

**Evaluation Metrics:**
- **RMSE (Root Mean Squared Error):** penalizes large errors  
- **MAE (Mean Absolute Error):** average prediction error  

**Interpretation:**
- Lower RMSE → fewer large mistakes  
- Lower MAE → better overall accuracy  

---

## (b) Explaining Different Promotion Recommendations

Different recommendations occur due to:

- Seasonal effects (December vs March)
- Customer behavior changes
- Festival periods or demand spikes

**Using Feature Importance:**
- Identify which features influenced predictions most
- Example:
  - December → high impact of is_festival
  - March → low demand, discount works better

**Communication:**
Explain to marketing team using:
- Feature importance charts
- Simple explanation of seasonal trends
- Clear business reasoning (not technical jargon)

---

## (c) Deployment Strategy

**Step 1: Save Model**
- Use joblib or pickle to save trained model

**Step 2: Monthly Prediction Pipeline**
- Collect new data for each store
- Apply same preprocessing steps
- Input into saved model
- Generate promotion recommendations

**Step 3: Monitoring**
Track:
- Prediction error over time
- Actual vs predicted sales
- Sudden drops in performance

**Retraining Trigger:**
- If performance degrades significantly
- If new trends or seasonality changes appear

**Goal:**
Maintain consistent and reliable promotion recommendations without retraining every month.
