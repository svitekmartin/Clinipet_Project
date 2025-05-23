## 1. Cleaning Data and Creating Aggregated Tables
-  Clean  missing/wrong  values  in  reg_cards
```sql 
SELECT

patient_id,

owner_id,

TRIM(REGEXP_REPLACE(owner_name, r'\b(Dr\.?|PhD|Prof\.?|MD)\b', ''))  AS  owner_name,

pet_type,

-- Replace missing breed with 'Unknown'

IFNULL(breed,'Unknown')  AS  breed,

-- Make patient_names consistent

UPPER(SUBSTR(patient_name, 1, 1)) || LOWER(SUBSTR(patient_name, 2))  AS  patient_name,

gender,

patient_age,

date_registration,

-- Remove non-numeric values from owner_phone

REGEXP_REPLACE(owner_phone, r'[^0-9]', '')  AS  owner_phone

  

FROM  `martins-test-laut-campus.health_tail.healthtail_reg_cards`

```
 
## 2. Creating Aggregated Table
-  Creating med_audit

```
 CREATE OR REPLACE TABLE `martins-test-laut-campus.health_tail_consumer.med_audit` AS

-- CTE: Aggregate amount of sold medicine
WITH tot_sold_medicine AS (
  SELECT
    DATE_TRUNC(visit_datetime, MONTH) AS month,
    med_prescribed,
    ROUND(SUM(med_dosage), 2) AS all_med_dosage
  FROM
    `martins-test-laut-campus.health_tail.visits`
  GROUP BY
    month, med_prescribed
),

-- CTE: Aggregate sold medicine by joining tot_sold_medicine and invoice
all_medicine_used AS (
  SELECT
    month,
    med_name,
    SUM(all_med_dosage * packs) AS packs,
    SUM(all_med_dosage * price) AS total_value,
    "medicine_out" AS medicine_movement
  FROM
    tot_sold_medicine AS t
  INNER JOIN
    `martins-test-laut-campus.health_tail.invoices` AS i
  ON
    t.med_prescribed = i.med_name
  GROUP BY
    month, med_name
),

-- CTE: Aggregate bought medicine 
medicine_in AS (
  SELECT
    DATE_TRUNC(month_invoice, MONTH) AS month,
    med_name,
    SUM(packs) AS packs,
    SUM(total_price) AS total_value,  -- renaming to match the other CTE
    "medicine_in" AS medicine_movement
  FROM
    `martins-test-laut-campus.health_tail.invoices`
  GROUP BY
    month, med_name
)

-- Combine usage and purchase into one table
SELECT * FROM all_medicine_used
UNION ALL
SELECT * FROM medicine_in;

```
