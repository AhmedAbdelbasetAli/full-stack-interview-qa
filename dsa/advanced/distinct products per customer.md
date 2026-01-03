Use a grouped count of **distinct products per customer** and compare it to the **total number of products** in the `Product` table. Any customer whose distinct product count equals the total product count has bought all products.[1][2][3]

```sql
SELECT
    c.customer_id
FROM
    Customer AS c
GROUP BY
    c.customer_id
HAVING
    COUNT(DISTINCT c.product_key) = (
        SELECT COUNT(*) FROM Product
    );
```

- `COUNT(DISTINCT c.product_key)` ensures duplicates in `Customer` do not inflate the count.[4][1]
- The subquery `SELECT COUNT(*) FROM Product` gives the total number of products that must be purchased.[5][1]
- The `HAVING` clause filters to only those customers who bought **every** product in `Product`.[2][4]

