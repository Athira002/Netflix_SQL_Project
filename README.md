# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/Athira002/Netflix_SQL_Project/blob/main/netflix.jpg)

## Objective
The primary objective of this project is to analyze and explore the Netflix content library using SQL queries to extract meaningful insights from the dataset. By leveraging PostgreSQL, this analysis focuses on understanding the distribution, trends, and patterns of Netflix's content, including movies and TV shows. Key objectives include:

 1) Content Categorization: To categorize content based on genres, countries, and ratings to understand Netflix’s content variety.

 2) Trend Analysis: To identify trends in content releases over the years, especially focusing on growth in specific regions like India.

 3) Actor and Director Analysis: To identify key actors and directors associated with Netflix content, and to observe their involvement over time.

 4) Content Characteristics: To analyze movies and TV shows based on attributes like duration, number of seasons, and descriptions (e.g., the presence of keywords like "violence" and "kill").

 5) Geographical Insights: To determine which countries contribute the most to Netflix’s content library and how content availability varies across different regions.

Through this analysis, the project aims to provide insights into Netflix's content strategy, help discover hidden patterns, and offer a comprehensive understanding of the platform’s catalog.

## Dataset
The for this project is sourced from the Kaggle Dataset:
- Dataset Link:[Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows)

## Schema

```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
show_id VARCHAR(6),
type VARCHAR(10),
title VARCHAR(150),
director VARCHAR(208),
casts VARCHAR(1000),
country VARCHAR(150),
date_added VARCHAR(50),
release_year INT,
rating VARCHAR(10),
duration VARCHAR(15),
listed_in VARCHAR(100),
description VARCHAR(250)
);
```

## Business Problems and Solutions

### 1. Count the number of Movies or TV Shows

```sql
SELECT*FROM netflix;
SELECT 
 type,
 COUNT(*) as total_content 
FROM netflix
GROUP BY TYPE;
```
