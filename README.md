# Customer Churn Prediction System

This project focuses on analyzing customer churn behavior using exploratory data analysis (EDA) to identify key factors influencing customer retention.

## Business Problem

Customer churn is a critical problem for subscription-based businesses, as acquiring new customers is significantly more expensive than retaining existing ones.  
The goal of this project is to analyze customer behavior and build a predictive system that can identify customers who are likely to churn, enabling proactive retention strategies.

## Dataset Description

The dataset used is the Telco Customer Churn dataset, which contains customer demographics, service usage details, billing information, and churn status.

- Number of records: ~7,000
- Target variable: `Churn` (Yes / No)
- Feature types:
  - Numerical: tenure, MonthlyCharges, TotalCharges
  - Categorical: Contract, PaymentMethod, InternetService, etc.

## Exploratory Data Analysis

### Target Variable: Churn

- The dataset shows class imbalance, with a higher proportion of non-churned customers.
- This indicates that evaluation metrics beyond accuracy (e.g., precision, recall, ROC-AUC) will be important in later stages.

### Numerical Features

Key insights from numerical feature analysis:

1. **Tenure** shows a strong inverse relationship with churn. Customers with shorter tenure are significantly more likely to churn, indicating that early-stage customers are at higher risk.
2. **MonthlyCharges** are higher on average for churned customers, suggesting potential price sensitivity or unmet expectations.
3. Customers with low tenure and high monthly charges represent the highest churn risk group.

### Categorical Features

1. **Payment Method** is a strong indicator of churn:
   - Customers using electronic checks exhibit the highest churn rate (~45%).
   - Automatic payment methods (credit card and bank transfer) significantly reduce churn.

2. **Contract Type** strongly affects churn:
   - Month-to-month contracts have a much higher churn rate compared to one-year and two-year contracts.
   - Longer contract durations are associated with improved customer retention.

## Data Quality Observations

- The `TotalCharges` column contains blank string values instead of numeric values.
- These rows correspond to customers with zero tenure who have not yet been billed.
- Dropping these records would remove important new-customer data.
- The issue will be handled by converting `TotalCharges` to numeric and replacing missing values with 0.

## Data Preprocessing & Feature Engineering

After completing exploratory data analysis, the dataset was transformed into a model-ready format using a structured preprocessing pipeline.

The following steps were performed:

- Converted the `TotalCharges` column from string to numeric format. Blank values were identified as customers with zero tenure and were imputed with 0 to preserve business consistency.
- Removed non-predictive identifier columns such as `customerID`.
- Encoded the target variable `Churn` into binary format (0 = No, 1 = Yes).
- Applied one-hot encoding to categorical variables to convert them into numerical features suitable for machine learning models.
- Performed a stratified train–validation split to preserve the churn distribution across datasets and avoid sampling bias.

### Dataset After Preprocessing
- Total records: 7,043
- Number of input features after encoding: 30
- Churn ratio: ~26.5%
- Train set size: 5,634 records
- Validation set size: 1,409 records

### Preprocessing Design Decisions

- One-hot encoding was chosen as categorical variables do not have an inherent ordinal relationship.
- Stratified splitting was used due to moderate class imbalance in the churn variable.
- Feature scaling was intentionally deferred to the modeling stage to prevent data leakage.

## Modeling & Evaluation (Baseline Model)

A Logistic Regression model was trained as a baseline classifier with class-weight balancing to handle class imbalance in the churn dataset.

### Performance Summary
- Accuracy: ~66.5%
- Recall (Churn): ~87%, indicating strong ability to identify customers at risk of churn.
- Precision (Churn): ~43%, reflecting a trade-off where more customers are flagged to minimize missed churners.
- ROC-AUC: ~0.73, demonstrating good ranking capability between churned and non-churned customers.

### Confusion Matrix Insights
- The model correctly identified most churned customers, with a low number of false negatives.
- A higher number of false positives indicates an aggressive churn detection strategy, which is acceptable in retention-focused business scenarios.

### Interpretation
The baseline model prioritizes recall over precision, which is appropriate for churn prediction problems where missing a churner is more costly than contacting a non-churner.

## Model Comparison & Selection

Two models were evaluated for churn prediction: Logistic Regression (baseline) and Random Forest.

### Logistic Regression
- Recall (Churn): ~87%
- Precision (Churn): ~43%
- ROC-AUC: ~0.81
- Behavior: Aggressive churn detection with low false negatives.

### Random Forest
- Recall (Churn): ~50%
- Precision (Churn): ~63%
- ROC-AUC: ~0.83
- Behavior: Conservative predictions with significantly fewer false positives.

### Model Selection Rationale
Logistic Regression is suitable when the cost of missing a churner is high and retention actions are inexpensive.  
Random Forest is preferred when retention actions are costly and higher confidence in churn predictions is required.

In practice, both models can be used together, where Logistic Regression acts as a screening model and Random Forest refines high-confidence churn cases.

The Random Forest model achieved the highest ROC-AUC score while maintaining a better balance between precision and recall, making it suitable for identifying high-risk churn customers.

## Model Explainability
To interpret the Random Forest model and understand the drivers behind churn predictions, model-agnostic explainability techniques were applied.

### Explainability Techniques Used
- **Permutation Feature Importance**: Measures the decrease in model performance when a feature’s values are randomly shuffled.
- **Partial Dependence Plots (PDP)**: Visualize how changes in key numerical features affect churn probability.

### Key Insights from Explainability
- **Tenure** is the strongest predictor of churn. Customers with shorter tenure are significantly more likely to churn, highlighting the importance of early-stage retention.
- Higher **TotalCharges** early in the customer lifecycle indicate dissatisfaction and increase churn risk.
- Customers using **fiber optic internet** show higher churn probability, potentially due to pricing sensitivity or service quality expectations.
- **Long-term contracts (one-year and two-year)** substantially reduce churn likelihood.
- Value-added services such as **online security** and **technical support** lower the probability of churn.
- **Senior citizens** exhibit slightly higher churn risk, suggesting the need for targeted engagement strategies.

These insights translate model predictions into actionable business strategies for customer retention.

## Business Impact

The churn prediction model provides actionable insights that can support business decision-making:

- Enables early identification of high-risk customers, especially those with short tenure.
- Supports targeted retention strategies such as contract incentives and onboarding programs.
- Highlights price-sensitive segments, informing pricing and discount strategies.
- Identifies the value of support services in reducing customer churn.

By aligning model outputs with business actions, the system helps improve customer retention and reduce revenue loss.


## Key Takeaways

- Customer churn is strongly influenced by tenure, pricing, payment method, and contract type.
- Early-stage customers on high-priced plans are at the highest risk of churn.
- Automatic payment methods and long-term contracts significantly reduce churn.
- Model explainability techniques (permutation importance and partial dependence) enable clear identification of key churn drivers.
- Careful handling of data quality issues is essential for building reliable predictive models.


## Future Enhancements

- Perform decision threshold tuning to balance precision and recall based on business cost considerations.
- Deploy the churn prediction model using a REST API (e.g., FastAPI) for real-time inference.
- Monitor model performance and data drift to ensure long-term reliability.
- Explore cost-sensitive learning or additional ensemble methods to further improve churn detection.

