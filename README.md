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

I created the database(DB) named "Deforestation" where I added the three datasets.

## Exploratory Data Analysis

Exploring the Forestation Data to answer the below  questions:

- Question 1: What are the total number of countries involved in deforestation? 
- Question 2: Show the income groups of countries having total area ranging from 75,000 to 150,000 square meter?
- Question 3: Calculate average area in square miles for countries in the 'upper middle income region'. Compare the result with the rest of the income categories.
- Question 4: Determine the total forest area in square km for countries in the 'high income' group. Compare result with the rest of the income categories.
- Question 5: Show countries from each region(continent) having the highest total forest areas. 


## Data Analysis

I analysed the data to answer the below questions:



#### The total number of countries involved in Deforestation

```SQL
WITH table_1 AS 
(
SELECT * FROM 
(SELECT COUNTRY_NAME, YEAR, forest_area_sqkm,
RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY forest_area_sqkm DESC) AS COL_rank
FROM Forest_Area) table_1
WHERE COL_RANK > 1),

table_2 AS 
(
SELECT COUNTRY_NAME, YEAR, FOREST_AREA_SQKM,
LAG(forest_area_sqkm) OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR ASC) AS PREVIOUS_FA
FROM table_1),

table_3 AS 
(SELECT * FROM table_2 WHERE PREVIOUS_FA IS NOT NULL),

table_4 AS 
(SELECT *, 
CASE WHEN FOREST_AREA_SQKM >= PREVIOUS_FA THEN 'AFFORESTATION' ELSE 'DEFORESTATION' END AS FOREST_STATE
FROM table_3)

SELECT COUNT(DISTINCT COUNTRY_NAME) AS NO_COUNTRIES_INVOLVED_IN_DEFORESTATION FROM table_4
WHERE FOREST_STATE = 'DEFORESTATION'
```

#### Show income groups of countries with total area between 75k - 150k sqm

``` SQL
SELECT LA.Country_name, YEAR, Total_area_sq_mi, income_group FROM LAND_AREA AS LA
INNER JOIN Region as R
ON LA.country_name = R.country_name
WHERE LA.total_area_sq_mi BETWEEN '75000' AND '150000'
ORDER BY LA.country_name
```

#### Calculate average area in square miles for countries in the 'upper middle income region'. Compare the result with the rest of the income categories.

``` SQL
SELECT AVG(total_area_sq_mi) AS Average_area, income_group FROM LAND_AREA AS LA
INNER JOIN REGION AS R
ON LA.COUNTRY_NAME = R.COUNTRY_NAME
WHERE R.Income_group IN ('upper middle income','lower middle income','low income','high income')
group by income_group
HAVING income_group <> 'null'
```


#### Determine the total forest area in square km for countries in the 'high income' group. Compare result with the rest of the income categories

``` SQL
SELECT sum(forest_area_sqkm) AS Average_Forest_Area, income_group FROM Forest_Area AS FA
INNER JOIN Region AS R
ON FA.COUNTRY_NAME = R.COUNTRY_NAME
WHERE FA.forest_area_sqkm IS NOT NULL AND income_group IN ('High income','Low income','Upper middle income','lower middle income')
GROUP BY income_group
```

#### Show countries from each region(continent) having the highest total forest areas.

```SQL
WITH table_1 AS

(SELECT REGION, fa.country_name, SUM(forest_area_sqkm) AS Forest_Area, RANK() OVER (PARTITION BY REGION ORDER BY forest_area_sqkm DESC) as Rank_1 FROM Forest_Area AS FA
JOIN REGION AS R
ON FA.country_name = r.country_name
WHERE forest_area_sqkm IS NOT NULL
group by region, fa.country_name ,forest_area_sqkm)

SELECT Region, country_name, Forest_Area, Rank_1 
FROM Table_1
WHERE RANK_1 = 1 AND Region != 'World'
```

## Results/Findings

A key finding in this analysis is that the upper middle income group covers the highest area in square miles as well as highest forest_area per square kilometer.









