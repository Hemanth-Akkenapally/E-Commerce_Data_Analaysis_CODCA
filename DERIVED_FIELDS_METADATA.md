# Derived Fields Metadata Documentation

## Overview
This document defines all new fields created during the analysis. Each field includes its purpose, calculation logic, data type, and example values. Definitions below reflect the **final implemented logic** used in the notebooks.

---

## Order-Line Metrics

### `order_row_value`
**Definition:** Total value of a single order line before any discounts  
**Calculation:** `price × quantity`  
**Data Type:** Float (currency)  
**Example:** 2 items at $25 → `2 × 25 = 50.00`  
**Used In:** Base revenue calculations and discount computation inputs

### `order_row_discount`
**Definition:** Referral discount amount applied to referred customers on their first order (line-level allocation)  
**Calculation:** `order_row_value × 0.10` if `is_first_order == True` and `is_referred == True`, else `0`  
**Data Type:** Float (currency)  
**Example:** $100 eligible line → `$100 × 0.10 = $10.00`  
**Used In:** Referral program cost / ROI analysis (Question 3)

### `black_friday_discount`
**Definition:** Black Friday discount amount (line-level allocation)  
**Calculation:** `order_row_value × 0.20` if `is_black_friday == True`, else `0`  
**Data Type:** Float (currency)  
**Example:** $100 line on Black Friday → `$100 × 0.20 = $20.00`  
**Used In:** Black Friday impact analysis (Question 4)

### `order_row_after_discount`
**Definition:** Order line value after applying **all applicable discounts** (referral + Black Friday)  
**Calculation:** `order_row_value - order_row_discount - black_friday_discount`  
**Data Type:** Float (currency)  
**Example:** $100 line with $10 referral + $20 BF → `$100 - $10 - $20 = $70.00`  
**Used In:** Net revenue and profitability computations

### `order_row_profit`
**Definition:** Profit from a single order line after discounts (margin applied to post-discount revenue)  
**Calculation:** `order_row_after_discount × profit_margin`  
**Data Type:** Float (currency)  
**Example:** Post-discount $70 with 25% margin → `$70 × 0.25 = $17.50`  
**Used In:** Profitability analysis (Question 2), total profit rollups

---

## Shipping Metrics (Order-Level)

### `customer_shipping_fee`
**Definition:** Shipping fee charged to the customer based on the **order total after discounts**  
**Calculation:** Tiered on **post-discount order total** (order-level):
- If `order_total_after_discounts < $50`: `$7.99`
- If `$50 ≤ order_total_after_discounts < $100`: `$4.99`
- If `order_total_after_discounts ≥ $100`: `$0.00`

**Data Type:** Float (currency)  
**Example:** Post-discount order total $75 → `$4.99`  
**Used In:** Profit (shipping margin), referral shipping tier counterfactuals

### `company_shipping_cost`
**Definition:** Shipping cost the company pays the carrier per delivered order  
**Calculation:** Flat rate of `$4.99` per delivered order  
**Data Type:** Float (currency)  
**Example:** Delivered order → `$4.99`  
**Used In:** Net profit calculations (shipping margin)

---

## Time-Based Fields

### `order_datetime_company`
**Definition:** Order timestamp converted to company timezone (America/Chicago) for operational analysis  
**Calculation:** Convert `order_datetime` from customer timezone → `America/Chicago` (timezone-aware), then store as naive Central Time  
**Data Type:** Datetime (naive, representing Central Time)  
**Example:** 3:00 PM ET → 2:00 PM CT  
**Used In:** Hourly volume analysis / staffing (Question 1)

### `hour_company`
**Definition:** Hour of day (0–23) in company timezone  
**Calculation:** Extract hour from `order_datetime_company`  
**Data Type:** Integer (0–23)  
**Example:** 2:30 PM CT → `14`  
**Used In:** Staffing analysis, hourly patterns

### `quarter`
**Definition:** Fiscal quarter of the order (company-time)  
**Calculation:** Extract quarter from `order_datetime_company`:
- Q1: Jan–Mar
- Q2: Apr–Jun
- Q3: Jul–Sep
- Q4: Oct–Dec  
**Data Type:** String (“Q1”, “Q2”, “Q3”, “Q4”)  
**Example:** March 15 → `"Q1"`  
**Used In:** Quarterly trends and profitability patterns

---

## Black Friday Field

### `is_black_friday`
**Definition:** Flag indicating if the order was placed on Black Friday  
**Calculation (implemented):** Based on the **customer-local** `order_datetime` date (consistent with the assumption that purchase timestamps are customer-local).  
**Data Type:** Boolean (True/False)  
**Example:** Order placed on Nov 29, 2024 (customer-local date) → `True`  
**Used In:** Black Friday revenue and discount impact analysis (Question 4)

**Implementation (conceptual):**
- Determine Black Friday dates for each year in scope
- `is_black_friday = (order_datetime.date() == black_friday_date_for_year)`

---

## Customer Behavior Fields

### `is_first_order`
**Definition:** Flag indicating whether this is the customer’s first order  
**Calculation:** For each `buyer_id`, sort by `order_datetime` (customer-local) and mark earliest order as first  
**Data Type:** Boolean (True/False)  
**Used In:** Referral discount application eligibility

### `is_referred_order`
**Definition:** Flag indicating an order is from a referred customer’s first order  
**Calculation:** `is_referred == True AND is_first_order == True`  
**Data Type:** Boolean (True/False)  
**Used In:** Referral program cost analysis

---

## Aggregate Metrics (Used in Visualizations / Reporting)

### `total_orders`
**Definition:** Count of distinct orders  
**Calculation:** `COUNT(DISTINCT order_id)`  
**Data Type:** Integer  
**Used In:** Volume analysis

### `total_revenue`
**Definition:** Total revenue after discounts  
**Calculation:** `SUM(order_row_after_discount)`  
**Data Type:** Float (currency)  
**Used In:** Revenue analysis

### `total_profit`
**Definition:** Total profit after discounts  
**Calculation:** `SUM(order_row_profit)` (+ order-level shipping margin, where applicable in the model)  
**Data Type:** Float (currency)  
**Used In:** Profitability analysis

### `avg_profit_per_order`
**Definition:** Average profit per order transaction (segment-level reporting)  
**Calculation:** `total_profit / total_orders`  
**Data Type:** Float (currency)  
**Used In:** Question 2 (segment profitability)

### `total_customers`
**Definition:** Count of distinct customers  
**Calculation:** `COUNT(DISTINCT buyer_id)`  
**Data Type:** Integer  
**Used In:** Customer base sizing

### `orders_per_customer`
**Definition:** Average number of orders per customer  
**Calculation:** `total_orders / total_customers`  
**Data Type:** Float  
**Used In:** Customer behavior / LTV-style framing

---

## Referral Program Metrics

### `items_discount_amt`
**Definition:** Total item discount given via referral program  
**Calculation:** Sum of `order_row_discount` over referred first orders  
**Data Type:** Float (currency)  
**Used In:** Referral program cost analysis

### `shipping_revenue_lost_ref`
**Definition:** Change in customer shipping fee due to referral discount shifting the shipping tier  
**Calculation:** `counterfactual_shipping_fee (no referral discount) - actual_shipping_fee`  
**Data Type:** Float (currency)  
**Used In:** Referral program cost analysis

### `net_revenue_lost_ref`
**Definition:** Total net revenue impact of referral program (items + shipping tier effects)  
**Calculation:** `items_discount_amt + shipping_revenue_lost_ref`  
**Data Type:** Float (currency)  
**Used In:** Referral program ROI framing

---

## Key Assumptions (Implemented)

1. **Referral Discount:** 10% off applies only to referred customers’ **first order**.  
2. **Black Friday Discount:** 20% discount applies to orders flagged as **Black Friday**.  
3. **Shipping Tiers:** Customer shipping fee is based on **order total after discounts**.  
4. **Company Timezone:** Operational analysis uses **America/Chicago** (St. Louis).  
5. **Customer Timezone:** Purchase timestamps are treated as **customer-local time**, and missing timezones are imputed using the dataset’s state→timezone patterns.

---

## Validation Checks Performed

- `order_row_value` always ≥ 0  
- `order_row_discount` ≤ `order_row_value`  
- `black_friday_discount` ≤ `order_row_value`  
- `order_row_after_discount` ≥ 0 (by construction; investigated any anomalies if present)  
- `customer_shipping_fee` ∈ {0, 4.99, 7.99}  
- `company_shipping_cost` = 4.99 for delivered orders  
- `hour_company` in range 0–23  
- Only one `is_first_order = True` per `buyer_id`  
- `is_black_friday` only True for valid Black Friday dates

---

## Example Walkthrough (Hypothetical)

**Scenario:** A line item qualifies for both referral and Black Friday discounts (hypothetical; not required to occur in the dataset)

- `order_row_value = $100.00`
- `order_row_discount = $10.00` (10%)
- `black_friday_discount = $20.00` (20%)
- `order_row_after_discount = $70.00`
- `order_row_profit = $70.00 × profit_margin`

> Note: This example is illustrative. The dataset may not contain overlapping discount cases.

---

*End of Metadata Documentation*
