# Advanced Analytics

## Ranking with Window Functions

Top Product per Category

```sql
WITH ranked AS (
    SELECT
        product_name,
        SUBSTRING_INDEX(category, '|', 1) AS main_category,
        rating_num,
        rating_count_num,
        DENSE_RANK() OVER (
            PARTITION BY SUBSTRING_INDEX(category, '|', 1)
            ORDER BY rating_num DESC
        ) AS rnk
    FROM products
    WHERE rating_count_num >= 50
)
SELECT * FROM ranked WHERE rnk = 1;
```

## Performance Analysis

Above vs. Below Average Rating

```sql
WITH category_avg AS (
    SELECT
        SUBSTRING_INDEX(category, '|', 1) AS main_category,
        ROUND(AVG(rating_num), 2)          AS avg_category_rating
    FROM products
    GROUP BY main_category
)
SELECT
    p.product_name,
    SUBSTRING_INDEX(p.category, '|', 1)  AS main_category,
    p.rating_num,
    ca.avg_category_rating,
    CASE
        WHEN p.rating_num >= ca.avg_category_rating THEN 'Above Average'
        ELSE 'Below Average'
    END AS performance
FROM products p
JOIN category_avg ca
    ON SUBSTRING_INDEX(p.category, '|', 1) = ca.main_category
ORDER BY main_category, performance;
```

## Part-to-Whole 

Category Share of Total Products

```sql
SELECT
    SUBSTRING_INDEX(category, '|', 1)                        AS main_category,
    COUNT(*)                                                  AS product_count,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 2)       AS pct_of_total
FROM products
GROUP BY main_category
ORDER BY pct_of_total DESC;
```


## Data Segmentation 

Discount Tiers

```sql
SELECT
    CASE
        WHEN discount_pct_num < 20  THEN 'Low (< 20%)'
        WHEN discount_pct_num < 50  THEN 'Mid (20–49%)'
        WHEN discount_pct_num < 70  THEN 'High (50–69%)'
        ELSE 'Very High (70%+)'
    END                         AS discount_tier,
    COUNT(*)                    AS product_count,
    ROUND(AVG(rating_num), 2)   AS avg_rating,
    ROUND(AVG(rating_count_num), 0) AS avg_reviews
FROM products
WHERE discount_pct_num IS NOT NULL
GROUP BY discount_tier
ORDER BY MIN(discount_pct_num);
```

## Cumulative Analysis 

Running Total of Products by Category

```sql
SELECT
    main_category,
    product_count,
    SUM(product_count) OVER (ORDER BY product_count DESC) AS running_total
FROM (
    SELECT
        SUBSTRING_INDEX(category, '|', 1) AS main_category,
        COUNT(*) AS product_count
    FROM products
    GROUP BY main_category
) category_counts;
```

## Reporting — Executive Summary

```sql
WITH summary AS (
    SELECT
        SUBSTRING_INDEX(category, '|', 1)       AS main_category,
        COUNT(*)                                 AS total_products,
        ROUND(AVG(discounted_price_num), 2)      AS avg_price,
        ROUND(AVG(discount_pct_num), 2)          AS avg_discount,
        ROUND(AVG(rating_num), 2)                AS avg_rating,
        SUM(rating_count_num)                    AS total_reviews
    FROM products
    GROUP BY main_category
)
SELECT *,
    DENSE_RANK() OVER (ORDER BY avg_rating DESC)    AS rating_rank,
    DENSE_RANK() OVER (ORDER BY total_reviews DESC) AS review_rank
FROM summary
ORDER BY total_products DESC;
```
