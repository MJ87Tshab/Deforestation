# Deforestation

## Project Overview
This project is used to highlight the key countries that were involved in Deforestation, the different income groups involved as well as countries with a high forest area per square kilometre.

## Data Sources

The datasets that were used for this analysis were:

- "Forest_Area.csv" file
- "Land_Area.csv" file
- "Region.csv" file

## Tools
- SQL Server - Data Analysis

## Data Preparation

In the initial phase, we performed the following steps:
1. We created a database(DB) named "Deforestation".
2. All 3 tables loaded on the DB


## Exploratory Data Analysis

Exploring the Forestation Data to answer the below  questions:

- Question 1: What are the total number of countries involved in deforestation? 
- Question 2: Show the income groups of countries having total area ranging from 75,000 to 150,000 square meter?
- Question 3: Calculate average area in square miles for countries in the 'upper middle income region'. Compare the result with the rest of the income categories.
- Question 4: Determine the total forest area in square km for countries in the 'high income' group. Compare result with the rest of the income categories.
- Question 5: Show countries from each region(continent) having the highest total forest areas. 


## Data Analysis


Total number of countries in Deforestation
```SQL
SELECT COUNT(*) AS Total_Number_of_Countries FROM (
SELECT country_name FROM FOREST_AREA
UNION
SELECT country_name FROM LAND_AREA
UNION
SELECT country_name FROM REGION) AS T```


- Show income groups of countries with total area between 75k - 150k sqm
```SELECT LA.Country_name, YEAR, Total_area_sq_mi, income_group FROM LAND_AREA AS LA
INNER JOIN Region as R
ON LA.country_name = R.country_name
WHERE LA.total_area_sq_mi BETWEEN '75000' AND '150000'
ORDER BY LA.country_name```


- Calculate average area in square miles for countries in the 'upper middle income region'. Compare the result with the rest of the income categories.
```SELECT AVG(total_area_sq_mi) AS Average_area, income_group FROM LAND_AREA AS LA
INNER JOIN REGION AS R
ON LA.COUNTRY_NAME = R.COUNTRY_NAME
WHERE R.Income_group IN ('upper middle income','lower middle income','low income','high income')
group by income_group
HAVING income_group <> 'null'```



- Determine the total forest area in square km for countries in the 'high income' group. Compare result with the rest of the income categories

```SELECT sum(forest_area_sqkm) AS Average_Forest_Area, income_group FROM Forest_Area AS FA
INNER JOIN Region AS R
ON FA.COUNTRY_NAME = R.COUNTRY_NAME
WHERE FA.forest_area_sqkm IS NOT NULL AND income_group IN ('High income','Low income','Upper middle income','lower middle income')
GROUP BY income_group```


- Show countries from each region(continent) having the highest total forest areas.

```WITH table_1 AS

(SELECT REGION, fa.country_name, SUM(forest_area_sqkm) AS Forest_Area, RANK() OVER (PARTITION BY REGION ORDER BY forest_area_sqkm DESC) as Rank_1 FROM Forest_Area AS FA
JOIN REGION AS R
ON FA.country_name = r.country_name
WHERE forest_area_sqkm IS NOT NULL
group by region, fa.country_name ,forest_area_sqkm)

SELECT Region, country_name, Forest_Area, Rank_1 
FROM Table_1
WHERE RANK_1 = 1 AND Region != 'World'```









