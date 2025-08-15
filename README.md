# Mobile Game A/B Testing Data Analysis
Cookie Cats Mobile Game Data Analysis Using SQL & Tableau

-- 1. Missing values check

SELECT 
    SUM(CASE WHEN cookie_cats.userid IS NULL THEN 1 ELSE 0 END) AS missing_userid,
    SUM(CASE WHEN cookie_cats.version IS NULL THEN 1 ELSE 0 END) AS missing_version,
    SUM(CASE WHEN cookie_cats.sum_gamerounds IS NULL THEN 1 ELSE 0 END) AS missing_gamerounds,
    SUM(CASE WHEN cookie_cats.retention_1 IS NULL THEN 1 ELSE 0 END) AS missing_ret1,
    SUM(CASE WHEN cookie_cats.retention_7 IS NULL THEN 1 ELSE 0 END) AS missing_ret7
FROM CookieCats.cookie_cats;
