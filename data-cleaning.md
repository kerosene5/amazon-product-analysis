# Data Cleaning

The raw CSV file requires encoding fixes (`₹` symbol causes `charmap` codec errors in MySQL Workbench)

```python
import pandas as pd
df = pd.read_csv('amazon.csv', encoding='latin1')
for col in df.select_dtypes(include='object').columns:
    df[col] = df[col].astype(str).str.encode('ascii', errors='ignore').str.decode('ascii')
df.to_csv('amazon_clean.csv', index=False, encoding='utf-8')
```

After importing `amazon_clean.csv` in MySQL Workbench, numeric columns were extracted from raw string fields (for example, `₹1,099` is changed to `1099.00`):

```sql
UPDATE products SET
    discounted_price_num = CAST(NULLIF(REPLACE(REPLACE(discounted_price, '?', ''), ',', ''), '') AS DECIMAL(10,2)),
    actual_price_num     = CAST(NULLIF(REPLACE(REPLACE(actual_price, '?', ''), ',', ''), '') AS DECIMAL(10,2)),
    discount_pct_num     = CAST(NULLIF(REPLACE(discount_percentage, '%', ''), '') AS DECIMAL(5,2)),
    rating_num           = CAST(NULLIF(REPLACE(REPLACE(rating, '|', ''), ',', ''), '') AS DECIMAL(3,1)),
    rating_count_num     = CAST(NULLIF(TRIM(REPLACE(rating_count, ',', '')), '') AS UNSIGNED);
```

Now the dataset is ready for analysis!
