# Clinipet-Project
In this project, I loaded data to BigQuery on Google Cloud Platform, cleaned it, created new tables, and then connected everything to Looker Studio. The data came from Healthtail, a veterinary clinic.#

## 1. ETL Plan

-   Upload the .csv file to a BigQuery project.   
-   Clean the `healthtail_reg_cards` table using SQL, addressing inconsistencies from manual entry, and save the cleaned version as `registration_clean`.    
-   Create an aggregated table called `med_audit` to track monthly medication movement (purchases and in-clinic usage) by joining data from the `invoice` and `visits` tables.

## 2. Execute the ETL Plan

-   Clean the data in the registration form and save the cleaned output as `registration_clean`.
-   Create med_audit.
-   Save results table to BigQuery.
  
-   Answer reseach questions:

--- 1. What med did we spend the most money on in total?

---2. What med had the highest monthly total_value spent on patients? At what month?

---3. What month was the highest in packs of med spent in vet clinic?

---4. What's on average monthly spent in packs of the med that generated the most revenue?

## 3. Create interactive report in Looker Studio


