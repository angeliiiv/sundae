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
