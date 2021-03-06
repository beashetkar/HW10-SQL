##  HOMEWORK 10 MySQL

### 1a. Display the first and last names of all actors from the table actor


```
select first_name, last_name 
  from actor;

```

### # 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column Actor Name.

```
select concat( first_name, ' ', last_name) as Actor_Name
  from actor;
```
  
### 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?  

```
select actor_id, first_name, last_name
  from actor
 where first_name = "JOE"; 
```
 
### 2b. Find all actors whose last name contain the letters GEN:

```
select * from actor
  where last_name like '%GEN%';
```
  
### Find all actors whose last names contain the letters LI. This time, order the rows by last name and first name, in that order:

```
select actor_id, last_name, first_name 
  from actor
 where last_name like '%LI%'
 order by last_name, first_name;
 ```
 
###  Using IN, display the country_id and country columns of the following countries: Afghanistan, Bangladesh, and China:

```
select country_id, country
  from country
 where country in ('Afghanistan','Bangladesh', 'China'); 
```

### 3a. Add a middle_name column to the table actor. Position it between first_name and last_name. Hint: you will need to specify the data type.

``` 
ALTER TABLE actor
 ADD COLUMN middle_name VARCHAR(90) NULL AFTER first_name;
```

### 3b. You realize that some of these actors have tremendously long last names. Change the data type of the middle_name column to blobs.

```
ALTER TABLE actor
MODIFY COLUMN middle_name BLOB NULL ;
```

### 3c. Now delete the middle_name column.

```
alter table actor
  drop column middle_name;
```

### 4a. List the last names of actors, as well as how many actors have that last name.

```
select last_name, count(last_name)
  from actor
 group by last_name; 
```

### 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors

```
SELECT last_name, count(last_name)
FROM actor
GROUP BY last_name
HAVING count(last_name) > 1;
```

### 4c. Oh, no! The actor HARPO WILLIAMS was accidentally entered in the actor table as GROUCHO WILLIAMS the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.

```
update actor
   set first_name = 'HARPO'
 where first_name = 'GROUCHO'
   and last_name = 'WILLIAMS';
```

### 4d. Perhaps we were too hasty in changing GROUCHO to HARPO. It turns out that GROUCHO was the correct name after all! In a single query, if the first name of the actor is currently HARPO, change it to GROUCHO. Otherwise, change the first name to MUCHO GROUCHO, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO MUCHO GROUCHO, HOWEVER! (Hint: update the record using a unique identifier.)   

```
select actor_id from actor
  where first_name = 'HARPO'; 
  
update actor
   set first_name = 'GROUCHO'
 where actor_id = 172;
```
 
 ### 5a. You cannot locate the schema of the address table. Which query would you use to re-create it?
 
 ```
 show create table address;
 ```

### 6a. Use JOIN to display the first and last names, as well as the address, of each staff member. Use the tables staff and address:

```
select first_name, last_name, address
  from staff s
  join address a
 using (address_id);
```
  
### 6b. Use JOIN to display the total amount rung up by each staff member in August of 2005. Use tables staff and payment.

```
select staff_id, first_name, last_name, concat('$',format(sum(amount),2)) as total_amount
  from staff s
  join payment p
 using (staff_id)
 where payment_date between '2005-08-01' 
   and date_add('2005-08-31', interval 1 day)
  group by staff_id;
```
  
### 6c. List each film and the number of actors who are listed for that film. Use tables film_actor and film. Use inner join.

```
select title, count(fa.actor_id) as Number_actors
  from film f
  inner join film_actor fa
  using (film_id)
 group by fa.film_id;
 ```
 
 ###  6d. How many copies of the film Hunchback Impossible exist in the inventory system?
 
```
select count(inventory_id) as Total_copies
  from film f
 inner join inventory i
 using (film_id)
 where title = 'HUNCHBACK IMPOSSIBLE';
```
 
 ### 6e. Using the tables payment and customer and the JOIN command, list the total paid by each customer. List the customers alphabetically by last name:

```
select last_name, first_name , concat('$',format(sum(amount),2)) as total_paid
 from customer c
 join payment p
   using (customer_id)
 group by customer_id
 order by last_name;
 ```

### 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters K and Q have also soared in popularity. Use subqueries to display the titles of movies starting with the letters K and Q whose language is English.

 ```
 select title
  from film
 where  (title like 'K%'  or title like 'Q%') and
 language_id in 
( 
  select language_id
    from language
   where name = 'English'
);
```

### 7b. Use subqueries to display all actors who appear in the film Alone Trip.

```
 select first_name, last_name
   from actor
  where actor_id in
( select actor_id
      from film_actor
     where  film_id in
	( select film_id
         from film
        where title = 'Alone Trip'
	)
);
```

### 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

```
select cu.first_name, cu.last_name, cu.email
  from customer cu
  join address a
    on cu.address_id = a.address_id
  join city c
    on a.city_id = c.city_id
  join country co
    on c.country_id = co.country_id
 where co.country = 'Canada';   
 ```

 ### 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.

 ```
 select title
  from film 
 where film_id in 
 (
  select fc.film_id 
    from film_category fc
   join category c
	on fc.category_id = c.category_id
  where name = 'Family'
 );
 ```

 ### 7e. Display the most frequently rented movies in descending order.

```
select f.title, count(r.rental_id) as total_times_rented
 from film f
 join inventory i
   on f.film_id = i.film_id
 join rental r
   on i.inventory_id = r.inventory_id
group by f.film_id
order by total_times_rented desc;
```

### 7f. Write a query to display how much business, in dollars, each store brought in.

```
select st.store_id, concat('$',format(sum(p.amount),2)) as total_business
  from store st
  join customer c
 using (store_id)
  join payment  p
    on (c.customer_id = p.customer_id)
group by (st.store_id);
```


### 7g. Write a query to display for each store its store ID, city, and country.

```
select s.store_id, c.city, co.country
  from store s
  join address a
    on s.address_id = a.address_id
  join city c
    on a.city_id = c.city_id
  join country co
    on c.country_id = co.country_id;
 ```   

### 7h. List the top five genres in gross revenue in descending order. (Hint: you may need to use the following tables: category, film_category, inventory, payment, and rental.)

```
select cat.name, concat('$',format(sum(p.amount),2)) as gross_revenue
  from category cat
  join film_category fc
    on cat.category_id = fc.category_id
  join inventory i
    on fc.film_id = inventory_id
  join rental r
    on i.inventory_id = r.inventory_id
  join payment p
    on r.rental_id = p.rental_id
  group by cat.category_id
  order by gross_revenue desc
  limit 5;
  ```

### 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.

```
CREATE 
    ALGORITHM = UNDEFINED 
    DEFINER = `root`@`localhost` 
    SQL SECURITY DEFINER
VIEW `sakila`.`top_5_genres` AS
    SELECT 
        `cat`.`name` AS `name`, SUM(`p`.`amount`) AS `value`
    FROM
        ((((`sakila`.`category` `cat`
        JOIN `sakila`.`film_category` `fc` ON ((`cat`.`category_id` = `fc`.`category_id`)))
        JOIN `sakila`.`inventory` `inv` ON ((`fc`.`film_id` = `inv`.`film_id`)))
        JOIN `sakila`.`rental` `r` ON ((`inv`.`inventory_id` = `r`.`inventory_id`)))
        JOIN `sakila`.`payment` `p` ON ((`r`.`rental_id` = `p`.`rental_id`)))
    GROUP BY `cat`.`category_id`
    ORDER BY SUM(`p`.`amount`) DESC
    LIMIT 5;
```          

### 8b. How would you display the view that you created in 8a?

```
select * from top_5_genres;
```

### 8c. You find that you no longer need the view top_five_genres. Write a query to delete it.

```
drop view top_5_genres;
```
