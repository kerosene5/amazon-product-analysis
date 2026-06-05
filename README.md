# Overview

![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=flat&logo=mysql&logoColor=white)
![Dataset](https://img.shields.io/badge/Dataset-Kaggle-20BEFF?style=flat&logo=kaggle&logoColor=white)
![Records](https://img.shields.io/badge/Records-1%2C337-brightgreen?style=flat)
![Status](https://img.shields.io/badge/Status-Complete-success?style=flat)

> A structured SQL-based analysis of 1,337 Amazon India products where I explore pricing, discounts, ratings, and category performance using EDA and Advanced Analytics techniques.

## Objective

The main goal is to:
- Understand the structure and quality of real-world data
- Perform meaningful analysis using SQL
- Derive actionable insights related to products, pricing, and customer behavior

---

This project aims to answer business questions about Amazon's product catalog:
- Which product categories dominate the platform?
- What is the relationship between discount percentage and customer rating?
- Which products and categories deliver the best value?
- How are products distributed across price and discount segments?
- What does the top-performing vs. bottom-performing product landscape look like?

## Dataset

**Source:** [Amazon Sales Dataset (from Kaggle)](https://www.kaggle.com/datasets/karkavelrajaj/amazon-sales-dataset/data)

This dataset contains 1,000+ Amazon product listings with ratings and reviews as per product information listed on [Amazon India](https://www.amazon.in/)

| Column | Description |
|---|---|
| `product_id` | Unique product identifier |
| `product_name` | Name of the product |
| `category` | Hierarchical category path |
| `discounted_price` | Selling price (₹) |
| `actual_price` | MRP / original price (₹) |
| `discount_percentage` | Discount offered (%) |
| `rating` | Average customer rating (out of 5) |
| `rating_count` | Number of ratings received |
| `about_product` | Product description |
| `user_id` / `user_name` | Reviewer identifiers |
| `review_id` / `review_title` / `review_content` | Review details |

---

## Framework

The analysis follows this structured mind map:

![Analysis Framework](https://github.com/user-attachments/assets/8008fb3b-d496-4e1f-9539-261bcf3837b4)
