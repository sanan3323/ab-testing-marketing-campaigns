# A/B Testing: Marketing Campaign Evaluation

30-day A/B test comparing two marketing campaigns (Control vs Test) across a
user acquisition funnel (impressions, clicks, product views, add-to-cart,
purchases). The question: did the Test campaign's activity actually move
revenue, or just upper-funnel engagement?

## Results

Short version: Test pulls more clicks, but those clicks convert to cart at a
worse rate than Control's, and final revenue efficiency is unchanged. Don't
scale Test.

| Metric | p-value | r (rank-biserial) | Effect | Direction |
|---|---|---|---|---|
| Click-through rate | 0.0003 | +0.556 | Large | Test > Control |
| Add-to-cart rate | 0.0085 | -0.400 | Medium | Control > Test |
| Conversion rate | 0.3061 | -0.156 | Small | Not significant |
| Cost per click | 0.8260 | -0.034 | Small | Not significant |
| Cost per purchase | 0.2717 | +0.168 | Small | Not significant |

All tests Mann-Whitney U, two-sided, α = 0.05. r is rank-biserial correlation
matched to the U statistic (small < 0.3, medium 0.3-0.5, large > 0.5).
Bootstrap 95% CIs on the median difference (Test minus Control) for the
significant results: CTR [0.0087, 0.0706], Add-to-Cart [-0.4127, -0.0220].

The pattern matters more than the rows on their own. Test wins the click
step with a large effect but loses the click-to-cart step with a medium
effect. Conversion rate, cost per click, and cost per purchase are flat.
Read: Test isn't attracting better customers, it's attracting more clickers
who don't add to cart. The CTR gain is headline engagement that doesn't
translate into purchase intent. Cost per purchase is statistically
equivalent between the two (p = 0.27), so Test isn't buying cheaper
acquisitions either, just noisier ones.

Before scaling Test, the step to investigate is click-to-cart. Most likely
culprits: the ad creative promises something the landing page doesn't
deliver, or targeting is drawing browsers instead of buyers. A landing-page
A/B test for Test's traffic is the next move, not a budget increase.

## Method notes

- Tested each KPI for normality with Shapiro-Wilk per group. Every metric
  failed normality in at least one of the two groups.
- Checked variance homogeneity with Levene's test. CTR came back with
  heteroscedastic variances between groups.
- Given non-normality plus unequal variances on CTR, Mann-Whitney U is the
  right call. Welch's t-test would have been wrong here.
- Reported rank-biserial correlation r as the effect size (natural companion
  to the U statistic). Bootstrap 95% CI on the median difference in parallel
  for sanity.
- The Add-to-Cart CI [-0.4127, -0.0220] is wide. The upper bound is close to
  zero, so the rejection is real but the true magnitude is uncertain. The
  significance is solid; the size estimate is not.
- KPIs computed per campaign-day before the between-group comparison. Each
  row in the test is one day's aggregate, not one user. That matches how the
  data ships.
- IQR method used to flag outlier days during EDA. Kept them in the test
  because 30 rows per group is too little data to throw any away.

## Repo layout

```
ab-testing-marketing/
├── README.md
├── requirements.txt
├── notebooks/
│   └── ABTesting.ipynb
├── src/
│   ├── __init__.py
│   └── preprocessing.py
└── data/
    ├── raw/
    │   ├── control_group.csv
    │   └── test_group.csv
    └── processed/
        └── ab_combined.csv
```

## Run it

```bash
git clone https://github.com/<you>/ab-testing-marketing.git
cd ab-testing-marketing
python -m venv venv && source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/ABTesting.ipynb
```

Tested on Python 3.9+. Dependencies: pandas, numpy, scipy, seaborn,
matplotlib, jupyter.

## Limitations

- 30 days of daily aggregates is a small sample. The non-significant
  conversion-rate result could be a real null effect or a power issue.
  I didn't run a retrospective power analysis to estimate the minimum
  detectable effect, which is the right next step to tell the two apart.
- Fixed-horizon design. Peeking at the data mid-experiment would have
  inflated the false-positive rate, so the test ran to its full 30 days.
  A sequential design (SPRT, or a Bayesian sequential setup) would let
  future tests stop early when the signal is clearly one way or the other.
- No segmentation. Aggregate results can hide heterogeneous effects across
  weekdays, spend levels, or ad formats. A per-cohort breakdown would be the
  next layer and would probably change which parts of Test are worth
  scaling.
- Frequentist testing only. Bayesian A/B testing would give a posterior on
  the uplift and an expected-loss number, which is more useful for a
  stakeholder conversation than a p-value and a yes/no significance call.

## License

MIT.
