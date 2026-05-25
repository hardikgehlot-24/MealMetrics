# 🍟 McDonald's Sales Analytics Dashboard —  Power Query

> **Key Insight:** Evening shift drives peak revenue ($13.50 avg order value), Apple Slices dominate Sides category at $25–$30 total sales, and workdays outperform weekends despite lower order frequency — signaling a lunch/dinner rush pattern among office-hour customers.

---

## 📁 Project Structure

```
mcdonalds-sales-analytics/
├── order_details.csv          # Raw transactional data (1000 rows, 5 columns)
├── mcdonald's menu items.xlsx # Menu master table (32 items, 4 columns)
├── dashboard.pbix             # Power BI Dashboard
└── README.md
```

---

## 📊 Dataset Overview

### Table 1: `order_details` (999+ rows, 9 columns after transformation)

| Column | Type | Description |
|---|---|---|
| order_details_id | Int64 | Unique row identifier (1000 distinct, 1000 unique) |
| order_id | Int64 | Order-level grouping (439 distinct, 166 unique) |
| order_date | Date | Range: 01/01/2023 onwards |
| order_time | Time | 438 distinct time stamps |
| item_id | Int64 | Foreign key → menu_item_id |
| price | Currency | Joined from menu table (99% valid, 1% null) |
| Day Name | Text | Derived via `Date.DayOfWeekName()` |
| workday_type | Text | Conditional: Saturday/Sunday = "weekend", else "workday" |
| TimePeriod | Text | Binned from hour: Morning/Afternoon/Evening/Night |

### Table 2: `mcdonald's menu items` (32 rows, 4 columns)

| Column | Type | Description |
|---|---|---|
| menu_item_id | Int64 | Primary key (32 distinct, 32 unique) |
| item_name | Text | 32 unique menu items |
| category | Text | 10 categories (Burger, Chicken, Fries, Sides, Wraps, etc.) |
| price | Currency | Range: $1.00 (Apple Slices) – $7.99 (Angus Third Pounder) |

---

## 🔧 Power Query Transformations Applied

1. **NULL Handling** — Replaced `"NULL"` strings → empty → `null` in `item_id`
2. **Type Coercion** — `order_date` parsed with `en-US` locale to handle MM/DD/YYYY format
3. **Left Outer Join** — `order_details.item_id` → `menu_items.menu_item_id` to bring in `price`
4. **Day Name Derivation** — `Date.DayOfWeekName([order_date])`
5. **Workday Classification** — Conditional column: weekend vs workday
6. **Time Period Binning** — Custom M function bucketing `Time.Hour()` into 4 shifts:
   - 🌅 Morning: 5:00–11:59
   - ☀️ Afternoon: 12:00–16:59
   - 🌆 Evening: 17:00–20:59
   - 🌙 Night: 21:00–4:59

---

## 📈 Exploratory Data Analysis (EDA)

### 1. Sales Performance Summary

| Metric | Value |
|---|---|
| **Total Sales** | $205.00 |
| **Total Orders** | 201 |
| **Total Quantity Sold** | 205 |
| **Average Order Value** | $1.02 |
| **Avg Order Value Per Day** | $2.47 |

> *Note: Low AOV ($1.02) suggests the dataset is a sample slice, not full annual data.*

### 2. Time-of-Day Analysis

Evening and Afternoon are the highest-revenue time bands:

| Period | Approx. Avg Sale | Observation |
|---|---|---|
| **Evening** | ~$13.00 | Peak revenue band |
| **Afternoon** | ~$11.00 | Second highest |
| **Morning** | ~$3.50 | Low traffic |
| **Night** | ~$1.50 | Minimal activity |

**Insight:** 80%+ of revenue is concentrated in the post-noon window (12 PM–9 PM).

### 3. Day-of-Week Patterns

- Wednesday (highlighted/selected in dashboard) shows notable activity
- Workdays generate higher total revenue despite weekends typically having more leisure time
- Workday vs. Weekend ratio from pie chart: approximately 65% workday / 35% weekend

### 4. Product-Level Analysis

**Top Performing Item: Apple Slices** ($25–$30 in total sales from Sides)

- Apple Slices (menu_item_id: 114) at $1.00 unit price has highest volume in the Sides category
- High frequency of ordering compensates for low unit price

**Price Range of Menu:**
- Lowest: Apple Slices — $1.00
- Highest: Angus Third Pounder — $7.99
- Most items cluster between $2.79–$6.99

### 5. Category Distribution (from menu table)

| Category | Item Count | Price Range |
|---|---|---|
| Burger | 9 | $1.99–$7.99 |
| Chicken | 6 | $2.29–$5.99 |
| Fries | 2 | $2.89–$3.49 |
| Sides | 4 | $1.00–$2.99 |
| Wraps | 2 | $2.99–$3.99 |
| Breakfast | 1 | $2.79 |
| Pasta | 2 | $7.99–$8.99 |
| Sandwich | 1 | $4.79 |

### 6. Data Quality Notes

- `order_details_id`: 100% valid, 0% error, 0% empty — clean primary key
- `item_id`: ~1% null after NULL string replacement — minor data quality issue in source
- `price`: 99% valid — corresponds to the ~1% null item_ids (unmatched joins)
- `order_date`: Required locale-specific parsing (`en-US`) — source date format inconsistency

---

## 📉 Trend Analysis (Line Chart — Price Over Time)

The line chart shows two items tracked over time:
- **Higher trend line** (~$4–$13): Item with volatile, increasing price pattern — likely a premium burger
- **Lower flat line** (~$2–$3): Consistent low-price item (likely a side or beverage)

The upward trajectory in the higher line suggests **either seasonal pricing or a higher-value item gaining frequency** toward later dates.

---

## 🛠️ Tools & Technologies

- **Microsoft Excel** — Source data storage
- **Power Query (M Language)** — ETL pipeline, data cleaning, feature engineering
- **Power BI** — Dashboard visualization
- **DAX** — KPI measures (Total Sales, AOV, Avg per Day)

---

## 📌 Key Takeaways

- **Evening is the golden hour** — prioritize staffing and promotions 5 PM–9 PM
- **Workday traffic dominates** — office/commuter customers are the core segment
- **Apple Slices punch above their price** — highest volume in Sides despite $1.00 price point
- **Data pipeline is reproducible** — full ETL in Power Query with documented M steps
