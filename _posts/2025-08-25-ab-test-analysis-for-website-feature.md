---
layout: post
title: "A/B Test Analysis for Website Feature"
image: /posts/ab-test.jpg
tags: [Python, Statistics, AB-Testing, Chi-Square, Data Science]
---

## Project Purpose  

The purpose of this project is to analyze an **A/B test** for a new website feature. The objective is to determine whether the **Treatment group** achieves a significantly higher **conversion rate** compared to the **Control group**.  

I use statistical techniques including the **Chi-Square Test for Independence**, **effect size (Cramér’s V)**, and **business uplift analysis**  to guide actionable recommendations.  


## GitHub Repository Link  


The source code for this project can be found at my [GitHub Repository](https://github.com/BaberFaisal/A-B-Test-Analysis-for-Website-Feature.git)

---

## Table of Contents  

- [00. Project Overview](#00-project-overview)  
  - [Context](#context)  
  - [Actions](#actions)  
  - [Results & Discussion](#results--discussion)  
- [01. Concept Overview](#01-concept-overview)  
- [02. Data Overview & Preparation](#02-data-overview--preparation)  
- [03. Applying Chi-Square Test For Independence](#03-applying-chi-square-test-for-independence)  
- [04. Analysing The Results](#04-analysing-the-results)  
- [05. Discussion](#05-discussion)  





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

**Conclusion:** Treatment significantly improves conversions. Recommend rollout after revenue check.  

---

![Conversion Rates by Group](/img/posts/conversion_rates.png)  
*Bar chart of conversion rates for Control vs Treatment groups.*  

---

## 01. Concept Overview  
## A/B Testing
A controlled experiment that compares two versions of a product, feature, or webpage (Version A = control, Version B = variation) to determine which performs better against a defined metric.

## Null Hypothesis (H₀)
The assumption that there is **no difference** in performance between version A and version B. Any observed difference is due to random chance.

## Alternative Hypothesis (H₁)
The assumption that there **is a difference** in performance between version A and version B (e.g., Version B improves conversion rate compared to Version A).

## Conversion Rate (CR)
The percentage of users who take a desired action (such as making a purchase, signing up, or clicking a button).  


## Baseline Metric
The initial performance value (e.g., current conversion rate of Version A) that serves as a benchmark against which the variation (Version B) is compared.

## Uplift
The percentage improvement of the variation (B) over the control (A).  


## Sample Size
The total number of observations (users, sessions, or actions) included in the test. Adequate sample size ensures that the test has enough statistical power.

## Statistical Significance
The likelihood that the observed difference between A and B is not due to chance. A result is considered statistically significant if the **p-value** is below a chosen threshold (commonly 0.05).

## p-value
The probability of observing the test results (or more extreme) if the null hypothesis is true.  
- **Low p-value (< 0.05)** → Reject H₀ (evidence of a real difference).  
- **High p-value (≥ 0.05)** → Fail to reject H₀ (difference is likely due to chance).

## Confidence Interval (CI)
A range of values that is likely to contain the true difference between A and B with a certain confidence level (commonly 95%). Narrower intervals = more precise estimates.

## Effect Size
The magnitude of the difference between Version A and Version B. It indicates whether the difference is **practically important**, not just statistically significant.

## Practical Significance
Even if a result is statistically significant, it may not be meaningful from a business perspective. Practical significance considers whether the observed uplift justifies implementation costs and risks.

## Statistical Power
The probability that the test will correctly reject the null hypothesis when the alternative hypothesis is true. Higher power reduces the risk of false negatives.

## Type I Error (False Positive)
Rejecting the null hypothesis when it is actually true (detecting an effect that doesn’t exist). Controlled by the **significance level (α)**.

## Type II Error (False Negative)
Failing to reject the null hypothesis when the alternative hypothesis is true (missing a real effect). Controlled by the **power** of the test.

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

 All expected counts > 5 → valid for chi-square.  

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
Roll out the **Treatment** feature.  
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





