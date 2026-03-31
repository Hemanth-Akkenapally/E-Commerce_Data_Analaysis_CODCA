# 🛒 E-Commerce Data Analysis — Capital One Data Challenge

End-to-end data analysis project focused on uncovering business insights from e-commerce data using Python and visualization tools.

---

## 🚀 Project Overview

This project analyzes customer behavior, revenue trends, and operational efficiency across ~244K orders to answer key business questions such as:

- When do customers place the most orders?
- Which customer segments are most profitable?
- Is the referral program worth the cost?
- How impactful is Black Friday?
- What is the best customer base to target?

---

## 📊 Key Results

- 💰 **Total Revenue:** $20.7M  
- 📈 **Total Profit:** $8.63M  
- 🧾 **Orders Analyzed:** 244,179  
- 👥 **Customers:** 25,001  
- 🎯 **Referral Contribution:** ~14% of revenue  

---

## 🔍 Key Insights

### ⏰ Peak Order Hours
- 72% of orders occur between **8AM–8PM**
- Afternoon peak (3PM–8PM) = **35% of total orders**
- 👉 Optimize staffing → potential **$50K–100K savings**

---

### 💡 Most Valuable Customers
- **Tech Enthusiasts → $83–85 profit/order (3.2× others)**
- High-value segments:
  - Tech Enthusiast
  - Beauty Lover
  - Young Parent

👉 Recommendation: Shift **40% marketing budget** toward top segments

---

### 🎯 Referral Program Performance
- Cost: ~$21K (only 0.10% of revenue)
- Drives:
  - 14% of customers
  - 14% of profit

👉 **Highly effective → continue & expand**

---

### 🛍️ Black Friday Impact
- Generates **1.1–1.4% of annual revenue in ONE day**
- Decline observed from 2024 → 2025

👉 Opportunity: Increase investment + inventory

---

### 🌎 Best Customer Base
- **Region:** East → 37.6% of total profit  
- **Segment:** Tech Enthusiast → highest value  

👉 Best combo: **Tech Enthusiast × East Region**

---

## 🧠 Data Engineering & Processing

- Cleaned **429K+ raw rows**
- Removed **26K duplicates (5%)**
- Handled:
  - Missing timezones
  - Datetime issues
  - Outliers
  - Schema inconsistencies

---

## ⚙️ Tech Stack

- Python (Pandas, NumPy)
- Data Visualization (Matplotlib, Plotly)
- Jupyter Notebook
- Data Cleaning & Feature Engineering

---

## 📂 Project Structure

- `Main_FINAL.ipynb` → Full analysis + insights  
- `functions_FINAL.ipynb` → Reusable pipeline functions  
- `DATA_QUALITY_REPORT.md` → Data issues + fixes  
- `RECOMMENDATIONS.md` → Business strategy  
- `DERIVED_FIELDS_METADATA.md` → Feature definitions  
- `ER_diagram.jpeg` → Database schema  

---

## 📈 Visualizations

- Hourly order trends  
- Profit by customer segment  
- Referral performance  
- Black Friday revenue comparison  
- Regional & segment heatmaps  

---

## ▶️ How to Run

```bash
pip install pandas numpy matplotlib seaborn plotly jupyter
