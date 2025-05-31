  # Netflix Data Analysis using SQL
 ![Netflix_logo](https://github.com/SimranjeetG/Netflix_project_SQL-/blob/main/logo.png )

## Objective
The objective of this project is to analyze Netflix's content database using SQL to answer specific business questions such as identifying the most common ratings, comparing the volume of movies vs. TV shows, finding top-performing genres and countries, and uncovering trends based on release years, durations, and director contributions. This analysis supports data-driven insights into content strategy and viewer preferences.

## Dataset
The data for this project is sourced from the Kaggle dataset.

## Schema
```sql
CREATE TABLE netflix 
( 
show_id	VARCHAR(6),
type VARCHAR(10),
title VARCHAR(150),
director VARCHAR(208),
casts VARCHAR(1000),
country	VARCHAR(150),
date_added VARCHAR(50),
release_year INT,
rating VARCHAR(10),
duration VARCHAR(15),
listed_in VARCHAR(150),
description VARCHAR(250)
);
```
## Problems and Queries
**--1.Count the Number of Movies vs TV Shows**
```sql  
SELECT 
    type,
    COUNT(*) as total_content
FROM netflix
GROUP BY type;
```

**--2.Find the Most Common Rating for Movies and TV Shows**
```sql
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```

**--3.List All Movies Released in a Specific Year (e.g., 2020)**
```sql
SELECT * 
FROM netflix
WHERE release_year = 2020;
```

**--4.Find the Top 5 Countries with the Most Content on Netflix**
```sql
SELECT * 
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```

**--5.Identify the Longest Movie**
```sql
SELECT 
    *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
```

**--6.Find Content Added in the Last 5 Years**
```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

**--7.Find All Movies/TV Shows by Director 'Rajiv Chilaka'**
```sql
SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
```

**--8.List All TV Shows with More Than 5 Seasons**
```sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
```
  
**--9.Count the Number of Content Items in Each Genre**
```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```

**--10.Find Each Year and the Average Number of Content Released in India**
```sql
SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```

**--11.List All Movies that are Documentaries**
```sql
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
```

**--12.Find All Content Without a Director**
```sql
SELECT * 
FROM netflix
WHERE director IS NULL;
```

**--13.Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years**
```sql
SELECT * 
FROM netflix
WHERE casts LIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
  ```
**--14.Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India**
```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
```

**--15.Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords**
```sql
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
```

**--16.Identify the Month with the Highest Number of Content Additions**
```sql
SELECT 
    TO_CHAR(TO_DATE(date_added, 'Month DD, YYYY'), 'Month') AS month_name,
    COUNT(*) AS total_additions
FROM 
    netflix
WHERE 
    date_added IS NOT NULL
GROUP BY 
    month_name
ORDER BY 
    total_additions DESC
LIMIT 1;
```

**--17.List the Top 5 Genres with the Most TV Shows**
```sql
SELECT 
    listed_in AS genre,
    COUNT(*) AS tv_show_count
FROM 
    netflix
WHERE 
    type = 'TV Show'
GROUP BY 
    genre
ORDER BY 
    tv_show_count DESC
LIMIT 5;
```

**--18.Calculate the Average Duration of Movies by Genre**
```sql
SELECT 
    listed_in AS genre,
    AVG(CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER)) AS avg_duration_minutes
FROM 
    netflix
WHERE 
    type = 'Movie'
    AND duration ILIKE '%min%'
GROUP BY 
    genre
ORDER BY 
    avg_duration_minutes DESC;
```
	
**--19.List Content Where the Description Mentions “Based on a True Story”**
```sql
SELECT 
    show_id,
    type,
    title,
    description
FROM 
    netflix
WHERE 
    description ILIKE '%based on a true story%';
```
	
**--20Identify the Number of Unique Directors and How Many Shows Each Directed**
```sql
SELECT 
    director,
    COUNT(*) AS content_count
FROM 
    netflix
WHERE 
    director IS NOT NULL AND TRIM(director) != ''
GROUP BY 
    director
ORDER BY 
    content_count DESC;
```
