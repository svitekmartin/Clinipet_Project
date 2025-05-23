## 2. Research questions with SQL
**-- 1. What med did we spend the most money on in total?**
  
```
SELECT
  med_name,
  SUM(total_value) AS total_spend
FROM
  `martins-test-laut-campus.health_tail_consumer.med_audit`
WHERE
  medicine_movement='medicine_in'
GROUP BY
  med_name
ORDER BY
  SUM(total_value) DESC
LIMIT
  1
```
**-- 2. What med had the highest monthly total_value spent on patients? At what month?**

```
SELECT
  med_name,
  month,
  SUM(total_value) AS total_spend
FROM
  `martins-test-laut-campus.health_tail_consumer.med_audit`
GROUP BY
  med_name,
  month
ORDER BY
  SUM(total_value) DESC
```
**-- 3. What month was the highest in packs of meds spent in vet clinic?**

```
SELECT
  month,
  ROUND(SUM(packs),1) AS total_packs
FROM
  `martins-test-laut-campus.health_tail_consumer.med_audit`
WHERE
  medicine_movement='medicine_in'
GROUP BY
  month
ORDER BY
  SUM(packs) DESC
```

 **-- 4. What’s an average monthly spent in packs of the med that generated the most revenue?**
 ```
  --CTE: Identify most used medicine
WITH
  most_used_med AS (
  SELECT
    med_name,
    SUM(total_value) AS total_value
  FROM
    `martins-test-laut-campus.health_tail_consumer.med_audit`
  WHERE
    -- used = sold = stock_out
    medicine_movement='medicine_out'
  GROUP BY
    med_name
  ORDER BY
    total_value DESC
    -- limit to the most used one
  LIMIT
    1 )
  -- main query
SELECT
  med.med_name,
  ROUND(AVG(packs),2) AS avg_monthly_spent_in_packs
FROM
  `martins-test-laut-campus.health_tail_consumer.med_audit` AS med
JOIN
  most_used_med AS mu
ON
  med.med_name = mu.med_name
WHERE
  med.medicine_movement='medicine_in'
GROUP BY
  med.med_name
```
