## 🧠 Feature Engineering & Metrics

To ensure accurate business analysis, multiple derived fields were created across revenue, profit, time, and customer behavior.

---

### 💰 Revenue & Profit Metrics

- **Order Value:** `price × quantity`
- **Referral Discount:** 10% off first order for referred customers
- **Black Friday Discount:** 20% applied on BF orders
- **Net Revenue:** After all discounts
- **Profit:** Applied margin on post-discount revenue

👉 Ensures accurate profitability analysis across segments

---

### 🚚 Shipping Logic

- <$50 → $7.99  
- $50–$99 → $4.99  
- ≥$100 → Free  
- Company cost: $4.99 per order  

👉 Used to analyze **true profit including shipping impact**

---

### ⏰ Time-Based Features

- Converted all timestamps to **company timezone (America/Chicago)**
- Extracted:
  - Hour (0–23)
  - Quarter (Q1–Q4)

👉 Enabled:
- Peak hour analysis
- Staffing optimization

---

### 🛍️ Customer Behavior Features

- **First Order Flag**
- **Referred Order Flag**
- **Black Friday Flag**

👉 Used for:
- Referral ROI analysis
- Customer segmentation
- Seasonal impact

---

### 📊 Business Metrics

- Total Revenue & Profit  
- Average Profit per Order  
- Orders per Customer  
- Customer Lifetime Indicators  

---

## 🎯 Key Takeaway

👉 Strong feature engineering enabled accurate insights across revenue, customer behavior, and operational performance.
