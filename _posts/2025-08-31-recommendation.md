---
layout: post
title: "Instacart Market Basket Analysis — Personalized Product Recommendation Engine"
image: /posts/instacart-recommender.jpg
tags: [Python, Machine Learning, LightGBM, Recommender Systems, Data Science]
---

## Project Purpose  

This project develops a **personalized recommendation engine** for Instacart customers.  
Using **market basket data** (user purchase histories, product catalog, and prior orders), we predict whether a customer will reorder a product in their next shopping session.  

Key steps include:  
- **Data ingestion & exploration** (EDA).  
- **Feature engineering** (user-level, product-level, and user–product interactions).  
- **Modeling** using **LightGBM (Gradient Boosting)**.  
- **Evaluation** with both **classification metrics** and **ranking metrics**.  
- **Interpretability & business insights**.  
- **Next steps** for scaling into production.  

---

## Table of Contents  

- 00. Project Overview  
  - Context  
  - Actions  
  - Results  
- 01. Data Overview  
- 02. Model Overview  
- 03. Data Preparation  
- 04. Concepts (Deep Dive)  
- 05. Fitting the Model  
- 06. Results & Plot Explanations  
- 07. Growth & Next Steps  
- Appendix: Suggested Figures for GitHub  

---

## 00. Project Overview  

### Context  

- Dataset: Instacart market basket dataset (orders, products, prior purchases).  
- Problem: Predict **reorders** → “Will a user reorder this product in their next order?”  
- Challenge: Thousands of products, sparse purchase patterns, and class imbalance.  

### Actions  

1. Extracted and loaded Instacart relational tables.  
2. Engineered **multi-level features**:  
   - User (shopping frequency).  
   - Product (popularity, reorder rate).  
   - User–Product (affinity, recency, frequency).  
3. Constructed **supervised dataset** with binary labels (`target = reordered`).  
4. Trained a **LightGBM** classifier with early stopping.  
5. Evaluated using **classification (AUC, confusion matrix)** and **ranking metrics (Precision@K, Recall@K, MAP@K)**.  
6. Produced **Top-N recommendation lists** for users.  

### Results (Executive Summary)  

- **Dataset size**: ~1.3M user–product pairs.  
- **Features**: 10 engineered (recency, frequency, reorder rate, cart position, etc.).  
- **Model**: LightGBM with tuned hyperparameters.  
- **Performance**: High ROC–AUC (>0.85 in the notebook run).  
- **Key insight**:  
  - PC1 equivalent → strong recency & frequency signals drive predictions.  
  - Top-10 recommended lists capture most of the actual reordered items.  
- **Deployment**: Provides a pipeline to generate **personalized reorder suggestions**.  

---

## 01. Data Overview  

- **Files used**:  
  - `orders.csv` — User orders (order_id, user_id, order_number, etc.).  
  - `products.csv` — Product catalog with product/aisle/department.  
  - `order_products__prior.csv` — Historical orders (training history).  
  - `order_products__train.csv` — Orders for supervised training (with labels).  
  - `aisles.csv`, `departments.csv` — Taxonomy files.  

- **Entity structure**:  
  - User → Orders → Products.  
  - Labels come from `reordered` in the train set.  

- **Exploratory findings**:  
  - Many products are **reordered frequently** (e.g., milk, eggs, bananas).  
  - Some products are rarely reordered (cold-start items).  
  - Target distribution is **imbalanced**: far more `0` (not reordered) than `1`.  

---

## 02. Model Overview  

### Problem framing  
- **Binary classification per (user, product)**: predict `target ∈ {0,1}`.  
- Recommendation lists are created by **ranking predicted probabilities**.  

### Algorithm chosen  
- **LightGBM (Gradient Boosted Trees)**:  
  - Efficient and scalable.  
  - Handles non-linear feature interactions.  
  - Robust with heterogeneous features.  
  - Provides feature importances.  

### Why LightGBM?  
- Outperforms simple baselines (popularity, co-occurrence).  
- Handles imbalance better than plain logistic regression.  
- Can be extended to production retrieval + re-ranking pipelines.  

---

## 03. Data Preparation  

### Feature Engineering  

- **User Features**:  
  - `user_total_orders` — how many orders a user has placed.  
  - `user_avg_days_between_orders` — cadence.  
  - `user_std_days_between_orders` — consistency.  
  - `user_total_days` — shopping lifetime.  

- **Product Features**:  
  - `product_total_orders` — popularity.  
  - `product_reorder_rate` — global reorder tendency.  
  - `product_avg_cart_position` — typical cart position.  

- **User–Product Features**:  
  - `up_order_count` — how many times user bought the product.  
  - `up_avg_cart_position` — where it appears in their cart.  
  - `up_first_order` / `up_last_order`.  
  - `up_reorder_count`.  

- **Derived Metrics**:  
  - `up_order_rate` = orders containing product ÷ total orders.  
  - `up_reorder_rate` = times reordered ÷ order_count.  
  - `up_orders_since_last` = recency (orders since last purchase).  
  - `up_days_since_first_order` = time feature (from notebook).  

---

## 04. Concepts (Deep Dive)  

### Market Basket Recommendation  
- Goal: anticipate next basket from past behavior.  
- Strong drivers: **recency, frequency, product popularity**.  

### Class Imbalance  
- More “not reordered” than “reordered.”  
- Solutions: class-weight adjustment, threshold tuning, ranking metrics.  

### Gradient Boosting & LightGBM  
- Builds trees sequentially to reduce error.  
- LightGBM uses leaf-wise growth with depth limits.  
- Hyperparameters: `num_leaves`, `max_depth`, `feature_fraction`, `bagging_fraction`.  

### Evaluation Metrics  
- **Classification**: Accuracy, Precision, Recall, F1, ROC–AUC.  
- **Ranking**: Precision@K, Recall@K, MAP@K.  
- **Business KPIs**: Coverage, diversity, uplift in reorder rate.  

---

## 05. Fitting the Model  

- Split into **train/test** with `stratify=y`.  
- Features selected:  
  - `up_order_count`, `up_avg_cart_position`, `user_total_orders`,  
  - `user_avg_days_between_orders`, `product_total_orders`,  
  - `product_reorder_rate`, `up_order_rate`, `up_reorder_rate`,  
  - `up_orders_since_last`, `up_days_since_first_order`.  

- Parameters:  
  ```python
  {
    'objective': 'binary',
    'metric': ['auc', 'binary_logloss'],
    'boosting_type': 'gbdt',
    'learning_rate': 0.1,
    'num_leaves': 31,
    'max_depth': 7,
    'feature_fraction': 0.8,
    'bagging_fraction': 0.8,
    'bagging_freq': 5,
    'random_state': 42
  }



---

## 06. Results & Plot Explanations  

Below are the plots generated in the notebook, with detailed explanations.  
Upload each plot as a `.jpg` file into `img/posts/` on GitHub and link them here.  

---

### A — Target Distribution  

![Target Distribution](/img/posts/fig_target_distribution.png)  
*Shows the balance of target classes (0 = not reordered, 1 = reordered).*  

**Insight:** Skewed toward 0 → confirms class imbalance problem.  

---

### B — Correlation Heatmap  

![Feature Correlation Heatmap](/img/posts/fig_feature_correlation.png)  
*Pairwise correlations among engineered features.*  

**Insight:** Recency and frequency features correlate, but not perfectly → useful complementarity.  

---

### C — Confusion Matrix  

![Confusion Matrix](/img/posts/fig_confusion_matrix.png)  
*Predicted vs actual counts.*  

**Insight:** Many true negatives, but false negatives show missed reorders.  
**Business view:** Missing reorders harms UX; tune threshold to reduce FN.  

---

### D — Classification Report  

![Classification Report](/img/posts/fig_classification_report.png)  
*Precision, recall, and F1-score per class.*  

**Insight:** Positive class (reordered) typically lower precision but higher recall.  

---

### E — ROC Curve  

![ROC Curve](/img/posts/fig_roc_curve.png)  
*True Positive Rate vs False Positive Rate across thresholds.*  

**Insight:** High AUC (>0.85 in runs) → model distinguishes reorders well.  

---

### F — Precision@K  

![Precision at K](/img/posts/fig_precision_at_k.png)  
*Fraction of correct recommendations in the Top-K list.*  

**Insight:** Precision falls as K grows; at K=10, still strong.  

---

### G — Recall@K  

![Recall at K](/img/posts/fig_recall_at_k.png)  
*Fraction of actual reorders captured in the Top-K list.*  

**Insight:** Recall improves with K; most staples covered by Top-15.  

---

### H — MAP@K  

![MAP at K](/img/posts/fig_map_at_k.png)  
*Ranking quality (higher when correct items appear earlier).*  

**Insight:** Good MAP indicates effective ordering, not just set membership.  

---

### I — Feature Importances  

![Feature Importances](/img/posts/fig_feature_importance.png)  
*Gain importance of features in LightGBM.*  

**Insight:** Recency (`up_orders_since_last`) and frequency (`up_order_count`) dominate;  
product reorder rate also strong.  

---

### J — Example User Recommendations  

![Example User Recommendations](/img/posts/fig_user_recommendations.png)  
*Top-10 predicted reorders with probabilities.*  

**Insight:** Items include both high-frequency staples and recent favorites.  

---

## 07. Growth & Next Steps  

- **Rolling retraining**: update features weekly to capture new habits.  
- **Retrieval + Re-ranking**: scale to 50k+ products efficiently.  
- **Time-aware validation**: mimic production by training on older orders, testing on recent.  
- **Sequence models**: test RNNs or Transformers on order sequences.  
- **Diversification**: ensure recommendations balance staples with exploratory items.  
- **Online A/B Testing**: measure uplift in order size and repeat rate.  

- **Monitoring**: drift detection on feature distributions and reorder probabilities.
