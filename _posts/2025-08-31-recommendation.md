---
layout: post
title: "Product Recommendation Engine"
image: /posts/instacart-recommender.jpg
tags: [Python, Machine Learning, LightGBM, Recommender Systems, Data Science]
---

## Project Purpose  

This project develops a **personalized recommendation engine** for Instacart customers.  
Using **market basket data** (user purchase histories, product catalog, and prior orders), I predict whether a customer will reorder a product in their next shopping session.  

Key steps include:  
- **Data ingestion & exploration** (EDA).  
- **Feature engineering** (user-level, product-level, and user–product interactions).  
- **Modeling** using **LightGBM (Gradient Boosting)**.  
- **Evaluation** with both **classification metrics** and **ranking metrics**.  
- **Interpretability & business insights**.  
- **Next steps** for scaling into production. 
 
## GitHub Repository Link  


The source code for this project can be found at my [GitHub Repository](https://github.com/BaberFaisal/Building-a-Personalized-Product-Recommendation-Engine.git). 

---

## Table of Contents  
 

- [00. Project Overview](#00-project-overview)  
  - [Context](#context)  
  - [Actions](#actions)  
  - [Results ](#results)  
- [01. Data Overview](#01-data-overview)  
- [02. Model Overview](#02-model-overview)  
- [03. Data Preparation](#03-data-preparation)  
- [04. Concepts (Deep Dive)](#04-concepts-deep-dive)  
- [05. Fitting the Model](#05-fitting-the-model)  
- [06. Results & Plot Explanations](#06-results--plot-explanations)  
- [07. Growth & Next Steps](#07-growth--next-steps)  






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
- **Deployment**: Provide a pipeline to generate **personalized reorder suggestions**.  

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
  - Providefeature importances.  

### Why LightGBM?  
- Outperforms simple baselines (popularity, co-occurrence).  
- Handles imbalance better than plain logistic regression.  
- Can be extended to production retrieval + re-ranking pipelines.  

---

## 03. Data Preparation  

### Feature Engineering  


- **User Features**: 
{% highlight python %} 
  - `user_total_orders` — how many orders a user has placed.  
  - `user_avg_days_between_orders` — cadence.  
  - `user_std_days_between_orders` — consistency.  
  - `user_total_days` — shopping lifetime.  
{% endhighlight %}
- **Product Features**: 
{% highlight python %} 
  - `product_total_orders` — popularity.  
  - `product_reorder_rate` — global reorder tendency.  
  - `product_avg_cart_position` — typical cart position.  
{% endhighlight %}
- **User–Product Features**:  
{% highlight python %}
  - `up_order_count` — how many times user bought the product.  
  - `up_avg_cart_position` — where it appears in their cart.  
  - `up_first_order` / `up_last_order`.  
  - `up_reorder_count`. 
{% endhighlight %} 

- **Derived Metrics**:  
{% highlight python %}
  - `up_order_rate` = orders containing product ÷ total orders.  
  - `up_reorder_rate` = times reordered ÷ order_count.  
  - `up_orders_since_last` = recency (orders since last purchase).  
  - `up_days_since_first_order` = time feature (from notebook).  
{% endhighlight %}
---

## 04. Concepts 

### Gradient Boosting 

Gradient Boosting is an ensemble learning method that builds models sequentially, where each new decision tree is trained to correct the residual errors of the previous trees. The underlying idea is that many weak learners, typically shallow trees, can be combined to form a strong predictive model. At each step, gradient descent is used to minimize a loss function, such as log-loss in the case of classification tasks, ensuring that the model improves iteratively as more trees are added.  


### LightGBM  

LightGBM (Light Gradient Boosting Machine) is a modern and efficient implementation of gradient boosting that is designed to handle large-scale datasets and high-dimensional feature spaces. It uses histogram-based algorithms to speed up the training process, significantly reducing computation time compared to traditional gradient boosting approaches. Unlike level-wise growth strategies, LightGBM grows trees leaf-wise, selecting splits with the maximum information gain. This often leads to higher accuracy and deeper insights into the data. Additionally, LightGBM has built-in support for categorical variables, imbalanced datasets, and provides feature importance scores, making it highly effective for recommendation systems and large-scale predictive modeling tasks.  



### ROC – AUC  

The ROC (Receiver Operating Characteristic) curve is a graphical representation of a model’s ability to distinguish between classes across different classification thresholds. It plots the True Positive Rate (Recall) against the False Positive Rate, illustrating the trade-off between correctly identifying positives and incorrectly flagging negatives. This visualization helps in understanding model performance beyond a single threshold, showing how well the model can separate classes across all possible cutoffs.  

The AUC (Area Under the Curve) is a scalar value that summarizes the ROC curve into a single number. It is interpreted as the probability that the model assigns a higher predicted score to a randomly chosen positive instance than to a randomly chosen negative one. An AUC of 0.5 indicates no discriminatory power, equivalent to random guessing, while an AUC of 1.0 represents perfect separation between classes. In practical applications, particularly with imbalanced datasets such as predicting reorders, an AUC above 0.85 is considered a strong indication of model effectiveness.  



---



## 05. Fitting the Model  

- Split into **train/test** with `stratify=y`.  
- Features selected:  
  - `up_order_count`, `up_avg_cart_position`, `user_total_orders`,  
  - `user_avg_days_between_orders`, `product_total_orders`,  
  - `product_reorder_rate`, `up_order_rate`, `up_reorder_rate`,  
  - `up_orders_since_last`, `up_days_since_first_order`.  

- Parameters:  
{% highlight python %}

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

{% endhighlight %}

---

## 06. Results & Plot Explanations  

Below are the plots generated, with detailed explanations.  


---

### Target Distribution  

![Target Distribution](/img/posts/fig_target_distribution.png)  
*Shows the balance of target classes (0 = not reordered, 1 = reordered).*  

**Insight:** Skewed toward 0 → confirms class imbalance problem.  

---

###  Correlation Heatmap  

![Feature Correlation Heatmap](/img/posts/fig_feature_correlation.png)  
*Pairwise correlations among engineered features.*  

**Insight:** Recency and frequency features correlate, but not perfectly → useful complementarity.  

---

### Confusion Matrix  

![Confusion Matrix](/img/posts/fig_confusion_matrix.png)  
*Predicted vs actual counts.*  

**Insight:** Many true negatives, but false negatives show missed reorders.  
**Business view:** Missing reorders harms UX; tune threshold to reduce FN.  

---

### Classification Report  

![Classification Report](/img/posts/fig_classification_report.png)  
*Precision, recall, and F1-score per class.*  

**Insight:** Positive class (reordered) typically lower precision but higher recall.  

---

### ROC Curve  

![ROC Curve](/img/posts/fig_roc_curve.png)  
*True Positive Rate vs False Positive Rate across thresholds.*  

**Insight:** High AUC (>0.85 in runs) → model distinguishes reorders well.  

---


### Feature Importances  

![Feature Importances](/img/posts/fig_feature_importance.png)  
*Gain importance of features in LightGBM.*  

**Insight:** Recency (`up_orders_since_last`) and frequency (`up_order_count`) dominate;  
product reorder rate also strong.  


---

## 07. Growth & Next Steps  

- **Rolling retraining**: update features weekly to capture new habits.  
- **Retrieval + Re-ranking**: scale to 50k+ products efficiently.  
- **Time-aware validation**: mimic production by training on older orders, testing on recent.  
- **Sequence models**: test RNNs or Transformers on order sequences.  
- **Diversification**: ensure recommendations balance staples with exploratory items.  
- **Online A/B Testing**: measure uplift in order size and repeat rate.  
- **Monitoring**: drift detection on feature distributions and reorder probabilities.