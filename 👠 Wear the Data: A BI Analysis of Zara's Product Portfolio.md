# 👠 Wear the Data: A BI Analysis of Zara's Product Portfolio

This BI project starts from the following business challenge: Zara needs to optimize its product portfolio performance while maximizing promotion effectiveness across different clothing types.   

For this reason, this case study will revolve around the following key questions:

- How can Zara maximize promotional effectiveness across its current product portfolio?
- How should Zara optimize its product portfolio for maximum sales impact?  

## A. Exploratory Analysis
This initial part is intended to get a better understanding of the dataset scrapped from Kaggle.

### 1A. How many unique products exists in the dataset?

```sql 
SELECT count(DISTINCT product_id) as unique_product_id
FROM zara_sales
```
Answer:

![unique pid](https://github.com/user-attachments/assets/b11648d9-307a-4a28-889b-d78c48155ea2)

Zara's product portfolio is made of 252 unique products.

***

### 2A. How many nulls in the key fields?

```sql
SELECT
  SUM(product_id IS NULL) as missing_ids,
  SUM(terms IS NULL) as missing_terms,
  SUM(price IS NULL) as missing_prices,
  SUM(sales_volume IS NULL) as missing_qty,
  SUM(promotion IS NULL) as missing_promotion,
  SUM(seasonal IS NULL) as missing_seasonal,
  SUM(section IS NULL) as missing_sex
FROM zara_sales
```

Answer:

![image](https://github.com/user-attachments/assets/2c955027-1237-4034-a940-cf1240991082)

There are no nulls in the key fields.

### 3A. How are uniques products split bewteen men and women in the dataset?

```sql
SELECT COUNT(DISTINCT product_id) as unique_product_id, section as sex
FROM zara_sales
GROUP BY sex
```

Answer:

![2a](https://github.com/user-attachments/assets/ab491b9c-0c88-4778-9e63-7648bb42d6cf)

Out of 252 unique products, 218 are for men and 34 for woman.

### 4A. Which are the existing clothing types in the dataset?

```sql
SELECT DISTINCT (terms) as clothing_type, section as sex
FROM zara_sales
ORDER BY sex
```

Answer:

![c types and sex](https://github.com/user-attachments/assets/39dd6d16-3f07-423e-9880-719427fdca71)

There are 5 different types of clothes. All of them are available for men, while women reports just sweatears as those available. 


### Key insights gained

The dataset contains 252 unique products and there are no missing value in any critical field, so we can trust its completeness.  
However, there is a strong imbalance in products offerings as 218 products are classified under the Men's section, while only 34 under Women's section.  
Moreover, all the five clothing types appear in the Men's section, whereas the Women's section includes only sweaters.  
This inbalanced assortment gives an important context the correctly interpretate the following KPIs.

## B. KPIs Analysis: Zara Portfolio Performance

### 1B. Promotional Lift Rate by Section and Terms

- Business question: Which section and term should Zara allocate more promotional budget to?
- Why is this KPI relevant ?: Although Zara isn’t as associated with heavy discounting as some competitors, fast fashion in general relies on almost endless promotions.
Tracking promotional lift rate, that is comparing the sales volume promoted vs. unpromoted within each clothing type and gender segment, can help to get a better understanding of Zara portfolio performance.
In fact, it contributes to acquire valuable insights on relative performance patterns as it quantify the incremental impact of promotions.

```sql
SELECT
  section as sex,
  terms as clothing_type,
  -- average sales volume when on promotion -- What is the average number of units sold for items that were on promotion?
  ROUND(AVG(CASE WHEN promotion = 'Yes' THEN sales_volume END),3) as avg_promo_vol,
  -- average sales volume when not on promotion -- What is the average number of units sold for items that were NOT on promotion?
  ROUND(AVG(CASE WHEN promotion = 'No' THEN sales_volume END),3) as avg_nonpromo_vol,
  -- promotional lift rate = (avg_promo_vol / avg_nonpromo_vol) - 1 -- To what extent do average units sold increase (or decrease) when those items are on promotion versus when they are not?
  ROUND(AVG(CASE WHEN promotion = 'Yes' THEN sales_volume END)
    / NULLIF(AVG(CASE WHEN promotion = 'No' THEN sales_volume END), 0)
    - 1,
    3
  ) as promotional_lift_rate
FROM zara_sales
GROUP BY section,terms
ORDER BY promotional_lift_rate DESC
```

Answer:

![image](https://github.com/user-attachments/assets/1959a942-ff87-4e27-8026-509a8c2227b1)

The more negative the lift, the worse a promotion performs vs full-price.  
Within Zara's portfolio, Women's sweaters are clearly promo winners, as a promotion lift rate of 0.234 means that those in promotion are sold 23.4 % times more compared to the full-price ones. A positive lift rate is also seen for Men's sweaters.  
However, a moderate negative promotional lift rate is showed in all the other clothing type for men, meaning that those in promotion are sold less than full price items, with the worst performer being Men's t-shirts with a promotional lift rate of -0.125.  
This KPI clearly reveals that most of the promotions are failing to attrack new buyers as they present a negative lift rate.  
In this sense, Zara should pull back discount's on certain men's jackets, shoes, jeans, and t shirts, rethinking new ways to increase sales. Moreover, Zara should prioritize promo bugdet allocation in Women's sweaters slice as it has proven to deliver a significant boost in sales volume.

### 2B. Efficiency flag 

- Business question: Which terms is Zara wasting promotional budget on?
- Why is this KPI relevant ?: This KPI is related to the previous one and the marketing concept of “promotion fatigue”, namely when customers become overwhelmed by the number of promotions and marketing communications that learn to wait for markdown, generally reducing the engagement with the brand. By comparing promotion penetration (the ratio between promoted items and total items) and the previously computed promotional lift rate within clothing type and gender, we could get a better picture of which product categories are over-promoted but are actually underperforming.

```sql
  SELECT
  terms as clothing_type,
  section as sex,
  -- total number of clothes
  COUNT(*) as total_clothes,
  -- number of clothes on promotion
  SUM(CASE WHEN promotion = 'Yes' THEN 1 ELSE 0 END) as promo_clothes,
  -- promotion penetration: fraction of this term on promotion
  ROUND(
    SUM(CASE WHEN promotion = 'Yes' THEN 1 ELSE 0 END) * 1.0
    / COUNT(*),
    3
  ) as promotion_penetration,
  -- promotional lift rate = (avg_promo_vol / avg_nonpromo_vol) - 1
  ROUND(
    AVG(CASE WHEN promotion = 'Yes' THEN sales_volume END)
    / NULLIF(
        AVG(CASE WHEN promotion = 'No' THEN sales_volume END),
        0
      )
    - 1,
    3
  ) as promotional_lift_rate,
  -- efficiency flag: high penetration (>40%) but low lift (<10%)
  CASE
    WHEN 
      (SUM(CASE WHEN promotion = 'Yes' THEN 1 ELSE 0 END) * 1.0 / COUNT(*)) > 0.4
      AND
      (AVG(CASE WHEN promotion = 'Yes' THEN sales_volume END)
       / NULLIF(AVG(CASE WHEN promotion = 'No' THEN sales_volume END), 0)
       - 1) < 0.10
    THEN 'Overpromoted'
    ELSE 'OK'
  END as efficiency_flag
FROM zara_sales
GROUP BY terms,section
ORDER BY promotion_penetration DESC
```

Answer:

![image](https://github.com/user-attachments/assets/cb856da8-57d8-4c7e-9257-9a729870a938)

The results show that Men's shoes, jackets, and t-shirts are very promoted but report a low lift, resulting in discounting that deliver no incremental value, as almost half of these three items on these clothing types are on promotion but they all report a negative lift rate.  
On the opposite, promotions on Women's sweaters deliver strong incremental sales as well as Men's sweaters which shows a both a positive promotional penetration and lift rate despite cannote be listed as over-promoted due to a moderate penetration rate (0.143).  
Finally, despite cannot be defined as over-promoted due to a promotional penetration slight under 0.40, Men's jeans still show a negative relationship bewteen items on promotion and the value they deliver.  
Coupled with the previous KPI, this one confirms that Zara's endless and massive discount culture is driving ambigous results on sales volume, with the notable expection of Women's sweaters. Furthermore, men's sweater slice is worth keeping under observation, testing if incrementally increase promotions lead to lift rate to scale further.

### 3B. Product Performance Quartiles

- Business question: Which specific products should Zara discontinue, promote more, or expand?
- Why is this KPI relevant ?: This KPI aims to divide the product portfolio of Zara in 4 performance quartiles based on the revenue of each items (sales volume * price). This would get valuable data on high-performance and low-performance products, giving insights to better understand revenue concentration.

```sql
WITH sales_by_product as (
  SELECT
    terms as clothing_type,
    product_id,
    name as product_name,
    section as gender,
    SUM(price * sales_volume) as revenue,
    SUM(price * sales_volume)
      / NULLIF(SUM(sales_volume), 0) as revenue_per_piece
  FROM zara_sales
  GROUP BY
    terms,
    product_id,
    section,
    name
)
SELECT
gender,
clothing_type,
product_id,
revenue_per_piece,
product_name,
revenue,
NTILE(4) OVER (PARTITION BY clothing_type ORDER BY revenue DESC) AS performance_quartile
FROM sales_by_product
ORDER BY
performance_quartile 
```

Answer:










