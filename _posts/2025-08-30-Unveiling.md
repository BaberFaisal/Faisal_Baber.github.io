---
layout: post
title: "Unveiling Hidden Risk Factors in Global Markets using PCA"
image: /posts/pca-market.jpg
tags: [Python, Finance, PCA, Statistics, Data Science]
---

## Project Purpose  

The purpose of this project is to analyze **sector-level returns in global markets** using **Principal Component Analysis (PCA)**.  
The goal is to identify hidden risk factors, reduce dimensionality, and provide actionable insights into **systematic shocks** and **sector rotations** that drive asset price movements.  

We use PCA to:  
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
- PCA Overview  
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

✅ **Conclusion:** Global sector markets can be reduced to a few interpretable drivers, enabling more effective **risk management and portfolio design**.  

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

## 02. PCA Overview  

**Principal Component Analysis (PCA)**:  
- Linear technique to transform correlated variables into uncorrelated components.  
- First components explain the maximum possible variance.  
- Interpretation in finance: PCs ≈ hidden **risk factors**.  

---

## 03. Data Preparation  

- Converted prices → daily returns.  
- Standardized each sector series.  
- Checked missing values, outliers, and ensured numerical stability.  

✅ Final dataset ready for PCA: mean = 0, variance = 1.  

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

### A — Scree Plot
![Scree Plot](/img/posts/pca_scree.png) 
Shows eigenvalues in descending order. Sharp drop after PC2 → market is **2-factor dominant**.  

### B — Cumulative Variance (`pca_cumulative_variance.png`)  
By PC3, ~85% variance explained. Higher PCs add marginal noise only.  

### C — PC Loadings Biplot (`pca_biplot.png`)  
- PC1: all sectors aligned (systematic risk).  
- PC2: divergence between **cyclical vs defensive sectors**.  
- Interpretation: Market vs rotation tradeoff.  

### D — Heatmap of Loadings (`pca_heatmap.png`)  
Clear contrast in loadings: Energy & Tech vs Staples & Utilities.  

### E — Correlation Circle (`correlation_circle.png`)  
Sectors close together = high correlation.  
Defensives cluster together, cyclicals in another group.  

### F — PC1 Time Series (`pc1_timeseries.png`)  
Tracks **overall market sentiment**.  
Sharp negative spikes during 2008 & 2020 crises.  

### G — PC2 Time Series (`pc2_timeseries.png`)  
Captures **sector rotations**.  
Oscillates between defensive and cyclical leadership.  

### H — PC1 vs PC2 Scatter (`pc1_vs_pc2_scatter.png`)  
Reveals orthogonal shocks:  
- PC1 axis = systemic crashes.  
- PC2 axis = rotation-driven divergences.  

### I — Reconstruction (`reconstruction.png`)  
Actual vs reconstructed Tech ETF returns.  
3 PCs almost fully replicate real returns.  
Residual = idiosyncratic (stock-specific) noise.  

---

![PC Biplot Example](/img/posts/pca_biplot.png)  
*Biplot showing sector loadings on PC1 and PC2.*  

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

✅ Confirms PCA as a strong method for unveiling hidden risk factors in markets.  

---


