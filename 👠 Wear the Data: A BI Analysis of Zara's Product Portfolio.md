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

The dataset contains 252 unique products and there are no missing value in any critical field, so we can trust its completeness.                                                                                    However, there is a strong imbalance in products offerings as 218 products are classified under the Men's section, while only 34 under Women's section. Moreover, all the five terms appear in the Men's section, whereas the Women's section includes only sweaters.                                                                                                                                                                  This inbalanced assortment gives an important context the correctly interpretate the following KPIs.

## B. KPIs Analysis: Zara Portfolio Performance

### 1B. Promotional Lift Rate by Section and Terms

- Metric: AVG(sales_volume WHERE promotion = 'Yes') / AVG(sales_volume WHERE promotion = 'No') - 1
- Business question: Which section and term should Zara allocate more promotional budget to?
- Why is this KPI relevant ?: Although Zara is not known as much as its competitor for this policy, the fast fashion industry is well-known for its discount culture. Namely they run (almost) endless promotions all over the year. Tracking promotional lift rate by section, that is comparing the sales volume promoted vs. unpromoted items within the same section and terms, can help to get a better understanding of Zara portfolio performance, by acquiring valuable insights on relative performance patterns. 












