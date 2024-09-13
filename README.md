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

### Q17. Do German movies get more votes than Italian movies?

 	(SELECT sum(r.total_votes) AS no_of_votes, m.languages
	FROM ratings AS r
	INNER JOIN movie AS m
	ON r.movie_id = m.id
	WHERE languages like '%german%'
 	)
	UNION
	(SELECT sum(r.total_votes), m.languages
	FROM 
	ratings AS r
	INNER JOIN movie AS m
	ON r.movie_id = m.id
	WHERE languages like '%Italian%'
 	);

### Q18. Which columns in the names table have null values ?

	SELECT 	SUM(CASE WHEN name IS NULL THEN 1 ELSE 0 END) AS name_nulls, 
		SUM(CASE WHEN height IS NULL THEN 1 ELSE 0 END) AS height_nulls, 
        	SUM(CASE WHEN date_of_birth IS NULL THEN 1 ELSE 0 END) AS date_of_birth_nulls, 
       	 	SUM(CASE WHEN known_for_movies IS NULL THEN 1 ELSE 0 END) AS known_for_movies_nulls 
	FROM names;

	SECOND METHOD : 

	SELECT count(*)
	FROM names
	WHERE name IS NULL;

	SELECT count(*)
	FROM names
	WHERE height IS NULL;

	SELECT count(*)
	FROM names
	WHERE date_of_birth IS NULL;

	SELECT count(*)
	FROM names
	WHERE known_for_movies IS NULL;

 ### Q19. Who are the top three directors in the top three genres whose movies have an average rating > 8?

 	WITH director_of_movie AS ( SELECT  g.genre, count(g.movie_id) AS movie_count,
				DENSE_RANK() OVER (ORDER BY count(g.movie_id) DESC) AS movie_dense_rank
	FROM genre AS g
	INNER JOIN ratings AS r
	ON g.movie_id = r.movie_id
	WHERE r.avg_rating > 8
	GROUP BY g.genre
	LIMIT 3
 	)
	SELECT 	n.NAME AS director_name, 
		Count(d.movie_id) AS movie_count 
	FROM director_mapping AS d 
    	INNER JOIN genre g 
    	USING (movie_id) 
    	INNER JOIN names AS n 
    	ON n.id = d.name_id 
    	INNER JOIN director_of_movie 
    	USING (genre) 
    	INNER JOIN ratings 
    	USING (movie_id) 
	WHERE avg_rating > 8 
	GROUP BY NAME 
	ORDER BY movie_count DESC 
	limit 3 ;
	
### Q20. Who are the top two actors whose movies have a median rating >= 8 ?

	SELECT 	n.name AS actor_name, 
		count(rm.movie_id) AS movie_count
	FROM role_mapping AS rm
	INNER JOIN names AS n
	ON rm.name_id = n.id
	INNER JOIN ratings AS r
	ON rm.movie_id = r.movie_id
	WHERE rm.category = 'actor' AND r.median_rating >= 8
	GROUP BY n.name 
	ORDER BY movie_count DESC;

 ### Q21. Which are the top three production houses based on the number of votes received by their movies ?

 	SELECT 	m.production_company, 
		sum(r.total_votes) AS vote_count,
        	ROW_NUMBER() OVER (ORDER BY sum(r.total_votes) DESC) AS prod_comp_rank
	FROM movie AS m
	INNER JOIN ratings AS r
	ON m.id = r.movie_id
	GROUP BY production_company
	LIMIT 3;

 ### Q22. Rank actors with movies released in India based on their average ratings. Which actor is at the top of the list?
	(Note: The actor should have acted in at least five Indian movies.) 

 	WITH actor_summary AS (
		SELECT 	n.name AS actor_name, 
			sum(r.total_votes) AS total_votes, 
       			count(m.id) AS movie_count, 
			Round(Sum(avg_rating * total_votes) / Sum(total_votes), 2) AS actor_avg_rating
		FROM role_mapping as rm
		INNER JOIN names as n
		ON rm.name_id = n.id
		INNER JOIN ratings as r
		ON rm.movie_id = r.movie_id
		INNER JOIN movie as m
		ON r.movie_id = m.id
		WHERE category = 'actor' AND country like '%india%'
		GROUP BY name
  		)
	SELECT 	actor_name, total_votes, 
		movie_count, 
		actor_avg_rating,
		dense_rank() OVER (ORDER BY actor_avg_rating DESC) AS actor_rank
	FROM actor_summary
	WHERE movie_count >= 5;

 ### Q23.Find out the top five actresses in Hindi movies released in India based on their average ratings? 
	(Note: The actresses should have acted in at least three Indian movies.)

 	WITH actress_summary AS (
		SELECT 	n.name as actress_name, 
			sum(r.total_votes) AS total_votes, 
			count(m.id) AS movie_count, 
			Round(Sum(avg_rating * total_votes) / Sum(total_votes), 2) AS actress_avg_rating
		FROM role_mapping AS rm
		INNER JOIN names AS n
		ON rm.name_id = n.id
		INNER JOIN ratings AS r
		ON rm.movie_id = r.movie_id
		INNER JOIN movie as m
		ON r.movie_id = m.id
		WHERE category = 'actress' AND country like '%india%' AND languages like '%hindi%'
		GROUP BY name
  		)
	SELECT 	actress_name, 
		total_votes, 
		movie_count, 
        	actress_avg_rating,
		DENSE_RANK() OVER( ORDER BY actress_avg_rating DESC) AS actress_rank
	FROM actress_summary
	WHERE movie_count >= 3;

 ### Q24. Select thriller movies as per avg rating and classify them in the following category: 
			Rating > 8: Superhit movies
			Rating between 7 and 8: Hit movies
			Rating between 5 and 7: One-time-watch movies
			Rating < 5: Flop movies

   	WITH thriller_movie_summary AS(
		SELECT 	id, title, genre, avg_rating 
		FROM movie AS m
		INNER JOIN genre AS g
		ON m.id = g.movie_id
		INNER JOIN ratings AS r
		ON g.movie_id = r.movie_id 
		WHERE genre = 'thriller'
		)
		SELECT title,
  			CASE 
     			WHEN avg_rating >8 THEN 'Superhit Movies'
            		WHEN avg_rating BETWEEN 7 AND 8 THEN 'Hit movies'
            		WHEN avg_rating BETWEEN 5 AND 7 THEN 'One-Time watch movies'
            		ELSE 'Flop movies'
            		END AS Rating
		FROM thriller_movie_summary;

  ### Q25. What is the genre-wise running total and moving average of the average movie duration ? 

  	SELECT 	genre, round(avg(duration),2) AS avg_duration,
		sum(round(avg(duration),2)) OVER (ORDER BY genre) AS running_total_duration,
       	 	AVG(round(avg(duration),2)) OVER (ORDER BY genre) AS moving_avg_duration
	FROM genre AS g
	INNER JOIN movie as m
	ON g.movie_id = m.id
	GROUP BY genre;

 ### Q26. Which are the five highest-grossing movies of each year that belong to the top three genres ?

 	WITH top_3_genre AS(
		SELECT   genre
		FROM genre
		GROUP BY genre
		ORDER BY COUNT(genre) DESC
		LIMIT 3 
  		),
	top_movie AS (
		SELECT  genre,year, TITLE  AS movie_name,
			CAST(REPLACE(IFNULL(worlwide_gross_income,0),'$ ','') AS DECIMAL(10)) AS worldwide_gross_income_$,
			ROW_NUMBER() OVER (PARTITION BY YEAR ORDER BY CAST(REPLACE(IFNULL(worlwide_gross_income,0),'$ ','') AS DECIMAL(10)) DESC) AS movie_rank
		FROM movie AS m
		INNER JOIN genre AS g
		ON m.id = g.movie_id
		WHERE genre IN(SELECT * FROM   top_3_genre) 
		)
 	SELECT *
  	FROM   top_movie
 	WHERE  movie_rank<=5;

  ###  Q27.  Which are the top two production houses that have produced the highest number of hits (median rating >= 8) among multilingual movies ?

  	SELECT  m.production_company,
		COUNT(m.production_company) AS movie_count ,
		DENSE_RANK() OVER(ORDER BY COUNT(m.production_company) DESC) AS prod_comp_rank
	FROM movie AS m
	INNER JOIN ratings AS r
	ON m.id = r.movie_id
	WHERE median_rating >=8 AND languages REGEXP ','
	GROUP BY production_company
	LIMIT 2;

### Q28. Who are the top 3 actresses based on number of Super Hit movies (average rating >8) in drama genre ?
 	SELECT 	name AS actress_name, 
		total_votes, 
		count(rm.movie_id) AS movie_count, 
		avg_rating AS actress_avg_rating,
		DENSE_RANK() OVER (ORDER BY avg_rating desc) AS actress_rank
	FROM role_mapping AS rm
		INNER JOIN names AS n
		ON rm.name_id = n.id
		INNER JOIN genre AS g
		ON rm.movie_id = g.movie_id
		INNER JOIN ratings AS r
		ON rm.movie_id = r.movie_id
	WHERE rm.category = 'actress' AND g.genre = 'drama' AND r.avg_rating > 8
	GROUP BY name;

### Q29. Get the following details for top 9 directors (based on number of movies)
		Director id
		Name
		Number of movies
		Average inter movie duration in days
		Average movie ratings
		Total votes
		Min rating
		Max rating
		total movie durations

  	WITH next_date_published AS (
		SELECT  name_id AS director_id,
			NAME AS director_name,
			date_published,
			avg_rating,
			total_votes,
			duration,
			LEAD(date_published,1) OVER(PARTITION BY name_id ORDER BY date_published) AS next_date_published
		FROM director_mapping AS d
			INNER JOIN names AS n
			ON d.name_id = n.id
			INNER JOIN movie as m
			ON d.movie_id = m.id
			INNER JOIN ratings AS r
			USING     (movie_id) 
        		) 
		SELECT director_id,
			director_name,
			COUNT(director_name) AS number_of_movies,
			ROUND(AVG(DATEDIFF(next_date_published,date_published)),0) AS avg_inter_movie_days,
			ROUND(AVG(avg_rating),2) AS avg_rating,
			SUM(total_votes) AS total_votes,
			MIN(avg_rating) AS min_rating,
			MAX(avg_rating) AS max_rating,
			SUM(duration)  AS total_duration
		FROM     next_date_published
		GROUP BY director_id
		ORDER BY number_of_movies DESC
		LIMIT    9;	

 	






 

