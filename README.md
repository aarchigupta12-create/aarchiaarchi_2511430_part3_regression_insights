# aarchiaarchi_2511430_part3_regression_insights
# Part 3 — Regression Insights: Store Sales Drivers

## Business Problem
Identify which store-level factors (marketing, footfall, staffing, discounting,
inventory, location, ratings) actually drive `monthly_sales`, so investment
decisions can be based on evidence instead of intuition.

## Dataset Used
`data/business_regression_data.xlsx` — a balanced panel of 80 stores across
4 months (320 rows), with marketing spend, footfall, discounting, staffing,
inventory availability, competitor distance, holiday flag, rating, and
monthly sales/profit.

## Tools Used
Python (pandas, numpy, statsmodels), Google Colab

## Steps Performed
1. Checked data quality: no duplicate rows or duplicate store-month
   combinations (fully balanced panel); confirmed `region` and `store_type`
   have clean, consistent category labels. Found missing values only in
   `competitor_distance_km` (6 rows) and `customer_rating` (8 rows) — both
   under 3% of the data.
2. Imputed the two columns with missing values using the median (robust to
   outliers, appropriate for this small a gap).
3. Reviewed correlations between each numeric predictor and `monthly_sales`.
4. Fit a multiple linear regression: `monthly_sales` on all numeric
   predictors plus `region` and `store_type` as categorical (dummy-coded)
   variables.
5. Checked for multicollinearity using Variance Inflation Factor (VIF).
6. Reviewed residuals for basic model health (mean ~0, no obvious pattern).
7. Repeated the same model for `monthly_profit` as a secondary check.

## Key Outputs
**Model fit:** R² = 0.857, Adjusted R² = 0.850, F-test p < 0.0001 —
the model explains ~86% of the variation in monthly sales.

**Statistically significant positive drivers** (p < 0.05):
- `marketing_spend`, `footfall`, `staff_count`, `inventory_availability_pct`,
  `holiday_flag`, `customer_rating`
- Region: `South` and `West` significantly outsell the baseline (`East`)
- Store type: none outsell `Airport` (the baseline category)

**Statistically significant negative drivers:**
- `competitor_distance_km` — stores farther from a competitor sell *less*
- `store_type` = High Street and Residential sell significantly less than
  Airport stores, holding everything else constant

**Not statistically significant:**
- `avg_discount_pct` (p ≈ 0.22) — discounting doesn't reliably move sales
  once other factors are controlled for
- Region `North` vs. `East` (p ≈ 0.13)
- Store type `Mall` vs. `Airport` (p ≈ 0.19)

**Multicollinearity:** all VIFs are well under the common threshold of 10;
`footfall` and `staff_count` show the highest values (~6.5), meaning they
move together somewhat (larger stores likely have both more staff and more
visitors), but this isn't severe enough to distort the model.

## Business Insights
Sales are driven mainly by scale and operational factors — marketing spend,
footfall, staffing, and inventory availability — not by discounting.
The discount finding is the most actionable insight: heavier discounting
is not statistically associated with higher sales in this data, so
across-the-board discounting is not obviously paying for itself and is worth
testing more rigorously (e.g., an A/B test like Part 2's) before continuing
it as a blanket strategy. Airport locations dramatically outperform
High Street and Residential stores even after controlling for marketing,
staffing, and footfall — suggesting the airport channel benefits from
factors this dataset doesn't capture directly (e.g., captive audience,
higher-spending traveler segment), and may deserve more investment relative
to underperforming formats.

## Assumptions Made
- Missing values in `competitor_distance_km` and `customer_rating` are
  missing at random and were imputed with the column median.
- The regression is linear and additive; no interaction terms (e.g.,
  marketing spend x store type) were tested, though they may exist.
- Coefficients are interpreted as associations, not proven causal effects —
  this is observational panel data, not a controlled experiment.

## Known Limitations
- `footfall` and `staff_count` are moderately collinear (VIF ~6.5), so their
  individual coefficients should be interpreted with some caution even
  though the overall model isn't badly distorted.
- Only 4 months of data per store — not enough to control for store-specific
  fixed effects or seasonality beyond the single `holiday_flag` indicator.
- The negative `competitor_distance_km` coefficient is easy to
  over-interpret; it doesn't mean competitors help sales, more likely both
  are proxies for being in a high-traffic location.

## Screenshots
See `screenshots/` for the regression summary output, VIF table, and
correlation matrix.
