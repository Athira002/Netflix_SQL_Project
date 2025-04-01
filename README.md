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

### 2. Find the most common rating for movies and TV shows
```sql
SELECT*FROM netflix;
SELECT
  type,
  rating
FROM 
(
	SELECT
	  type,
	  rating,
	  COUNT(*),
	  RANK() OVER(PARTITION BY type ORDER BY COUNT(*)DESC) as ranking
	FROM netflix
	GROUP BY 1,2
)as t1
WHERE
  ranking=1
```

### 3. List all movies relaesed in a specific year(eg.2020)
```sql
SELECT * FROM netflix
WHERE 
  type = 'Movie'
  AND
  release_year=2020;
```

### 4. Find the top 5 countries with the most content on Netflix
```sql
 SELECT 
   TRIM(UNNEST(STRING_TO_ARRAY(country,',')))as new_country,
   COUNT(show_id)as total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

### 5.Identify the longest movies?
```sql
SELECT * 
FROM netflix
WHERE 
  type = 'Movie'
  AND 
  CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) = 
      (SELECT MAX(CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER)) 
       FROM netflix WHERE type = 'Movie');
```
         
### 6. Find content added in the last 5 years
```sql
SELECT*FROM netflix
WHERE
  TO_DATE(date_added,'Month DD, YYYY')>=CURRENT_DATE-INTERVAL '5 years';
```

### 7.Find all the movies/TV shows by director 'Rajiv Chilaka'
```sql
SELECT *FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';
```

### 8. List all TV shows with more than 5 seasons
```sql
SELECT *FROM netflix
WHERE 
   type='TV Show'
   AND
   SPLIT_PART(duration,' ',1)::integer >5 ;
```

### 9.Count the number of content items in each genre
```sql
SELECT 
   TRIM(UNNEST(STRING_TO_ARRAY(listed_in, ','))) AS genre,
   COUNT(show_id) AS total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC;
```

### 10.Find each year and the average number of content release in India on netflix,return top 5year with highest avg content release
```sql
WITH yearly_data AS (
    SELECT 
        EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS year,
        COUNT(*) AS yearly_content
    FROM netflix
    WHERE country = 'India'
    GROUP BY 1
)
SELECT 
    year,
    yearly_content,
    ROUND(yearly_content::numeric / (SELECT COUNT(DISTINCT year) FROM yearly_data)::numeric, 2) AS avg_content_per_year
FROM yearly_data
ORDER BY yearly_content DESC
LIMIT 5;
```

### 11. List all the movies that are documentaries
```sql
SELECT*FROM netflix
WHERE 
   listed_in ILIKE '%documentaries%';
```

### 12.Find all the content without any director
```sql
SELECT*FROM netflix
WHERE 
   director IS NULL;
```

### 13.Find the movies actor 'Salman Khan' appeared in last 10 years
```sql
SELECT*FROM netflix 
WHERE
  casts ILIKE '%Salman Khan%'
  AND
  release_year>EXTRACT(YEAR FROM CURRENT_DATE)-10;
```

### 14.Find the top 10 actors who have appeared in the highest number of movies produced in India
```sql
SELECT 
   UNNEST(STRING_TO_ARRAY(casts,','))as actors,
   COUNT(*) as total_content
FROM netflix 
WHERE country ILIKE '%India%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```

### 15.Categorize the content based on the presence of the keywords 'kill' and 'violence' in the 
description field. Label content containing these keywords as 'Bad'and all other content
as 'Good'.Count how many items fall into each category.
```sql
WITH new_table AS (
  SELECT 
    *, 
    CASE 
      WHEN COALESCE(description, '') ~* '\mkill\M' 
        OR COALESCE(description, '') ILIKE '%Violence%'
      THEN 'Bad' 
      ELSE 'Good' 
    END AS category
  FROM netflix
)
SELECT 
  category, 
  COUNT(*) AS total_content
FROM new_table
GROUP BY 1;
```

