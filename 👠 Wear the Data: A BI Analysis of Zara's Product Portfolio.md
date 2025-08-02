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

### 3A. How are uniques products split bewteen men and women in the dataset and what are the existing clothing types in the dataset?

```sql
SELECT DISTINCT terms as clothing_type, COUNT(terms) as num_clothing_type, section as sex
FROM zara_sales
GROUP BY terms, section
ORDER BY num_clothing_type DESC
```

Answer:

![image](https://github.com/user-attachments/assets/e67e0f19-1d74-470d-ad00-2de7e6a19f40)


Out of 252 unique products, just 34 for women while the rest is intended for men. There are 5 different types of clothes, and, while all of them are available for men, women reports just sweatears as those available.  
Moreover, jackets are clearly over-represented, while there are just 8 items categorized as jeans and 7 as shoes in the whole dataset.

### Key insights gained

The dataset contains 252 unique products and there are no missing value in any critical field, so we can trust its completeness.  
However, there is a strong imbalance in products offerings as 218 products are classified under the Men's section, while only 34 under Women's section.  
Moreover, all the five clothing types appear in the Men's section, whereas the Women's section includes only sweaters, and the some clothing type are clearly over-represented.
This inbalanced assortment gives an important context the correctly interpretate the following KPIs.

## B. KPIs Analysis: Zara Portfolio Performance

### 1B. Promotional Lift Rate by Section and Terms

- Business question: Which section and term should Zara allocate more promotional budget to?
- Why is this KPI relevant ?: Although Zara isn’t as associated with heavy discounting as some competitors, fast fashion in general relies on almost endless promotions.
Tracking promotional lift rate, that is comparing the sales volume promoted vs. unpromoted within each clothing type and gender segment, can help to get a better understanding of Zara portfolio performance.
In fact, it contributes to acquire valuable insights on relative performance patterns as it quantify the incremental impact of promotions.

```sql
SELECT
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
GROUP BY terms
ORDER BY promotional_lift_rate DESC
```

Answer:

<img width="546" height="141" alt="image" src="https://github.com/user-attachments/assets/dd626bb6-4727-4027-9a43-a9feb42814a8" />

The more negative the lift, the worse a promotion performs vs full-price.  
Within Zara's portfolio, sweaters are clearly promo winners, as a promotion lift rate of 0.222 means that those in promotion are sold 22.2 % times more compared to the full-price ones.  
However, a moderate negative promotional lift rate is showed in all the other clothing type for, meaning that those in promotion are sold less than full price items, with the worst performer being t-shirts with a promotional lift rate of -0.125.  
This KPI clearly reveals that most of the promotions are failing to attrack new buyers as they present a negative lift rate.  
In this sense, Zara should pull back discount's on certain jackets, shoes, jeans, and t shirts, rethinking new ways to increase sales. Moreover, the company should prioritize promo bugdet allocation to sweaters slice as it has proven to deliver a significant boost in sales volume.

### 2B. Efficiency flag 

- Business question: Which terms is Zara wasting promotional budget on?
- Why is this KPI relevant ?: This KPI is related to the previous one and the marketing concept of “promotion fatigue”, namely when customers become overwhelmed by the number of promotions and marketing communications that learn to wait for markdown, generally reducing the engagement with the brand. By comparing promotion penetration (the ratio between promoted items and total items) and the previously computed promotional lift rate within clothing type and gender, we could get a better picture of which product categories are over-promoted but are actually underperforming.

```sql
SELECT
  terms as clothing_type,
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
GROUP BY terms
ORDER BY promotion_penetration DESC
```

Answer:

<img width="777" height="140" alt="image" src="https://github.com/user-attachments/assets/464d1e9e-216d-4b24-aa5b-03a822f25b65" />

The results show that shoes, jackets, and t-shirts are very promoted but report a low lift, resulting in discounting that deliver no incremental value, as almost half of these three items on these clothing types are on promotion but they all report a negative lift rate.  
On the opposite, promotions on sweaters deliver strong incremental sales and shows a positive promotional lift rate, a unique trait across the five clothing types.  
Finally, despite cannot be defined as over-promoted due to a promotional penetration slight under 0.40, jeans still show a negative relationship bewteen items on promotion and the value they deliver.  
Coupled with the previous KPI, this one confirms that Zara's endless and massive discount culture is driving ambigous results on sales volume, indicating a waste of promotional budget on shoes, jackets, and t shirts. Within this landscape, the notable expection is represented by sweaters. 

### 3B. Product Performance Quartiles

- Business question: Where is Zara revenue concentration?
- Why is this KPI relevant ?: This KPI aims to divide the product portfolio of Zara in 4 performance quartiles based on the revenue of each items (sales volume * price), blending volume, margin, and relative performace in a single view. In this sense, it gives answers to questions revolving around top vs. bottom performers, revenue insights, and gender comparisons. Coupled with the two previous KPIs, it paves the way for actionable steps that might include boosting or levelling off promotions, rolling out new selling tactics for certains products or considering discountinuing very lowest performers.

```sql
WITH sales_by_product as (
  SELECT
    terms as clothing_type,
    product_id,
    name as product_name,
    section as gender,
-- total revenue per product
    SUM(price * sales_volume) as revenue,
-- average price per sold unit
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
-- dividing the whole dataset into four equal-sized buckets by descending revenue
NTILE(4) OVER (ORDER BY revenue DESC) AS performance_quartile
FROM sales_by_product
ORDER BY
performance_quartile 
```

Answer:

![image](https://github.com/user-attachments/assets/9c0e5f29-aea5-479e-a276-a0dc0d13ae0a)

![image](https://github.com/user-attachments/assets/1edc58ba-85de-4e67-b478-bf09f437af49)

![image](https://github.com/user-attachments/assets/cbb2c88f-5c79-4ef3-8df8-f49a34ff5d5b)

![image](https://github.com/user-attachments/assets/edbf1289-76ec-4a52-9473-b66e11fe7a7e)

As this table spans over 252 different products - capturing every product quartile, revenue, and price - it remains user friendly when displayed in Power BI in the report, giving the possibility to filtering and slicing across all categories and performing aggregation to get a better understanding of the product's performance on the market.  
However, from a first view, it can be noticed that product most of the revenues of Zara's portfolio lay in the very profitable slice of men's jacket, being  over-represented in the first and second quartile, and topping the table of most priced products.  
On the other hand, the other two quartiles present a more diluted composition.

### 4B. Portfolio Diversity Score

- Business question: Is Zara too dependent on certain categories, creating a potential business risk?
- Why is this KPI relevant ?: This KPI can be thought of as the step forward of the previous one. By computing, for every combination of seasonal and promotional flag, the total revenue and its share of overall and clothing type-level revenue, Zara would expand its knowledge of which combination of term, seasonal, promotion (slice) drives the revenue. Coupled with the Product Performance Quartiles, these KPIs aim to disclose over- and under-invested areas within Zara’s portfolio.

```sql
WITH revenue_slices AS (
  SELECT
    terms as clothing_type,        
    seasonal,                                              
    promotion,                                             
    SUM(price * sales_volume) as revenue
  FROM zara_sales
  GROUP BY
    terms,
    seasonal,
    promotion
)
SELECT
  clothing_type,
  seasonal,
  promotion,
  revenue,
  -- share of all Zara revenue
  ROUND(
    100.0 * revenue
    / SUM(revenue) OVER (),
    2
  ) as share_of_total_revenue,
  -- share of revenue within this clothing_type
  ROUND(
    100.0 * revenue
    / SUM(revenue) OVER (PARTITION BY clothing_type),
    2
  ) as share_of_term_revenue
FROM revenue_slices
ORDER BY clothing_type ASC
```

Answer:

![image](https://github.com/user-attachments/assets/e8b8d97c-6b3f-4f77-803c-318488883111)

As expected due to their over-rapresentation in the dataset, men's jacktes constitues the four highest sources of revenue for Zara, accounting for over 68% of it regardless of the slices. In this sense, Zara appears to be extremely dependent on this clothing type, resulting in an enormous revenue loss if a outwear trends or a supply chain issue would hit.  
On the other hand, slice-level fragmentation whithin clothing type looks very fragmented as no single "seasonal x promotion" slice clearly stands out as the most profitable combination compared to the others, describing a stable portfolio management that is not overly sensitive to timing or discounting.  
Generally speaking, these results seem to show that there is no magic slice Zara can pull to outperform the rest and promo sales are not cannibalizing full-price volume, creating room for experiment in one slice without major risks of drop in overall category revenue due to a huge dependence on a certain slice. 
Finally, apart from peak of revenues constituted by jackets due to their over-rapresentation, Zara does not seem to present layer of specific high profitability, suggesting that real profit drives may lay in different segments, such as regional market or specific styles.

















