# Mobile Game A/B Testing Data Analysis
Cookie Cats Mobile Game Data Analysis Using SQL & Tableau

1. Missing values check

```sql
SELECT 
    SUM(CASE WHEN cookie_cats.userid IS NULL THEN 1 ELSE 0 END) AS missing_userid,
    SUM(CASE WHEN cookie_cats.version IS NULL THEN 1 ELSE 0 END) AS missing_version,
    SUM(CASE WHEN cookie_cats.sum_gamerounds IS NULL THEN 1 ELSE 0 END) AS missing_gamerounds,
    SUM(CASE WHEN cookie_cats.retention_1 IS NULL THEN 1 ELSE 0 END) AS missing_ret1,
    SUM(CASE WHEN cookie_cats.retention_7 IS NULL THEN 1 ELSE 0 END) AS missing_ret7
FROM CookieCats.cookie_cats;
```

| Row | missing_userid | missing_version | missing_gamerounds | missing_ret1 | missing_ret7 |
|-----|----------------|----------------|------------------|--------------|--------------|
| 1   | 0              | 0              | 0                | 0            | 0            |


2. Duplicate users

```sql
SELECT userid, COUNT(*) 
FROM CookieCats.cookie_cats
GROUP BY userid
HAVING COUNT(*) > 1;
```

|There is no data to display.|


3 Check for leading/trailing spaces in string columns

```sql
SELECT SUM(
    CASE
      WHEN TRIM(cookie_cats.version) != cookie_cats.version THEN 1
      ELSE 0
      END)
    AS leading_trailing_spaces_version
FROM CookieCats.cookie_cats AS cookie_cats;
```
| Row | leading_trailing_spaces_version |
|-----|--------------------------------|
| 1   | 0                              |


4 Basic statistical summary and outlier detection

```sql
SELECT 
    MIN(sum_gamerounds) AS min_rounds,
    MAX(sum_gamerounds) AS max_rounds,
    AVG(sum_gamerounds) AS avg_rounds
FROM CookieCats.cookie_cats;
```

| Row | min_rounds | max_rounds | avg_rounds        |
|-----|------------|------------|-----------------|
| 1   | 0          | 49854      | 51.872456729757211 |


```sql
SELECT 
  PERCENTILE_CONT(sum_gamerounds, 0.95) OVER() AS p95
FROM CookieCats.cookie_cats
LIMIT 1;
```
| Row | p95   |
|-----|-------|
| 1   | 221.0 |



ANALYSIS

Retention

```sql
WITH filtered AS (
  SELECT *
  FROM CookieCats.cookie_cats
  WHERE sum_gamerounds <= 221 -- 95th percentile cutoff
)
SELECT 
    version,
    ROUND(AVG(CASE WHEN retention_1 = TRUE THEN 1 ELSE 0 END) * 100, 2) AS day1_retention_rate,
    ROUND(AVG(CASE WHEN retention_7 = TRUE THEN 1 ELSE 0 END) * 100, 2) AS day7_retention_rate
FROM filtered
GROUP BY version;
```
| Row | version  | day1_retention_rate | day7_retention_rate |
|-----|---------|-------------------|-------------------|
| 1   | gate_30 | 42.22             | 15.45             |
| 2   | gate_40 | 41.66             | 14.63             |
