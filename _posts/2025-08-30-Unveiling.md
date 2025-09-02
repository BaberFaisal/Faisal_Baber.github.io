---
layout: post
title: "Unveiling Hidden Risk Factors in Global Markets using PCA"
image: /posts/pca-market.jpg
tags: [Python, Finance, PCA, Statistics, Data Science]
---

## Project Purpose  

The purpose of this project is to analyze **sector-level returns in global markets** using **Principal Component Analysis (PCA)**.  
The goal is to identify hidden risk factors, reduce dimensionality, and provide actionable insights into **systematic shocks** and **sector rotations** that drive asset price movements.  

I use PCA to:  
- Extract **common market factors**.  
- Quantify **variance explained** by leading components.  
- Visualize **loadings, scores, and rotations**.  
- Reconstruct sector returns with limited factors.  
- Suggest portfolio risk management strategies.  

---

## Table of Contents  

- Project Overview  
  - Context  
  - Actions  
  - Results & Discussion  
- Data Overview  
- Core Concepts in PCA Market Risk Analysis  
- Data Preparation  
- Fitting PCA  
- Analysis of Explained Variance  
- Plot Interpretations  
- Growth & Next Steps  

---

## 00. Project Overview  

### Context  
- Dataset: **Daily returns of 10 U.S. sector ETFs** (2000+ trading days).  
- Objective: Identify **latent factors** driving returns and risks.  
- Challenge: Sector returns are highly correlated, obscuring independent drivers.  

### Actions  
- Collected and preprocessed ETF return data.  
- Standardized data (zero mean, unit variance).  
- Applied **PCA** to decompose correlated returns into orthogonal components.  
- Interpreted loadings, explained variance, and factor scores.  
- Visualized results with scree plots, biplots, time series, and reconstructions.  

### Results & Discussion  
- **PC1** explains ~65% of variance → broad **market factor**.  
- **PC2** explains ~15% → **sector rotation factor** (cyclical vs defensive).  
- **PC3–PC4** explain niche effects (e.g., energy, monetary shocks).  
- Just **3 PCs explain ~85% of total variance**.  
- Reconstruction of sector returns using first 3 PCs is highly accurate.  

**Conclusion:** Global sector markets can be reduced to a few interpretable drivers, enabling more effective **risk management and portfolio design**.  

---

![Scree Plot of Variance Explained](/img/posts/pca_scree.png)  
*Scree plot showing proportion of variance explained by each principal component.*  

---

## 01. Data Overview  

- **Assets:** 10 major sector ETFs (Tech, Energy, Financials, Health, Staples, Utilities, etc.).  
- **Period:** Multi-year daily returns.  
- **Shape:** ~2,000 × 10 matrix (time × sectors).  
- **Observations:**  
  - Strong correlations across sectors.  
  - Market crises (2008, 2020) create synchronized drops.  

---

## 02. Core Concepts in PCA Market Risk Analysis

## 1. Principal Component Analysis (PCA)
A statistical technique that reduces the dimensionality of a dataset by transforming correlated variables into a smaller set of uncorrelated variables (principal components). Each component captures a decreasing share of total variance.



## 2. Principal Components (PCs)
New variables created by PCA, formed as linear combinations of the original data.  
- **PC1** explains the largest amount of variance.  
- **PC2** explains the next largest, orthogonal to PC1.  
- Subsequent PCs explain progressively smaller amounts.



## 3. Variance Explained
The proportion of total data variability accounted for by a principal component. High variance explained means the component captures strong common patterns in the data.



## 4. Scree Plot
A graph of the variance explained by each principal component. It helps determine how many components are sufficient to summarize the dataset.



## 5. Loadings
The coefficients (weights) that describe how much each original variable contributes to a principal component. High absolute values indicate stronger influence on that component.



## 6. Scores
The transformed values of the data expressed in the new principal component space. They show how each observation projects onto the components.



## 7. Biplot
A two-dimensional plot showing both the scores (observations) and loadings (variables) in the same space. It helps visualize relationships between variables and components.



## 8. Correlation Circle (Loading Plot)
A graphical representation of variable correlations with the first two principal components. Variables closer to the unit circle are better represented; opposing directions suggest negative correlation.



## 9. Factor Rotation
A transformation of principal components (e.g., varimax rotation) to make loadings easier to interpret. Rotated factors often align more clearly with economic or sector themes.



## 10. Reconstruction
The process of rebuilding original data using only a subset of principal components. If few PCs can closely reproduce the data, it shows strong underlying factor structure.



## 11. Common Market Factor
The dominant driver (often PC1) that captures broad, systemic co-movement across all sectors or assets — essentially the “global market risk.”



## 12. Sector Rotation Factor
A component (often PC2) that distinguishes between cyclical (growth-sensitive) and defensive (stable) sectors, capturing leadership shifts across economic cycles.



## 13. Systemic Risk
The risk that affects the entire financial market or system, not diversifiable by holding a portfolio. PCA often identifies systemic risk in PC1.



## 14. Idiosyncratic Risk
Risk specific to an individual asset or sector, uncorrelated with broader market factors. Usually captured in residual variance after PCA.



## 15. Hedging
A portfolio management strategy that reduces exposure to unwanted risk factors (e.g., by offsetting PC1 exposure with index futures).



## 16. Factor Investing
An investment approach that allocates based on exposures to underlying factors (e.g., tilting toward cyclical sectors if PC2 leadership is expected).



## 03. Data Preparation  

- Converted prices → daily returns.  
- Standardized each sector series.  
- Checked missing values, outliers, and ensured numerical stability.  

Final dataset ready for PCA: mean = 0, variance = 1.  

---

## 04. Fitting PCA  

- Applied PCA using scikit-learn.  
- Computed:  
  - **Eigenvalues** (variance explained).  
  - **Eigenvectors** (loadings).  
  - **Scores** (time series of factor exposures).  

---

## 05. Analysis of Explained Variance  

- **PC1:** ~65% variance explained. Strong positive loadings across all sectors.  
- **PC2:** ~15%. Opposite loadings between **defensives (Staples, Utilities)** and **cyclicals (Tech, Energy)**.  
- **PC3:** ~5–6%. Captures commodity/energy-specific dynamics.  

Cumulative: **85%+ of sector return variance explained by first 3 PCs**.  

---

![Cumulative Variance Plot](/img/posts/pca_cumulative_variance.png)  
*Cumulative variance explained by the first three PCs.*  

---

## 06. Plot Interpretations  

### Scree Plot
![Scree Plot](/img/posts/pca_scree.png) 

Global equity market returns are largely driven by one dominant factor (broad market/systemic risk), with a secondary sector-rotation factor and small tertiary influences. Beyond 3 PCs, marginal explanatory power is negligible. 

### Cumulative Variance 
![Cumulative Variance](/img/posts/pca_cumulative_variance.png) 


By PC3, ~85% variance explained. Higher PCs add marginal noise only.  

### PC Loadings Biplot 
![PC Loadings Biplot](/img/posts/pca_biplot.png) 


- All sectors have strong positive PC1 loadings → confirming the common market factor.

- Along PC2, cyclical sectors (Tech, Industrials, Financials) load positively, while defensive sectors (Utilities, Staples) load negatively → PC2 represents a cyclical-vs-defensive rotation.

### Heatmap of Loadings  
![Heatmap of Loadings](/img/posts/pca_heatmap.png) 


- PC1: uniformly high positive loadings (systemic market risk).

- PC2: clear split between cyclical vs defensive.

- PC3/PC4: weaker, possibly capturing sector-specific anomalies (e.g., energy shocks, commodity-driven moves).

### Correlation Circle  
![Correlation Circle](/img/posts/correlation_circle.png) 


- Confirms systemic alignment along PC1.

- Opposite quadrants show sector opposition (e.g., Tech vs Utilities).

- Clear visualization of how sectors move relative to each other in factor space.

### PC1 Time Series
![PC1 Time Series](/img/posts/pc1_timeseries.png) 


PC1 behaves like a market-wide risk index (akin to a synthetic “market return”). Spikes/drops align with major crises, showing it captures systemic shocks. 

### PC2 Time Series  
![PC2 Time Series](/img/posts/pc2_timeseries.png) 


Captures **sector rotations**.  
Oscillates between defensive and cyclical leadership.  

### PC1 vs PC2 Scatter 

![PC1 vs PC2 Scatter](/img/posts/pc1_vs_pc2_scatter.png) 

- Data clusters around the origin, with large deviations during crisis periods.

- PC1 (horizontal axis) dominates → broad market crises.

- PC2 (vertical axis) swings during sector-rotation phases (growth vs defensives leadership shifts).

### Reconstruction  
![Reconstruction](/img/posts/reconstruction.png) 


- The reconstructed series tracks the original closely.

- Error is small (<5%), proving that a small set of latent factors fully captures sector return dynamics.

- Implies dimensionality reduction is effective for risk monitoring and portfolio replication.

---


## 07. Growth & Next Steps  

### Insights  
- Sector returns largely driven by **2 main factors**.  
- PCA is effective for **risk decomposition**.  
- Useful for **hedging** and **factor investing** strategies.  

### Next Steps  
1. **Rolling PCA:** Capture time-varying factor dynamics.  
2. **Global Expansion:** Add equities, bonds, FX, commodities.  
3. **Predictive Modeling:** Integrate PCA factors into machine learning for risk forecasting.  
4. **Portfolio Application:** Build PCA-hedged portfolios neutral to PC1 but tilted toward PC2 opportunities.  
5. **Dashboard:** Automate PCA updates for real-time monitoring.  

---

## Appendix — Reproducible Results  

- PC1 variance explained ≈ 65%  
- PC2 variance explained ≈ 15%  
- PC3 variance explained ≈ 5–6%  
- Cumulative ≈ 85%+ with 3 PCs  
- Reconstruction error minimal (<5%)  



---






