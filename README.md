# Customer Shopping Behaviour Analysis

End-to-end analytics project on retail customer data. Python cleans the raw file, SQL Server answers 13 business questions, and a Power BI dashboard presents the results.

**Business question:** How can the company use consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategy?

## Results at a Glance

| Metric | Value |
|---|---|
| Customers analysed | 3,900 |
| Total revenue | $233,081 |
| Average purchase amount | $59.76 |
| Average review rating | 3.75 |
| Subscribers | 27.0% of customers |
| Orders that used a discount | 43.0% |
| Online (home delivery) revenue share | 83.3% |

## Data Audit & Cleaning Note

The raw file `customer_shopping_behavior.csv` holds 5,050 rows. During preprocessing, `50` exact duplicate rows and `1,100` anomalous/mixed-schema rows (`IDs 4000-5099`) were identified and removed, which leaves 3,900 valid records (Customer IDs 1 to 3900, all unique).

The 1,100 rows did not match the schema of the main dataset:

- Gender value `Other`, and products (Phone, Laptop, Watch, Headphones, Bag) filed under an `Electronics` category that exists nowhere else
- City names (New York, Los Angeles, Houston, Phoenix, Chicago) where the clean data holds US states, and `Autumn` where the clean data uses `Fall`
- Purchase amounts up to $1,499.76, against a maximum of $100 in the valid records
- About half of the purchase amounts, review ratings, and previous purchase counts missing

Keeping them would have raised total revenue from $233,081 to about $647,000. After removal, 37 missing review ratings (0.9%) were filled with the median rating of their product category.

## Tech Stack

- **Python** (pandas, SQLAlchemy): cleaning, feature engineering, load to SQL Server
- **SQL Server**: 13 analysis queries using CTEs, window functions, and `TOP ... WITH TIES`
- **Power BI**: interactive dashboard with KPI cards, charts, and slicers

## Repository Structure

```
customer shopping behaviour analysis/
├── Business Problem Document.pdf
├── customer_shopping_behavior.csv                   raw data (5,050 rows)
├── Python Implementation.ipynb                      cleaning and loading
├── SQL implementations.sql                          13 business queries
├── Behavior Dashboard.pbix                          Power BI dashboard
├── Behavior Dashboard.pdf                           dashboard export
├── Screenshots/                                     SQL query results (1.png to 13.png)
├── Customer-Shopping-Behaviour-Analysis-ppt.pdf
└── Project Report.pdf    
```

## Workflow

### 1. Python: preparation

1. Load the CSV and drop the 50 exact duplicates.
2. Keep records with `Customer ID <= 3900`, which removes the 1,100 anomalous rows.
3. Fill missing `Review Rating` values with the category median.
4. Rename columns to snake_case and shorten `Purchase Amount (USD)` to `purchase_amount`.
5. Create `age_group` from age quartiles: Young Adult (18-31), Adult (32-44), Middle-aged (45-57), Senior (58-70).
6. Create `purchase_frequency_days` from the purchase frequency label.
7. Write the result to the `CustomerDataset` table in SQL Server.

### 2. SQL: analysis

| # | Question | Result |
|---|---|---|
| 1 | Revenue by gender | Male $157,890, Female $75,191 |
| 2 | Discount users who spent above average | 839 customers |
| 3 | Top 5 products by average rating | Gloves 3.86, Sandals 3.84, Boots 3.82, Hat 3.80, Skirt 3.78 |
| 4 | Standard vs Express shipping | $58.46 vs $60.48 average |
| 5 | Subscribers vs non-subscribers | $59.49 vs $59.87 average, $62,645 vs $170,436 revenue |
| 6 | Top 5 products by discount rate | Hat 50.00%, Sneakers 49.66%, Coat 49.07%, Sweater 48.17%, Pants 47.37% |
| 7 | New, Returning, Loyal segments | 83, 701, 3,116 customers |
| 8 | Top 3 products per category | Jewelry, Blouse, Pants, Sandals, Jacket lead their categories |
| 9 | Repeat buyers who subscribe | 27.56% of 3,476 repeat buyers |
| 10 | Revenue by age group | Young Adult leads with 26.66% |
| 11 | Online vs offline channel | Online 83.30% of revenue, Offline 16.70% |
| 12 | Seasonal patterns | Fall leads with $60,018 and $61.56 average |
| 13 | Payment methods | Credit Card leads with 17.29% of revenue |

Question 3 ranks products by exact average rating. Skirt, T-shirt, and Handbag all display as 3.78 after rounding, but only Skirt is above the cutoff.

### 3. Power BI: dashboard

The dashboard has four KPI cards (customers, average purchase, average rating, total revenue), five charts, and four slicers (gender, subscription status, category, shipping type).

## Key Insights

- **Order value is flat.** Average purchase amount stays between $57 and $62 across categories, genders, channels, payment methods, and seasons. Revenue differences come from customer volume.
- **Clothing drives revenue.** It brings 44.7% of revenue, followed by Accessories at 31.8%. Outerwear is last at 8.0%.
- **Discounts do not raise spend.** Discounted orders average $59.28 against $60.13 without a discount.
- **Subscribers spend the same.** Subscription status shows no spending gap (Welch t-test p = 0.66).
- **Fall is the strongest season.** It is the only seasonal gap that passes a significance test (ANOVA p = 0.011).
- **Most customers are loyal.** 79.9% have more than 10 previous purchases.

## Business Recommendations

1. Concentrate campaigns in fall and test summer offers.
2. Run a controlled test of lower discount depth on the most-discounted products.
3. Position subscription around purchase frequency and retention, not order value.
4. Grow the female customer base (32% of customers) and build Outerwear bundles.

## How to Run

1. Place `customer_shopping_behavior.csv` next to the notebook.
2. Open `Python Implementation.ipynb`, set the SQL Server host and database in the connection cell, and run all cells.
3. Run the queries in `SQL implementations.sql` against the `CustomerDataset` table.
4. Open `Behavior Dashboard.pbix` in Power BI Desktop and refresh the data source.

## Author

**Soham Patil**
BTech AI & Data Science, Vishwakarma University
[LinkedIn](https://linkedin.com/in/soham-patil-dv)
