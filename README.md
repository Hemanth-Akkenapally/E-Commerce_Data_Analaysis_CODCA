# E-Commerce Data Challenge — Submission Package

## Submission Contents

This package contains the complete analysis for the Capital One Data Challenge:

### Core Analysis Files
1. **`Main_FINAL.ipynb`** — Complete analysis notebook with data loading, quality checks, answers to all 5 business questions, visualizations, and recommendations
2. **`functions_FINAL.ipynb`** — Reusable functions library (data loading, cleaning, type casting, merging, timezone conversion, analysis helpers)

### Documentation Files
3. **`DERIVED_FIELDS_METADATA.md`** — Complete metadata for all engineered fields
4. **`DATA_QUALITY_REPORT.md`** — Detailed documentation of all 6 data quality issues
5. **`RECOMMENDATIONS.md`** — Strategic recommendations and implementation roadmap
6. **`README.md`** — This file
7. **`ER_diagram.jpeg`** — Database schema visualization

---

## Key Metrics Summary

| Metric | Value |
|--------|-------|
| Raw rows (all datasets) | 429,541 |
| Duplicate rows removed | 26,097 (5.06%) |
| Final unique orders analyzed | 244,179 |
| Total revenue | $20,747,635.90 |
| Total profit | $8,636,804.27 |
| Average profit per order | $35.37 |
| Unique customers | 25,001 |
| Referral program cost | $21,077.79 |
| Referred customers | 3,563 (14.25% of base) |

> **Note on 244,179 orders:** The sales dataset contains one row per order line item. Multiple SKUs in the same order collapse into a single order when grouped by `order_id`. 350,897 line items → 244,179 unique orders after cleaning.

---

## Business Questions Answered

### Q1: Order Volume by Hour
**Finding:** Peak ordering hours are 8AM–8PM (company time), contributing 72.3% of all orders. The afternoon peak (3PM–8PM) alone accounts for 35.4%. Overnight (12AM–6AM) is only 12.9%, with the lowest hour at 3AM (~1.06%).

**Recommendation:** Heavy staffing during 8AM–8PM (especially 3–8PM). Lean overnight monitoring team. 24/7 full staffing is not cost-effective — estimated $50K–100K annual labor savings.

---

### Q2: Average Profit by Segment & Quarter
**Finding:** Tech Enthusiast customers generate $83–85 profit per order consistently across all quarters — 3.2× more than Bargain Hunters ($26/order). Other segments range from $24–37/order.

**Recommendation:** Shift 40% of marketing budget to Tech Enthusiast acquisition. Beauty Lover ($30–34) and Young Parent ($32–37) are the next priority tier.

---

### Q3: Referral Program 
**Finding:**

| Metric | Value |
|--------|-------|
| Item discount cost | $21,964.12 |
| Shipping tier gain (offset) | −$886.33 |
| Net revenue lost | $21,077.79 (0.10% of total revenue) |
| Referred customers | 3,563 (14.25% of base) |
| Orders by referred customers | 34,974 (14.32% of total) |
| Revenue from referred customers | $2,902,209.53 (13.99%) |
| Profit from referred customers | $1,214,838.09 (14.07%) |


**Recommendation:** Continue the program. profits are good when compared to annual sales and revenue profits. Consider adding referrer incentive ($5 credit) and tiered rewards at 3+ and 6+ referrals.

---

### Q4: Black Friday Impact
**Finding:** Black Friday contributes 1.1–1.4% of annual revenue per segment in a single day. Tech Enthusiast shows highest BF revenue ($34,028 in 2024, $23,950 in 2025). Revenue declined 2024→2025 across all segments, signaling the need for more aggressive investment.

**Recommendation:** Increase tech inventory 30% ahead of Black Friday. Launch preview campaigns 6 months early. Target Tech Enthusiast segment specifically. Goal: lift BF contribution from ~1.4% to 2–3% of annual revenue.

---

### Q5: Best Customer Base & KPIs
**Finding:** East Region + Tech Enthusiast is the highest-value combination.

**Geographic Performance:**
- **East Region:** $3.25M profit (37.6% of total) · 91K orders · 9,300 customers · $35.60 profit/order
  - Best timezone: America/New_York ($2.87M profit, 33.2% of total)

**Segment Performance:**
- **Tech Enthusiast:** $2.08M profit (24.2%) · $83–85/order
- **Beauty Lover:** $1.95M profit (22.6%) · $30–34/order
- **Young Parent:** $1.83M profit (21.1%) · $32–37/order

**Top Segment × Region Combinations:**
1. Tech Enthusiast × East Region: ~$780K profit
2. Beauty Lover × East Region: ~$725K profit
3. Young Parent × Central Region: ~$520K profit

**Recommended KPIs:**

*Acquisition:* CAC by segment (target < $20) · CLV (est. $1,800–2,000) · LTV:CAC ratio (target > 3:1)

*Engagement:* Repeat purchase rate (current 90%) · Days to 2nd purchase (target < 30–45 days) · Purchase frequency (current ~10 orders/customer)

*Profitability:* AOV by segment (current $84.97) · Contribution margin by segment (maintain > 23%) · Revenue per customer by region (current ~$830)

*Operational:* Inventory turnover (target > 6× annually) · Stockout rate (target < 2%) · NPS by segment (target > 50) · Market penetration by state (21 states currently)

---

## Data Quality Work

### 6 Issues Identified and Resolved

| # | Issue | Severity | Records Affected | Resolution |
|---|-------|----------|-----------------|------------|
| 1 | Duplicate Rows | HIGH | 26,097 (5.06% of 429,541) | Removed at load via `drop_duplicates()` |
| 2 | Profit Margin Format | CRITICAL | ~30% of vendor files | Normalized to decimal at ingestion |
| 3 | Missing Timezone | HIGH | 2,369 buyer records | Imputed via state → mode(timezone) mapping |
| 4 | Missing Datetime | MEDIUM | 188 sales records (0.08%) | Excluded from time-based analyses only |
| 5 | Numeric Outliers | MEDIUM | 36 extreme quantity rows | 99.9th percentile cap, 99.99% retention |
| 6 | Schema Misalignment | HIGH | All vendor files | Schema enforced at ingestion |

See `DATA_QUALITY_REPORT.md` for full details including detection code, resolution logic, and validation.

---

## Technical Implementation

### Key Design Decisions

**1. Timezone Conversion**
- Challenge: Customers in different timezones, need company-standard time (America/Chicago)
- Solution: Group by timezone, use `ZoneInfo` (Python 3.9+ stdlib), handle DST edge cases
- DST handling: `ambiguous='NaT'` (fall-back ambiguity → mark as missing), `nonexistent='shift_forward'` (spring-forward → shift)
- Why `ZoneInfo` over `pytz`: Correct DST arithmetic by default; no need for manual `localize()` + `normalize()` sequence

**2. Profit Margin Detection**
- Two signals: column name (`pct_profit_margin` vs `profit_margin`) + value threshold (>1 is impossible as decimal)
- Normalized at file ingestion, not after concat — prevents any mixed data entering the pipeline

**3. Scalable Merging**
- `merge_many_df()`: sequential multi-dataset joins with configurable keys, suffixes, and post-merge column drops
- Default: LEFT JOIN — preserves all sales rows, makes unmatched records visible as nulls (vs inner join which silently drops rows)

**4. Timezone Imputation Strategy**
- Used `mode(timezone)` per state from non-null records in the dataset itself
- Avoids hardcoded external map — states like Indiana/Kentucky span multiple timezones; mode reflects actual customer distribution

**5. Black Friday Detection**
- Challenge: Date changes every year (Friday after US Thanksgiving)
- Solution: Dynamically compute Thanksgiving (4th Thursday of November via `WeekOfMonth`) then add 1 day
- Applied across both 2024 and 2025 from a precomputed cache

**6. Referral Discount Logic**
- 10% off first order only for referred customers
- First order identified via `transform("min")` on order_datetime per buyer — broadcasts group minimum back to all rows for row-level comparison
- Shipping tier effect also captured: some orders after discount crossed into a lower/higher shipping tier

---

## Visualizations Created

All charts generated in Python (Plotly + Matplotlib):

1. **Hourly Order Volume** — Bar chart by hour (company timezone)
2. **Quarterly Profit by Segment** — Line/bar chart across 8 segments × 4 quarters
3. **Referral Cost Analysis** — Cost breakdown and referred vs non-referred performance
4. **Black Friday Revenue** — 2024 vs 2025 by customer segment
5. **Geographic Heatmaps** — Segment × Region and Segment × Timezone profit heatmaps
6. **KPI Dashboards** — Multi-metric scatter (profit vs customers, bubble = revenue)

---

## Assumptions

1. Company location: St. Louis, Missouri → `America/Chicago` timezone
2. Order timestamps recorded in customer's local timezone
3. Referred customers receive **10% off first order only**
4. Customer shipping tiers (after discounts): < $50 → $7.99 · $50–$99.99 → $4.99 · ≥ $100 → Free
5. Company flat shipping cost: $4.99 per delivered order
6. Black Friday sale: 20% discount on all items (not reflected in base price data)
7. Taxes: pre-calculated into data, ignored

---

## How to Run

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn plotly jupyter
```

### Steps
1. Ensure data files are in the same directory: `buyer.csv`, `sales.csv`, `vendor_datasets/` folder
2. Run functions notebook first: `jupyter notebook functions_FINAL.ipynb` → Run All
3. Run main analysis: `jupyter notebook Main_FINAL.ipynb` → Run All

### Expected Runtime
- Functions notebook: < 1 minute
- Main analysis: 3–5 minutes

---

## Compliance Statement

- ✅ No external data sources used (only provided datasets)
- ✅ All software open source (pandas, numpy, plotly, etc.)
- ✅ Work completed independently
- ✅ Code and materials kept confidential
- ✅ File size under 10 MB (notebooks without large outputs)
