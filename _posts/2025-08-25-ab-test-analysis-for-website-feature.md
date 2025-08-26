---
layout: post
title: "A/B Test Analysis for Website Feature"
image: /posts/ab-test.jpg
tags: [Python, Statistics, AB-Testing, Chi-Square, Data Science]
---

## Project Purpose  

The purpose of this project is to analyze an **A/B test** for a new website feature.  
The objective is to determine whether the **Treatment group** achieves a significantly higher **conversion rate** compared to the **Control group**.  

We use statistical techniques — including the **Chi-Square Test for Independence**, **effect size (Cramér’s V)**, and **business uplift analysis** — to guide actionable recommendations.  

---

## Table of Contents  

- Project Overview  
  - Context  
  - Actions  
  - Results & Discussion  
- Concept Overview  
- Data Overview & Preparation  
- Applying Chi-Square Test For Independence  
- Analysing The Results  
- Discussion  

---

## 00. Project Overview  

### Context  
- Total users: **10,000** split evenly into Control (5,000) and Treatment (5,000).  
- Outcome of interest: **Conversion (Yes/No)**.  

**Key summary:**  
- Control: **479 conversions** → 9.58%  
- Treatment: **590 conversions** → 11.80%  

### Actions  
- Cleaned and validated dataset (no duplicates or missing values).  
- Constructed a **2×2 contingency table** (Converted vs Not Converted).  
- Applied a **Chi-Square Test** to check for independence.  
- Calculated **Cramér’s V** for effect size.  
- Assessed **business impact** (uplift in conversions at scale).  

### Results & Discussion  
- **Absolute uplift:** +2.22 percentage points.  
- **Relative uplift:** +23.17%.  
- **Chi-square result:** χ² = 12.905, p = 0.00033 → statistically significant.  
- **Effect size:** Cramér’s V = 0.0359 (small, but meaningful at scale).  
- **Extra conversions:** +111 (sample) → +2,220 (scaled to 100k users).  

✅ **Conclusion:** Treatment significantly improves conversions. Recommend rollout after revenue check.  

---

![Conversion Rates by Group](/img/posts/conversion_rates.png)  
*Bar chart of conversion rates for Control vs Treatment groups.*  

---

## 01. Concept Overview  

We are testing whether **conversion rates differ between Control and Treatment groups**.  

- **Why Chi-Square Test?**  
  - Data is categorical (Converted vs Not Converted).  
  - Measures association between **Group** and **Conversion**.  
  - Equivalent in inference to a two-proportion z-test for a 2×2 table.  

- **Assumptions:**  
  1. Independent observations (ensured by randomization).  
  2. Expected counts ≥ 5 (satisfied).  
  3. Groups are mutually exclusive.  

---

![Distribution of User Behavior](/img/posts/distributions.png)  
*Distribution of PageViews, Clicks, and Converted users across groups.*  

---

## 02. Data Overview & Preparation  

### Data Fields  
- `UserID` — unique identifier  
- `Group` — Control or Treatment  
- `Date` — experiment date  
- `PageViews`, `Clicks`, `Converted` — behavioral metrics  

### Cleaning & Validation  
- No missing values in key fields.  
- Balanced groups: 5,000 users each.  
- Counts:  
  - Control: 479 converted, 4521 not converted.  
  - Treatment: 590 converted, 4410 not converted.  

---

## 03. Applying Chi-Square Test For Independence  

### Contingency Table  

| Group     | Converted | Not Converted | Total |
|-----------|-----------|---------------|-------|
| Control   | 479       | 4521          | 5000  |
| Treatment | 590       | 4410          | 5000  |
| **Total** | 1069      | 8931          | 10000 |

### Expected Counts (under H₀)  

| Group     | Converted | Not Converted |
|-----------|-----------|---------------|
| Control   | 534.5     | 4465.5        |
| Treatment | 534.5     | 4465.5        |

✅ All expected counts > 5 → valid for chi-square.  

### Python Code  

{% highlight python %}
from scipy.stats import chi2_contingency
import numpy as np, math

# Contingency table
table = np.array([[479, 4521],
                  [590, 4410]])

chi2, p, dof, expected = chi2_contingency(table, correction=False)
n = table.sum()
cramers_v = math.sqrt(chi2 / (n * min(table.shape[0]-1, table.shape[1]-1)))

print("Observed:", table)
print("Expected:", expected)
print("Chi2:", chi2)
print("p-value:", p)
print("Cramer's V:", cramers_v)
{% endhighlight %}

---

![Chi-Square Test Visual](/img/posts/chi_square.png)  
*Visualization of observed vs expected conversion counts.*  

---

## 04. Analysing The Results  

### Statistical Significance  
- p < 0.05 → reject null hypothesis.  
- Treatment has significantly higher conversions.  

### Practical Impact  
- Absolute uplift: **+2.22%**  
- Relative uplift: **+23.17%**  
- Extra conversions: **+111 in 10k users**  
- At 100k users: **+2,220 conversions**  

### Effect Size  
- Cramér’s V ≈ 0.036 = small, but **important at high traffic scale**.  

### Robustness Checks  
- Two-proportion z-test: z = −3.592, p ≈ 0.00033 → matches chi-square.  
- Need to confirm balanced randomization (check user segments).  
- Check temporal consistency (early vs late test phase).  
- Monitor secondary KPIs (engagement, bounce, revenue per conversion).  

---

![Confidence Interval of Uplift](/img/posts/confidence_interval.png)  
*95% confidence interval for conversion rate uplift.*  

---

## 05. Discussion  

### Recommendation  
✅ Roll out the **Treatment** feature.  
- Statistically significant improvement in conversions.  
- Practical uplift translates into **thousands of extra conversions** at scale.  
- Perform a **revenue impact analysis** before final rollout.  

### Limitations  
- Small effect size (but significant with large N).  
- Only short-term conversion impact measured.  
- Randomization biases (bots, cookies) must be checked.  

### Next Steps  
1. **Revenue uplift analysis** (per-conversion revenue × extra conversions).  
2. **Segment deep-dive** (device type, geography, new vs returning).  
3. **A/B/n testing** for additional variants.  
4. **Monitor long-term KPIs** (retention, lifetime value).  

---

![Power Analysis Curve](/img/posts/power_curve.png)  
*Statistical power curve showing effect size vs detection probability.*  

---

## Appendix — Reproducible Results  

- Control conversion: 479/5000 = **9.58%**  
- Treatment conversion: 590/5000 = **11.80%**  
- χ² = **12.9053**, p = **0.00032765**, df = 1  
- Expected counts: Converted = 534.5, Not Converted = 4465.5 (per group)  
- Cramér’s V = **0.0359** (small)  
- Business uplift: +111 conversions (sample), +2,220 (100k users)  





