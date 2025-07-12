# Propensity Modeling Case Study

## Objective
This case study walks through a structured data science pipeline to identify clients most likely to take up one of three financial products:
- Consumer Loans (CL)
- Credit Cards (CC)
- Mutual Funds (MF)

The end goal is to build a targeting strategy that ties directly to **expected revenue**. This ensures that product recommendations are both data-informed and commercially impactful.

---

## Data Sources & Integration
We started by joining four client-level datasets:
- **Soc_Dem**: Demographics like age, gender, and tenure
- **Products_ActBalance**: Product holdings and account balances
- **Inflow_Outflow**: Transaction volumes and frequencies
- **Sales_Revenues**: Ground truth—actual product purchases and associated revenue

Categorical variables like `Sex` were label-encoded. Missing values were filled with zero to reflect the absence of certain transactions or product holdings. 

---

## Modeling Strategy
Three **XGBoost** classifiers were trained—one per product—on an 80/20 stratified split. Targets were binary indicators for whether a client purchased CL, CC, or MF. Key evaluation metrics included both **ranking-based** and **threshold-based** perspectives:

- **ROC-AUC (Receiver Operating Characteristic - Area Under the Curve)**: Measures the model's ability to distinguish between buyers and non-buyers across all thresholds.
    - CL: AUC = 0.613  
    - CC: AUC = 0.6095  
    - MF: AUC = 0.5824
    - Interpretation: While these AUC values are modest, they still show the model performs better than random in prioritizing potential buyers. However, there is room to improve ranking accuracy via feature enrichment or modeling techniques.

- **Classification Report**: Reflects fixed-threshold performance (at 0.5):
    - All three models achieved high **precision** (≥ 0.94), indicating strong confidence when predicting a buyer.
    - **Recall** was lower (~0.81–0.82), meaning the model misses some actual buyers.
    - **F1-scores** (≈ 0.88–0.89) reflect good balance overall.
    - Interpretation: The models are calibrated to make high-confidence predictions, but may need threshold tuning or rebalancing if minimizing false negatives is key.


> **Model Performance**
> - CL: AUC = 0.613  
> - CC: AUC = 0.6095  
> - MF: AUC = 0.5824

While the AUC scores are modest, the ROC curves still offer value in visualizing ranking behavior and guiding product targeting under uncertainty.

---

## Explainability via SHAP
To ensure interpretability, we used SHAP:
- **Summary plots** show how feature values affect predictions directionally

**Top Takeaways:**
- Clients with higher tenure and more active credit usage are likely credit card candidates
- Age and strong savings behavior align with mutual fund interest
- Overdraft activity and inflow-outflow gaps hint at consumer loan potential

---

## Revenue-Driven Targeting Strategy
1. Predict propensities for CL, CC, and MF
2. Multiply each by its respective historical revenue
3. Select the product with the **highest revenue-weighted score** per client

The result: a prioritized list of clients and the product they're most likely to buy *and* drive value from.

> Final output: `targeted_client_list.csv` with Client ID, propensities, best offer, and expected revenue

Top clients per product were also surfaced using color-coded visual tables (green gradient) for quick targeting.

---

## Conclusion
This pipeline delivers a practical, explainable, and revenue-focused engine for personalized product targeting. 