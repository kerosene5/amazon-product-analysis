# Exploratory Data Analysis

Let us proceed with the EDA of the dataset to get a thorough feel and familiarize ourselves with the data!

## Database Exploration

So, since this is a completely new dataset and we have no idea what we're in for, 
we first perform Database Exploration to understand what tables there are in the database (in this case, there is only 1 table).

 ```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_SCHEMA = 'amazon_sales';
```

This query helps to understand what columns there in our table:
 ```sql
SELECT * FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'products';
```
---

## Dimension Exploration

First, let's explore all categories and subcategories our products are in!

 ```sql
SELECT
	SUBSTRING_INDEX(category, '|', 1) as main_category,
    SUBSTRING_INDEX(SUBSTRING_INDEX(category, '|', 2), '|', -1) as sub_category,
    product_name
FROM products
ORDER BY 1, 2, 3;
```

| main_category           | sub_category              | product_name |
|------------------------|--------------------------|--------------|
| Car&Motorbike          | CarAccessories           | Refair AX30 [MAX] Portable Air Purifier for Car, ... |
| Computers&Accessories  | Accessories&Peripherals  | Agaro Blaze USBA to micro +Type C 2in1 Braide... |
| Computers&Accessories  | Accessories&Peripherals  | AirCase Protective Laptop Bag Sleeve fits Upto ... |
| ...                    | ...                      | ...          |

---

Now, let's find the total number of unique categories, products and reviewers:

```sql
SELECT 
	COUNT(DISTINCT product_name) as unique_products,
    COUNT(DISTINCT category) as unique_category,
    COUNT(DISTINCT user_id) as unique_reviewers
FROM products;
```

| unique_products | unique_category | unique_reviewers |
|-----------------|----------------|------------------|
| 1337            | 211            | 1194             |

---

## Measures Exploration

Let's get insights on the total number of products, the average prices (discounted and actual) and the average discount %.

I'm going to include the average rating across all products as well.

```sql
SELECT
	COUNT(*) as total_products,
	ROUND(AVG(discounted_price_num),2) as avg_discounted_price,
    ROUND(AVG(actual_price_num),2) as avg_actual_price,
    ROUND(AVG(discount_pct_num),2) as avg_discount,
    ROUND(AVG(rating_num),2) as avg_rating
FROM products;
```

| total_products | avg_discounted_price | avg_actual_price | avg_discount | avg_rating |
|---------------|---------------------|------------------|--------------|------------|
| 1465          | 3125.31             | 5444.99          | 47.69        | 4.10       |

---

## Magnitude Exploration

For magnitiude explroation, we usually group a measure by a dimension, so as to find the magnitude of each values in the dimension.

For example, let's find  total number of products for each category!

```sql
SELECT
	SUBSTRING_INDEX(category, '|', 1) as main_category,
	COUNT(product_name) as product_count
FROM products
GROUP BY main_category
ORDER BY product_count DESC;
```

| main_category           | product_count |
|------------------------|---------------|
| Electronics            | 526           |
| Computers&Accessories  | 453           |
| Home&Kitchen           | 448           |
| OfficeProducts         | 31            |
| MusicalInstruments     | 2             |
| ...                    | ...           |

Looks like Electronics is the most dominant category, having the most number of products!

---

Next, let's find the average price of products for each category:
```sql
SELECT
	SUBSTRING_INDEX(category, '|', 1) as main_category,
    ROUND(AVG(discounted_price_num),2) as avg_price
FROM products
GROUP BY main_category
ORDER BY avg_price DESC;
```

| main_category           | avg_price |
|------------------------|----------:|
| Electronics            | 5965.89   |
| Car&Motorbike          | 2339.00   |
| Home&Kitchen           | 2330.62   |
| Health&PersonalCare    | 899.00    |
| Computers&Accessories  | 842.65    |
| ...                    | ...       |

Here, we again see Electronics as the category having the highest average price among all categories.

---

## Top N - Bottom N Analysis

Top N analysis usually consists of ranking the out of magnitude explorations to find the best performing or most popular category / product.

Bottom N analysis is the reciprocal of the above.

Top N : Find Top 5 Products with the highest rating (product credibility criteria is `>= 5000` ratings)
```sql
SELECT
	product_name,
    rating_num,
    rating_count_num
FROM products
where rating_count_num > 5000
GROUP BY product_name, rating_num, rating_count_num
ORDER BY rating_num DESC, rating_count_num DESC
LIMIT 5;
```

| product_name                                                         | rating_num | rating_count_num |
|----------------------------------------------------------------------|-----------:|-----------------:|
| Swiffer Instant Electric Water Heater Faucet Ta...                   | 4.8        | 53803            |
| Spigen EZ Fit Tempered Glass Screen Protector ...                    | 4.7        | 7779             |
| Sony Bravia 164 cm (65 inches) 4K Ultra HD Sm...                     | 4.7        | 5935             |
| Redgear MP35 Speed-Type Gaming Mousepad (...                         | 4.6        | 33434            |
| Spigen EZ Fit Tempered Glass Screen Protector ...                    | 4.6        | 26603            |


---

Now, let's find the bottom 5 Products with the lowest ratings (same credibility criteria)
```sql
SELECT
	product_name,
    rating_num,
    rating_count_num
FROM products
where rating_count_num > 5000
GROUP BY product_name, rating_num, rating_count_num
ORDER BY rating_num ASC, rating_count_num DESC
LIMIT 5;
```

| product_name                                                         | rating_num | rating_count_num |
|----------------------------------------------------------------------|-----------:|-----------------:|
| Wecool Nylon Braided Multifunction Fast Chargi...                    | 3.3        | 9792             |
| PRO365 Indo Mocktails/Coffee Foamer/Cappuc...                        | 3.3        | 8427             |
| LS LAPSTER Quality Assured Universal Silicone 1...                   | 3.3        | 5692             |
| Canon PIXMA MG2577s All-in-One Inkjet Colour ...                     | 3.4        | 12185            |
| Zebronics ZEB-90HB USB Hub, 4 Ports, Pocket S...                     | 3.4        | 9385             |
