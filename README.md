  # Netflix Data Analysis using SQL
 ![Netflix_logo](https://github.com/SimranjeetG/Netflix_project_SQL-/blob/main/logo.png )

## Objective
The objective of this project is to analyze Netflix's content database using SQL to answer specific business questions such as identifying the most common ratings, comparing the volume of movies vs. TV shows, finding top-performing genres and countries, and uncovering trends based on release years, durations, and director contributions. This analysis supports data-driven insights into content strategy and viewer preferences.

## Dataset
The data for this project is sourced from the Kaggle dataset.

## Schema
'''sql
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
'''



