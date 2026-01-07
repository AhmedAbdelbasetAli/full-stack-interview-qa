Use `DENSE_RANK()` over `score` in descending order so ties share the same rank and there are no gaps between ranks.

### SQL with window function (MySQL 8+)

```sql
SELECT
  score,
  DENSE_RANK() OVER (ORDER BY score DESC) AS `rank`
FROM Scores
ORDER BY score DESC;
```


### SQL without window functions (older MySQL)

```sql
SELECT
  s.score,
  (
    SELECT COUNT(DISTINCT s2.score)
    FROM Scores s2
    WHERE s2.score > s.score
  ) + 1 AS `rank`
FROM Scores s
ORDER BY s.score DESC;
```
