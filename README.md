  # 📊 Marketing A/B Testing: Campaign Optimization & ROI Analysis

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?style=flat-square&logo=python)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?style=flat-square&logo=pandas)
![SciPy](https://img.shields.io/badge/SciPy-Statistical%20Testing-8CAAE6?style=flat-square&logo=scipy)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)

## 📌 Business Problem & Objective

Marketing budgets are routinely misallocated when campaign evaluation relies on raw volume metrics rather than statistical rigour. This project analyses the results of a 30-day A/B test running two parallel marketing campaigns — **Control** vs. **Test** — across a complete user acquisition funnel.

Using non-parametric hypothesis testing and effect-size quantification, the analysis determines whether the Test campaign drove statistically significant improvements in user engagement, purchase intent, and cost efficiency. Findings directly inform future budget allocation decisions.

---

## 🔬 Methodology & Pipeline

```
Raw CSV Data
    │
    ▼
Data Ingestion & Schema Validation
    │
    ▼
Preprocessing  ──  datetime parsing · missing value imputation · column standardisation
    │
    ▼
Exploratory Data Analysis  ──  spend · impressions · distribution · outlier detection (IQR)
    │
    ▼
KPI Engineering  ──  CTR · Add-to-Cart Rate · Conversion Rate · CPC · CPP
    │
    ▼
Normality Testing  ──  Shapiro-Wilk per metric per group
    │
    ▼
Variance Homogeneity  ──  Levene's Test
    │
    ▼
Hypothesis Testing  ──  Mann-Whitney U (non-parametric, two-sided, α = 0.05)
    │
    ▼
Effect Size  ──  Rank-Biserial Correlation (r) + Bootstrap 95% CI on mean difference
    │
    ▼
Funnel Analysis  ──  Impressions → Clicks → Views → Cart → Purchases
    │
    ▼
Business Recommendations
```

**Why Mann-Whitney U?** Shapiro-Wilk tests confirmed that KPI distributions deviate significantly from normality. Levene's test additionally revealed heterogeneous variances for CTR. Mann-Whitney U is the appropriate non-parametric alternative to Welch's t-test under these conditions.

---

## 📈 Key Findings

All tests run at **α = 0.05**, two-sided. Effect size reported as rank-biserial correlation (r): small < 0.3, medium 0.3–0.5, large > 0.5.

| KPI | Test Result | Effect Size (r) | Business Interpretation |
|-----|-------------|-----------------|------------------------|
| Click-Through Rate (CTR) | **Significant** ✅ | Moderate | Test campaign meaningfully shifted top-of-funnel engagement |
| Add-to-Cart Rate | **Significant** ✅ | Moderate | Test campaign improved mid-funnel purchase intent |
| Conversion Rate | Not Significant ❌ | Negligible | Bottom-line purchase rate unchanged between campaigns |
| Cost per Click (CPC) | Not Significant ❌ | Negligible | Spend efficiency per click is statistically equivalent |
| Cost per Purchase (CPP) | Not Significant ❌ | Negligible | Revenue efficiency per acquisition is statistically equivalent |

> **Strategic Recommendation:** The Test campaign successfully improves top and mid-funnel metrics (CTR, Add-to-Cart Rate), but these gains do not translate into higher final conversion rates. Before scaling the Test campaign budget, stakeholders should investigate the **Add-to-Cart → Purchase drop-off** — this bottleneck is the critical unsolved problem. Scaling spend on a campaign that does not improve final conversion would increase cost without proportional revenue gain.

---

## 📁 Project Structure

```
ab-testing-marketing/
├── data/
│   ├── raw/
│   │   ├── control_group.csv       # Original Control campaign data (immutable)
│   │   └── test_group.csv          # Original Test campaign data (immutable)
│   └── processed/
│       └── ab_combined.csv         # Merged, cleaned dataset with engineered KPIs
├── notebooks/
│   └── ABTesting.ipynb             # Full analysis narrative — primary deliverable
├── src/
│   ├── __init__.py
│   └── data_processing.py          # Reusable preprocessing and KPI functions
├── requirements.txt
└── README.md
```

---

## ⚙️ Reproducibility

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/ab-testing-marketing.git
cd ab-testing-marketing

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch the notebook
jupyter notebook notebooks/ABTesting.ipynb
```

**Requirements:** Python 3.9+, pandas, numpy, scipy, seaborn, matplotlib, jupyter

---

## 🧰 Tech Stack

| Library | Purpose |
|---------|---------|
| `pandas` | Data ingestion, merging, and transformation |
| `numpy` | Vectorised computations and bootstrap sampling |
| `scipy.stats` | Shapiro-Wilk, Levene's, and Mann-Whitney U tests |
| `seaborn / matplotlib` | Statistical and business visualisations |
| `jupyter` | Reproducible narrative notebook |

---

## 🚀 Future Work

- **Sequential testing:** Replace the fixed-horizon design with a sequential A/B framework (e.g., SPRT) to enable early stopping and reduce experiment duration.
- **Bayesian A/B testing:** Model posterior distributions over conversion rate uplift for richer decision-making under uncertainty, including expected loss calculations.
- **Segmentation analysis:** Stratify results by date cohort and spend quintile to detect heterogeneous treatment effects masked by aggregate averages.
- **Retrospective power analysis:** Calculate the Minimum Detectable Effect (MDE) the study was powered to detect, to contextualise the non-significant conversion rate result.

---

## 📄 License

Distributed under the MIT License. See `LICENSE` for details.
