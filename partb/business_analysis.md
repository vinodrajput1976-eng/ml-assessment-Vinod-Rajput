# B1 (a): Problem Formulation

## Target Variable
The main goal is to predict `items_sold`, which represents how many items are sold in a store during a particular month.

## Input Features
To make this prediction, we can use the following inputs:
- Store details: store_size, location_type, store_id  
- Promotion type: promotion_type (discount, BOGO, free gift, etc.)  
- Time-related factors: month, weekend, festival  
- Competition: competition_density  
- Customer-related factors: footfall, demographics  

## Type of Machine Learning Problem
This is a Regression Problem.

## Justification
The objective is to predict the number of items sold, which is a continuous numerical value. Since the output is numeric and not categorical, regression is the appropriate machine learning approach.

Using this model, the business can estimate sales for different promotions and select the most effective promotion for each store.

## Simple Understanding
We are predicting “how many items will be sold”, so this is a regression problem.

## B1 (b): Target Variable Justification

## Why items_sold is a Better Target than Revenue

Using `items_sold` (sales volume) is more reliable than total sales revenue because revenue can be influenced by multiple external factors such as pricing, discounts, and product mix.

For example, a promotion may increase the number of items sold but reduce revenue due to heavy discounts. In such cases, revenue does not accurately reflect the true effectiveness of the promotion. On the other hand, `items_sold` directly measures customer response and demand, making it a more stable and meaningful indicator for evaluating promotion performance.

## Broader Principle

This illustrates an important principle in machine learning:  
the target variable should closely align with the actual business objective and should not be heavily affected by external or confounding factors.

Choosing the right target variable ensures that the model learns meaningful patterns and provides reliable, actionable insights.

# B1 (c): Alternative Modelling Strategy

Instead of using a single global model for all stores, a better approach is to use **segmented or grouped models** based on store characteristics such as location type (urban, semi-urban, rural).

Stores in different locations often have different customer behavior, competition levels, and purchasing patterns. Because of this, the same promotion may have varying effects across stores.

By building separate models for each segment (or including interaction effects between store type and promotion), the model can better capture these differences and provide more accurate predictions.

## Justification
This approach improves model performance by accounting for data heterogeneity. It allows the model to learn location-specific patterns rather than forcing a single model to generalize across very different store environments.

# B2: Data and EDA Strategy

## (a) Data Joining and Dataset Design

The data is spread across four tables: transactions, store attributes, promotion details, and calendar.

### How to Join the Tables
We start with the **transactions table** as the base dataset.  
- Store attributes are joined using `store_id`  
- Promotion details are joined using `promotion_type`  
- Calendar data is joined using `transaction_date`  

This way, each record gets complete information about the store, promotion, and time context.

### Grain of Final Dataset
The final dataset should be:
**One row per store per day**

This means each row represents the total activity of a store on a given date.

### Aggregations Before Modelling
Before building the model, we summarize the data:
- Total `items_sold` per store per day  
- Total or average `footfall`  
- Promotion applied on that day  
- Time-based features like month and weekday  

These steps convert raw transaction data into a clean format suitable for modelling.

## (b) Exploratory Data Analysis (EDA)

Before building any model, it is important to understand the data.

### 1. Distribution of Sales
We use a histogram to see how `items_sold` is distributed.  
This helps identify skewness and outliers.

### 2. Sales by Promotion Type
A bar chart can show how different promotions affect sales.  
This helps understand which promotions are more effective.

### 3. Sales Over Time
A line chart helps identify trends, seasonality, and festival impact.  
This is useful for creating time-based features.

### 4. Relationship with Numerical Features
Scatter plots (e.g., sales vs footfall or competition) help us see relationships.  
This helps in selecting important features.

### 5. Correlation Analysis (Optional)
A heatmap helps identify strongly related variables and avoid redundancy.


## (c) Handling Imbalance in Promotion Data

Since most transactions (around 80%) have no promotion, the dataset is imbalanced.

### Impact on Model
The model may focus too much on “no promotion” cases and may not learn how promotions actually affect sales.

### How to Handle It
- Balance the data using sampling techniques  
- Add features indicating whether a promotion is applied  
- Use models that can handle imbalance (like tree-based models)  

This helps the model better understand the true impact of promotions.

## Simple Understanding
We first combine all data, understand patterns using EDA, and handle imbalance so that the model learns correctly.

# B3: Model Evaluation and Deployment

## (a) Train-Test Split and Evaluation

Since the data is time-based (monthly data over three years), we should use a **temporal split** instead of a random split.

### Train-Test Split
- Use the first ~80% of the timeline (earlier months) as the training set  
- Use the most recent ~20% (latest months) as the test set  

This ensures the model learns from past data and is tested on future data.

### Why Not Random Split?
A random split mixes past and future data, which can lead to **data leakage**.  
This gives overly optimistic results because the model indirectly sees future patterns during training.

### Evaluation Metrics

- **RMSE (Root Mean Squared Error)**  
  Measures overall prediction error, giving more importance to large mistakes.  
 Useful when large prediction errors are costly for the business.

- **MAE (Mean Absolute Error)**  
  Measures average error in predictions.  
 Easy to interpret (e.g., average error in items sold).

- **R² Score**  
  Shows how well the model explains variation in sales.  
 Helps understand overall model fit.

### Interpretation
Lower RMSE and MAE indicate better predictions.  
In this context, they tell us how accurately we can estimate items sold under different promotions.

---

## (b) Explaining Different Recommendations Using Feature Importance

The model recommends different promotions for the same store in different months because input conditions change over time.

### How to Investigate
- Check **feature importance** from the model  
- Analyze key features such as:
  - Month or season  
  - Festival or holiday effects  
  - Customer footfall  
  - Competition density  

### Explanation
For example:
- In **December**, higher demand (due to holidays/festivals) may make Loyalty Points more effective  
- In **March**, lower demand may require stronger incentives like Flat Discounts  

### Communication to Marketing Team
Explain in simple terms:
“The model considers seasonal trends and customer behavior. Since conditions differ across months, the most effective promotion also changes.”

This builds trust and makes the model’s decisions easier to understand.


## (c) Deployment and Monitoring

### Model Saving
After training, save the model using tools like `joblib` or `pickle`.

### Monthly Prediction Process
1. Collect new monthly data for all stores  
2. Apply the same preprocessing steps (feature engineering, encoding, scaling)  
3. Pass the data into the saved model  
4. Generate predictions for each promotion  
5. Recommend the promotion with the highest predicted sales  

### Automation
This process can be automated to run at the start of every month.

### Monitoring Model Performance
To ensure the model remains reliable:
- Track prediction errors over time (RMSE, MAE)  
- Compare predicted vs actual sales  
- Monitor data drift (changes in input patterns)  

### When to Retrain
If model performance worsens or data patterns change significantly, retrain the model using recent data.


## Simple Understanding
Train on past data, test on future data, explain model decisions using important features, and continuously monitor performance after deployment.