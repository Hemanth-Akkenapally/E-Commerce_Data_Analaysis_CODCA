## 🧹 Data Quality & Cleaning

Ensuring data reliability was a critical part of this project.  
Out of **429K+ raw records**, several major issues were identified and resolved before analysis.

---

### ⚠️ Key Issues Identified

1. **Duplicate Records**
   - ~26K duplicate rows (5% of data)
   - 👉 Removed to prevent inflated revenue and order counts

2. **Profit Margin Inconsistency**
   - Some vendors used % (15) vs decimal (0.15)
   - 👉 Standardized to decimal format to avoid 100× calculation errors

3. **Missing Timezone Data**
   - ~2,369 customer records are missing timezone
   - 👉 Imputed using state-based mapping (data-driven approach)

4. **Missing Order Timestamps**
   - 188 records (~0.08%)
   - 👉 Excluded only from time-based analysis (no assumptions made)

5. **Outliers in Quantity**
   - Extreme values (up to 9,999 items)
   - 👉 Trimmed using 99.9th percentile (99.99% data retained)

6. **Schema Inconsistency (Vendor Files)**
   - Different column formats across datasets
   - 👉 Standardized schema before merging

---

### 📊 Final Clean Dataset

- 📦 **Total Orders:** 244,179  
- 👥 **Customers:** 25,001  
- 💰 **Revenue:** $20.7M  
- 📈 **Profit:** $8.63M  
- ✅ **Data Coverage:**  
  - Datetime: 99.92%  
  - Timezone: ~99%  

---

## 🎯 Key Takeaway

👉 Clean data = reliable insights.  
Fixing data quality issues prevented major errors in revenue, profit, and customer analysis.
