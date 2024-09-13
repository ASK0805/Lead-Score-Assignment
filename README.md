# SQL-PROJECT | RSVP MOVIES DATASET

![RSVP](rsvp.png)

## Introduction 
RSVP Movies is an Indian film production company which has produced many super-hit movies. They have usually released movies for the Indian audience but for their next project, they are planning to release a movie for the global audience in 2022.

The production company wants to plan their every move analytically based on data and have approached you for help with this new project. You have been provided with the data of the movies that have been released in the past three years. You have to analyse the data set and draw meaningful insights that can help them start their new project.

Here we act as a data analyst and an SQL expert. We have to use SQL to analyse the given data and give recommendations to RSVP Movies based on the insights. 

## Data Model
![ER Diagram for this project](Data_Model.jpg)

## Dataset
Here we have 6 tables which are connected to each other with primary key and name of tables are movie, genre, director_mapping, role_mapping, ratings, names.
movie table have columns like id, title, year, date_published, duration, country, worldwide_gross_income, languages, production_company.
  - genre table have columns like movie_id, genre.
  - director_mapping have columns like movie_id, name_id. 
  - role_mapping have columns like movie_id, name_id, category.
  - name have columns like id name, height, date_of_birth, known_for_movies.
  - ratings have columns like movie_id, avg_rating, total_votes, median_rating.

Link of dataset : [Dataset](https://github.com/ASK0805/SQL-Project/tree/main/Dataset)

## Business Problems and Solutions

### Q1. Find the total number of rows in each table of the schema?
        
        SELECT count(*) AS 'number of row in director mapping'
        FROM director_mapping;

        SELECT count(*) AS number_of_rows_genre
        FROM genre;

        SELECT count(*) AS 'number of row movie'
        FROM movie;

        SELECT count(*) AS number_of_row_ratings
        FROM ratings;

        SELECT count(*) AS 'number of row role mapping'
        FROM role_mapping;

### Q2. Which columns in the movie table have null values?
  
    SELECT count(*)
    FROM movie
    WHERE id IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE title IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE year IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE date_published IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE duration IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE country IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE worlwide_gross_income IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE languages IS NULL;
  
    SELECT count(*)
    FROM movie
    WHERE production_company IS NULL;

### Q3. Find the total number of movies released each year? How does the trend look month wise?

    SELECT 	year, count(id) AS number_of_movies
    FROM movie
    GROUP BY year;

    Second Part of the question 

    SELECT 	month(date_published) AS month_of_movie, count(id) AS number_of_movies
    FROM movie
    GROUP BY month_of_movie
    ORDER BY month_of_movie;

### Q4. How many movies were produced in the USA or India in the year 2019??

    SELECT 	country, count(id) AS 'Total Number Of Movies'
    FROM movie
    WHERE year= 2019 AND (country like '%USA%' or country like '%india%');

### Q5. Find the unique list of the genres present in the data set?

    SELECT DISTINCT genre AS 'List Of Genre'
    FROM genre
    ORDER BY genre;

### Q6.Which genre had the highest number of movies produced overall?

    SELECT 	G.genre, count(M.id) AS number_of_movies
    FROM genre AS G
	  INNER JOIN movie AS M 
	  ON G.movie_id = M.id
    GROUP BY genre
    ORDER BY number_of_movies DESC
    LIMIT 1;

### Q7. How many movies belong to only one genre?

    WITH only_one_genre AS (
    SELECT movie_id
    FROM genre
    GROUP BY movie_id
    HAVING count(distinct genre)=1
    )

    SELECT count(*) AS movie_with_one_genre
    FROM only_one_genre;

### Q8.What is the average duration of movies in each genre?

    SELECT  g.genre, ROUND(avg(m.duration),2) AS avg_duration
    FROM genre AS g
	  INNER JOIN movie AS m
	  ON g.movie_id = m.id
    GROUP BY genre;

### Q9.What is the rank of the ‘thriller’ genre of movies among all the genres in terms of number of movies produced?

    WITH number_of_movies AS (
    SELECT  g.genre, count(m.id) AS movie_number,
            RANK() OVER ( ORDER BY count(m.id) DESC) AS rank_genre
    FROM genre AS g
	  INNER JOIN movie AS m 
	  ON g.movie_id = m.id
    GROUP BY genre
    )
    SELECT *
    FROM number_of_movies  
    WHERE genre = 'Thriller';

### Q10.  Find the minimum and maximum values in  each column of the ratings table except the movie_id column?

    SELECT  MIN(avg_rating) AS min_avg_rating, 
		        MAX(avg_rating) AS max_avg_rating,
		        MIN(total_votes) AS min_total_votes, 
		        MAX(total_votes) AS max_total_votes,
            MIN(median_rating) AS min_median_rating, 
            MAX(median_rating) AS max_median_rating
    FROM ratings;

### Q11. Which are the top 10 movies based on average rating?

    SELECT  m.title, r.avg_rating,
		        ROW_NUMBER() OVER (ORDER BY r.avg_rating DESC) AS movie_rank
    FROM movie AS m
	  INNER JOIN ratings AS r
	  ON m.id = r.movie_id
    limit 10;

### Q12. Summarise the ratings table based on the movie counts by median ratings?

    SELECT 	median_rating, 
		        count(movie_id) AS movie_count
    FROM ratings
    GROUP BY median_rating
    ORDER BY median_rating ;

### Q13. Which production house has produced the most number of hit movies (average rating > 8) ?

    SELECT 	m.production_company, 
		        count(m.id) AS no_of_movies,
		        DENSE_RANK() OVER (ORDER BY count(m.id) DESC) AS prod_company_rank
    FROM movie AS m
	  INNER JOIN ratings AS r
	  ON m.id = r.movie_id
    WHERE avg_rating > 8 AND production_company is not null
    GROUP BY production_company;

### Q14. How many movies released in each genre during March 2017 in the USA had more than 1,000 votes ?
    
    SELECT g.genre, count(m.id) AS movie_count
    FROM genre AS g
	  INNER JOIN movie AS m
	  ON g.movie_id = m.id
	  INNER JOIN ratings AS r
	  ON m.id = r.movie_id
    WHERE year=2017 AND month(m.date_published) = 3 AND country like 'USA' AND total_votes > 1000
    GROUP BY genre
    ORDER BY movie_count desc;

### Q15. Find movies of each genre that start with the word ‘The’ and which have an average rating > 8 ?

    SELECT 	m.title, r.avg_rating, g.genre
    FROM genre AS g
	  INNER JOIN movie AS m
	  ON g.movie_id = m.id
	  INNER JOIN ratings AS r
	  ON m.id = r.movie_id
    WHERE title like'The%' AND avg_rating > 8;

    Here apply filter on the basis of median rating

    SELECT 	m.title, r.avg_rating, g.genre
    FROM genre AS g
	  INNER JOIN movie AS m
	  ON g.movie_id = m.id
	  INNER JOIN ratings AS r
	  ON m.id = r.movie_id
    WHERE title like'The%' AND median_rating > 8;

### Q16. Of the movies released between 1 April 2018 and 1 April 2019, how many were given a median rating of 8?

    SELECT 	r.median_rating, count(m.id) AS movie_count
    FROM movie AS m
	  INNER JOIN ratings AS r
	  ON m.id = r.movie_id
    WHERE date_published BETWEEN '2018-04-01' AND '2019-04-01'  AND median_rating = 8
    GROUP BY median_rating;






 

