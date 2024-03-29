# sundae
SUNDAE_SQL_TEST
--------------------------------------------------------------------------
Question 1️⃣:  
Pull a list of the top 100 actors based on films they have been in all time within the movie rental database. Order the list descending by number of films.
--------------------------------------------------------------------------
Angel Notes:
Top 100 based on number of films they appeared in. 
Will need a count function probably and will sort based on that count.
Also will need to group by the names.
Do i need to really use 3 tables? Can I just use two?

I noticed in the Tables used there was 3 tables listed.
I also ran the query with pulling count(film_id) from the film_actor table vs the film table like I did below and I got the same result. 

Here is the query:
SELECT
	actor.first_name,
	actor.last_name,
	count(film_actor.film_id) AS n_films
FROM
	actor
JOIN
	film_actor 
	ON actor.actor_id = film_actor.actor_id
GROUP BY
	actor.first_name,
	actor.last_name
ORDER BY
	n_films DESC
LIMIT 
	100;
--------------------------------------------------------------------------
ANSWER:

SELECT
	actor.first_name,
	actor.last_name,
	count(film.film_id) AS n_films
FROM
	actor
JOIN
	film_actor 
	ON actor.actor_id = film_actor.actor_id
JOIN 
	film
	ON film_actor.film_id = film.film_id
GROUP BY
	actor.first_name,
	actor.last_name
ORDER BY
	n_films DESC
LIMIT 
	100;
--------------------------------------------------------------------------

QUESTION 2:  
Pull a list of all films grouped by category.name excluding Sports and Games. Order the list descending by number of films.

Angel Notes:
Framing my thoughts about what the query will need
 - Will need a count function on the film_id
 - group by on the film_category

Notes after I wrote the asnwer to the question
 - Same notes as first question. 
 - I noticed there was three tables listed in tables used. 
 - I wrote the query this time only using two but also listed the query with all three below
 - SELECT
	  category.name,
	  count(film.film_id) as n_films
  FROM
	  category
  JOIN
	  film_category 
	  ON category.category_id = film_category.category_id
  JOIN
	  film 
	  ON film_category.film_id = film.film_id
  WHERE
	  category.name NOT IN ('Sports','Games')
  GROUP BY
	  category.name
  ORDER BY
	  n_films DESC;
-------------------------------------------------------------------------------------
ANSWER:

SELECT
	category.name AS genre,
	count(film_category.film_id) as n_films
FROM
	category
JOIN
	film_category 
	ON category.category_id = film_category.category_id
WHERE
	category.name NOT IN ('Sports','Games')
GROUP BY
	category.name
ORDER BY
	n_films DESC;
-------------------------------------------------------------------------------------
NOT DONE YET!!!!!

Question 3: Write a query we can use to plot the total 
 - (1) number of rentals each week for last 12 weeks
 - (2) revenue from rentals each week for 12 weeks
 - (3) number of individuals that rented movies in each of the last 12 weeks(assume the most recent week was the week with the most recent rental). 
 - Exclude your results to only include rentals from store_id 1.



Fields to include:
	Week
	Total rentals
	Total revenue
	# of individuals
Tables used:
	Rental
	Payment
	Inventory
	Address

Angel Notes:
Framing my thoughts about what the query will need
 - agregate functions
 	o sum function on the revenue field
	o count on the number of rentals
	o count on rental_ts
- Where clause to only see results from store_id = 1

Notes after I wrote the asnwer to the question
 - Same notes as first question. 
 - I noticed there was three tables listed in tables used. 
 - I wrote the query this time only using two but also listed the query with all three below

*/

/*SELECT
	extract('week' from rental.rental_ts) as week,
	count(rental.rental_id) AS n_rentals,
	sum(payment.amount)AS rental_rev,
	count(rental.customer_id) AS n_customers
FROM
	rental
JOIN
	payment
	ON rental.customer_id = payment.customer_id 
JOIN
	inventory
	ON rental.inventory_id = inventory.inventory_id
WHERE 
	store_id = 1
GROUP BY
	week
ORDER BY 
	week DESC;*/
SELECT
	extract('week' from rental.rental_ts) as week
FROM 
	rental
JOIN
	inventory
	ON rental.inventory_id = inventory.inventory_id
GROUP BY 
	 week;
   
-------------------------------------------------------------------------------------

Question 4: Write a query that returns the distinct number of active customers that rented PG-rated movies at least twice in the last 15 days of a month in both July 2020 and August 2020. Exclude any customers from Dallas.

Fields to include:
	# of active US customers
Tables used:
	Rental
	Customer
	Address
	City
	Inventory
	Film

ANGEL NOTES:
Framing my thoughts about what the query will need
- Select
	  o I need to select the count of customer_id. I can get this from customer table
- FROM
	  o The select data will come from the customer talbe.
- WHERE
	  o In the where clause I can filter the months to only be July and August
		  - this is form the rental table using rental_ts
	  o Also need to filder the last 15 days of the month. 
		  - could I extract day of month and limit the to 15?
		  - Also from the rental table using rental_ts
	  o Also need to filter rented movies with a PG rating column film.rating
		  - this will come from the film table which I can join with inventory based on film_id which can join to rental using inventory_id
	  o And I need to filter out customers from dallas
		  - I can link customer.address_id to address.address_id
		
All in all I should literally only return a single number but will need alot of joins to include all the filters.

Notes after I wrote the asnwer to the question
 - I ran a query to see the distinct customer id on a day by day basis and therre were alot more than 543 customers so I expected to see more than 543 customers.
 - I joined a bunch of tables. Not sure I needed them all could most likely optimize this script further. 

ANSWER:
SELECT
	COUNT(DISTINCT(rental.customer_id)) AS n_customers
FROM
	rental
JOIN
	inventory
	ON rental.inventory_id = inventory.inventory_id
JOIN
	film
	ON inventory.film_id = film.film_id
JOIN
	customer
	ON rental.customer_id = customer.customer_id
JOIN
	address
	ON customer.address_id = address.address_id
JOIN
	city
	ON address.city_id = city.city_id
WHERE
	EXTRACT('day' from rental_ts) > 16
	AND
	(EXTRACT('month' from rental_ts) = 07
	OR
	EXTRACT('month' from rental_ts) = 08)
	AND
	city != 'Dallas'
	AND
	rating = 'PG';
	
	
	
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
/*
Question 1️⃣:  
Pull a list of the top 100 actors based on films they have been in all time within the movie rental database. Order the list descending by number of films.
--------------------------------------------------------------------------
Angel Notes:
Top 100 based on number of films they appeared in. 
Will need a count function probably and will sort based on that count.
Also will need to group by the names.
Do i need to really use 3 tables? Can I just use two?

I noticed in the Tables used there was 3 tables listed.
I also ran the query with pulling count(film_id) from the film_actor table vs the film table like I did below and I got the same result. 

Here is my answer:*/
SELECT
	actor.first_name,
	actor.last_name,
	count(film_actor.film_id) AS n_films
FROM
	actor
JOIN
	film_actor 
	ON actor.actor_id = film_actor.actor_id
GROUP BY
	actor.first_name,
	actor.last_name
ORDER BY
	n_films DESC
LIMIT 
	100;
	
/*
QUESTION 2️⃣:  
Pull a list of all films grouped by category.name excluding Sports and Games. Order the list descending by number of films.

Angel Notes:
Framing my thoughts about what the query will need
 - Will need a count function on the film_id
 - group by on the film_category

Notes after I wrote the asnwer to the question
 - Same notes as first question. 
 - I noticed there was three tables listed in tables used. 
 - I wrote the query this time only using two but also listed the query with all three below
 - SELECT
	  category.name,
	  count(film.film_id) as n_films
  FROM
	  category
  JOIN
	  film_category 
	  ON category.category_id = film_category.category_id
  JOIN
	  film 
	  ON film_category.film_id = film.film_id
  WHERE
	  category.name NOT IN ('Sports','Games')
  GROUP BY
	  category.name
  ORDER BY
	  n_films DESC;
Here is my answer:*/
SELECT
	category.name AS genre,
	count(film_category.film_id) as n_films
FROM
	category
JOIN
	film_category 
	ON category.category_id = film_category.category_id
WHERE
	category.name NOT IN ('Sports','Games')
GROUP BY
	category.name
ORDER BY
	n_films DESC;
	
	
/*
Question 3️⃣: Write a query we can use to plot the total (1) number of rentals, 
(2) revenue from rentals, (3) number of individuals that rented movies in each 
of the last 12 weeks(assume the most recent week was the week with the most recent rental). 
Exclude your results to only include rentals from store_id 1

Angel Notes:
Framing my thoughts about what the query will need
 - agregate functions
 	o sum function on the revenue field
	o count on the number of rentals
	o count on rental_ts
	o count on customer_id in the rental table
- Where clause to only see results from store_id = 1

Notes after I wrote the asnwer to the question
 - The question asked for 12 weeks of data but I only got 11 from my query. I wrote the below query to extract the weeks of the dataset as a whole. 
 - SELECT
	to_char(rental_ts, ' WW' ) as week
	FROM 
		rental
	JOIN
		inventory
		ON rental.inventory_id = inventory.inventory_id
	GROUP BY 
		week
	ORDER BY 
		week desc;
 - With this query I also only receive only 12 weeks in the entire dataset so I went ahead with my query. 
 - I added a distinct to the count of customer_id. The thought process is that N-rentals andN-customers were matching 
 which makes sense but it made more sense to me is that we wanted to see the ammount of customers who rented not the amount of rentals again basically. 
 Distinct should tell us how many customers made up those rentals for the week and the revenue
 
Here is my answer:*/
SELECT
	to_char(rental.rental_ts, ' WW' ) as week,
	count(rental.rental_id) AS n_rentals,
	sum(payment.amount)AS rental_rev,
	count(distinct(rental.customer_id)) AS n_customers
FROM
	rental
JOIN
	payment
	ON rental.customer_id = payment.customer_id 
JOIN
	inventory
	ON rental.inventory_id = inventory.inventory_id
WHERE 
	store_id = 1
GROUP BY
	week
ORDER BY 
	week DESC;
	
/*
Question 4️⃣: Write a query that returns the distinct number of active customers that rented PG-rated movies at least twice in the 
last 15 days of a month in both July 2020 and August 2020. Exclude any customers from Dallas.


ANGEL NOTES:
Framing my thoughts about what the query will need
- Select
	o I need to select the count of customer_id. I can get this from customer table
- FROM
	o The select data will come from the customer talbe.
- WHERE
	o In the where clause I can filter the months to only be July and August
		- this is form the rental table using rental_ts
	o Also need to filder the last 15 days of the month. 
		- could I extract day of month and limit the to 15?
		- Also from the rental table using rental_ts
	o Also need to filter rented movies with a PG rating column film.rating
		- this will come from the film table which I can join with inventory based on film_id which can join to rental using inventory_id
	o And I need to filter out customers from dallas
		- I can link customer.address_id to address.address_id
		
All in all I should literally only return a single number but will need alot of joins to include all the filters.

Notes after I wrote the asnwer to the question
 - I ran a query to see the distinct customer id on a day by day basis and therre were alot more than 543 customers so I expected to see more than 543 customers.
 - I joined a bunch of tables. Not sure I needed them all could most likely optimize this script further. 

Here is my answer:*/
SELECT
	COUNT(DISTINCT(rental.customer_id)) AS n_customers
FROM
	rental
JOIN
	inventory
	ON rental.inventory_id = inventory.inventory_id
JOIN
	film
	ON inventory.film_id = film.film_id
JOIN
	customer
	ON rental.customer_id = customer.customer_id
JOIN
	address
	ON customer.address_id = address.address_id
JOIN
	city
	ON address.city_id = city.city_id
WHERE
	EXTRACT('day' from rental_ts) > 16
	AND
	(EXTRACT('month' from rental_ts) = 07
	OR
	EXTRACT('month' from rental_ts) = 08)
	AND
	city.city != 'Dallas'
	AND
	film.rating = 'PG';
	
	
|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
/*
Question 1️⃣:  
Pull a list of the top 100 actors based on films they have been in all time within the movie rental database. Order the list descending by number of films.
--------------------------------------------------------------------------
Angel Notes:
Top 100 based on number of films they appeared in. 
Will need a count function probably and will sort based on that count.
Also will need to group by the names.
Do i need to really use 3 tables? Can I just use two?

I noticed in the Tables used there was 3 tables listed.
I also ran the query with pulling count(film_id) from the film_actor table vs the film table like I did below and I got the same result. 

Here is my answer:*/
SELECT
	actor.first_name,
	actor.last_name,
	count(film_actor.film_id) AS n_films
FROM
	actor
JOIN
	film_actor 
	ON actor.actor_id = film_actor.actor_id
GROUP BY
	actor.first_name,
	actor.last_name
ORDER BY
	n_films DESC
LIMIT 
	100;
	
/*
QUESTION 2️⃣:  
Pull a list of all films grouped by category.name excluding Sports and Games. Order the list descending by number of films.

Angel Notes:
Framing my thoughts about what the query will need
 - Will need a count function on the film_id
 - group by on the film_category

Notes after I wrote the asnwer to the question
 - Same notes as first question. 
 - I noticed there was three tables listed in tables used. 
 - I wrote the query this time only using two but also listed the query with all three below
 - SELECT
	  category.name,
	  count(film.film_id) as n_films
  FROM
	  category
  JOIN
	  film_category 
	  ON category.category_id = film_category.category_id
  JOIN
	  film 
	  ON film_category.film_id = film.film_id
  WHERE
	  category.name NOT IN ('Sports','Games')
  GROUP BY
	  category.name
  ORDER BY
	  n_films DESC;
Here is my answer:*/
SELECT
	category.name AS genre,
	count(film_category.film_id) as n_films
FROM
	category
JOIN
	film_category 
	ON category.category_id = film_category.category_id
WHERE
	category.name NOT IN ('Sports','Games')
GROUP BY
	category.name
ORDER BY
	n_films DESC;
	
	
/*
Question 3️⃣: Write a query we can use to plot the total (1) number of rentals, 
(2) revenue from rentals, (3) number of individuals that rented movies in each 
of the last 12 weeks(assume the most recent week was the week with the most recent rental). 
Exclude your results to only include rentals from store_id 1

Angel Notes:
Framing my thoughts about what the query will need
 - agregate functions
 	o sum function on the revenue field
	o count on the number of rentals
	o count on rental_ts
	o count on customer_id in the rental table
- Where clause to only see results from store_id = 1

Notes after I wrote the asnwer to the question
 - The question asked for 12 weeks of data but I only got 11 from my query. I wrote the below query to extract the weeks of the dataset as a whole. 
 - SELECT
	to_char(rental_ts, ' WW' ) as week
	FROM 
		rental
	JOIN
		inventory
		ON rental.inventory_id = inventory.inventory_id
	GROUP BY 
		week
	ORDER BY 
		week desc;
 - With this query I also only receive only 12 weeks in the entire dataset so I went ahead with my query. 
 - I added a distinct to the count of customer_id. The thought process is that N-rentals andN-customers were matching 
 which makes sense but it made more sense to me is that we wanted to see the ammount of customers who rented not the amount of rentals again basically. 
 Distinct should tell us how many customers made up those rentals for the week and the revenue
 - Using extract vs to_char resulted in different buckets. For example with to_char 35 was the last week vs 34. I stuck with extract since it returnd week as a numeric type.
 
Here is my answer:*/
SELECT
	extract('week' from rental.rental_ts) as week,
	count(rental.rental_id) AS n_rentals,
	sum(payment.amount)AS rental_rev,
	count(distinct(rental.customer_id)) AS n_customers
FROM
	rental
	JOIN payment
	 	ON rental.customer_id = payment.customer_id 
	JOIN inventory
	 	ON rental.inventory_id = inventory.inventory_id
WHERE 
	store_id = 1
GROUP BY
	week
ORDER BY 
	week DESC;
	
/*Question 4: Write a query that returns the distinct number of active customers that rented PG-rated movies at least twice in the 
last 15 days of a month in both July 2020 and August 2020. Exclude any customers from Dallas.


ANGEL NOTES:
Framing my thoughts about what the query will need
- Select
	  o I need to select the count of customer_id. I can get this from customer table
- FROM
	  o The select data will come from the customer talbe.
- WHERE
	  o In the where clause I can filter the months to only be July and August
		  - this is form the rental table using rental_ts
	  o Also need to filder the last 15 days of the month. 
		  - could I extract day of month and limit the to 15?
		  - Also from the rental table using rental_ts
	  o Also need to filter rented movies with a PG rating column film.rating
		  - this will come from the film table which I can join with inventory based on film_id which can join to rental using inventory_id
	  o And I need to filter out customers from dallas
		  - I can link customer.address_id to address.address_id
- All in all I should literally only return a single number but will need alot of joins to include all the filters.

Notes after I wrote the asnwer to the question
 - I ran a query to see the distinct customer id on a day by day basis and therre were alot more than 543 customers so I expected to see more than 543 customers.
 - I joined a bunch of tables. Not sure I needed them all could most likely optimize this script further. 

Here is my answer:*/
SELECT
	COUNT(DISTINCT(rental.customer_id)) AS n_customers
FROM
	rental
	JOIN inventory
		ON rental.inventory_id = inventory.inventory_id
	JOIN film
		ON inventory.film_id = film.film_id
	JOIN customer
		ON rental.customer_id = customer.customer_id
	JOIN address
		ON customer.address_id = address.address_id
	JOIN city
		ON address.city_id = city.city_id
WHERE
	to_char(rental.rental_ts::date, 'DD') > '16'
	AND
	(to_char(rental.rental_ts::date, 'MM') = '07'
	 OR
	 to_char(rental.rental_ts::date, 'MM') = '08')
	AND
	city.city != 'Dallas'
	AND
	film.rating = 'PG';
	
/*
Question 5: Movie rentals are sometimes heavily concentrated on certain calendar days. 
This daily variance might make it more difficult to identify trends or gain any actionable insights. 
To counteract this, we might want to use a rolling sum/average instead. 
For each of the days in August 2020, write a query that shows the rolling average of rental 
rates over the prior seven days(assume we’re only looking at rentals that occur in August).

ANGEL NOTES:
Framing my thoughts about what the query will need
- Select
	  o I need to select each day of August. EX column should be 1 - 31
	  o Need to have the averge rental rate of the previous 7 days. 
	  	- does this mean there will be data for the first 6 days of the month since we are only looking at august rentals
- FROM
	  o Days can come from the an extract of all days from august. not aure if I need to use a tablee to be honest. 
	  o rental rate comes from the film table
	  o will use the inventory table to link inventory to film
- WHERE
	  o I can make sure we are only looking at rental with a arental_ts in august
	  o not sure what else
	  
Notes after I wrote the asnwer to the question
 - This question was tough for me and took some time
 - I want to point out that the question asked for a rolling average of rental rates which is the cost to rent a specific film. I thought it was weird to get a rolling average of
 rental rate so I did a rolling average of rentals for each day in august. Now I could have totally misunderstood what the question wanted but this was my assumption and I feel like it tells me more
 - Also there could be a simpler way to solve this issue but this worked for me and I learned alot
 
Here is my answer:*/

SELECT 
	dates.date as DATE,
	COALESCE(t.rental_count,0),
	COALESCE(ROUND(AVG(t.rental_count) OVER(ORDER BY dates.date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW),2),0) AS avg_rentals
FROM
  	(SELECT to_char(rental.rental_ts::date, 'MM') AS MONTH,
	 		to_char(rental.rental_ts::date, 'DD') AS DAY,
	 		CAST(to_char(rental.rental_ts::date, 'YYYY-MM-DD') AS DATE) AS date,
        	count(rental.rental_id) AS rental_count
   		FROM rental
	 	JOIN inventory
		ON rental.inventory_id = inventory.inventory_id
	 	JOIN film
		ON inventory.film_id = film.film_id 
   		GROUP BY to_char(rental.rental_ts::date, 'MM'),
	 			 to_char(rental.rental_ts::date, 'DD'),
	 			 CAST(to_char(rental.rental_ts::date, 'YYYY-MM-DD') AS DATE)
	 	HAVING to_char(rental_ts::date, 'MM') = '08'
   		ORDER BY to_char(rental.rental_ts::date, 'DD')) t
RIGHT JOIN dates
	  ON t.date = dates.date
GROUP BY dates.date,
		 t.rental_count
HAVING 
	to_char(dates.date::date, 'YYYY') = '2020'
	AND 
	to_char(dates.date::date, 'MM') = '08'
ORDER BY 
	dates.date ASC;
