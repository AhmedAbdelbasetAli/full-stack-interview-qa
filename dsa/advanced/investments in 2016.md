The query needs to sum tiv_2016 for rows where tiv_2015 is duplicated among policyholders, and (lat, lon) is unique, then round to 2 decimals.[1][2]

## SQL solution (MySQL)

```sql
SELECT
    ROUND(SUM(tiv_2016), 2) AS tiv_2016
FROM Insurance
WHERE tiv_2015 IN (
    SELECT tiv_2015
    FROM Insurance
    GROUP BY tiv_2015
    HAVING COUNT(*) > 1
)
AND (lat, lon) IN (
    SELECT lat, lon
    FROM Insurance
    GROUP BY lat, lon
    HAVING COUNT(*) = 1
);
```


This query:
- Filters tiv_2015 to values that appear more than once among policyholders.[2][1]
- Filters locations to coordinate pairs (lat, lon) that occur exactly once, ensuring unique cities.[1][2]
- Sums tiv_2016 over the remaining rows and rounds to two decimal places, as required.[3][1]

[20](https://www.dsfaisal.com/articles/sql/leetcode-sql-problem-solving)
