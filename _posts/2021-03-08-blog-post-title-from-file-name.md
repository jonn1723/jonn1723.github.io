# Toronto Rentals SQL Project

This is a notebook to track the SQL concepts that I've learnt with the queries and concepts generally becoming increasingly more complex. I am currently looking to rent in Toronto and wish to gain a greater understanding of the rental market. Thus, I scraped a Toronto rental website using Python for all rentals in areas I'm looking to rent in, and performed data analysis to help me decide a suitable and affordable place to rent.

---

#### QUERY 1: SELECT DISTINCT
The SELECT DISTINCT statement returns only distinct/different values. Using the following query, we can see that we are only looking at condos or apartments in the cities North York, Scarborough, and Toronto - parameters that I set when scraping the website. This query verifies that the parameters I set up are correct.

```sql
SELECT DISTINCT city, housing_type
FROM cleaned_rental_datasets.compiled_rentals
```
<img width="462" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/07a8a01a-3a87-4596-a8a2-6535c9fa841b">

#### Query 2: COUNT (with GROUP BY, ORDER BY, and ALIASES)
The COUNT operator returns the number of rows that match your specified criteria while the ORDER BY operator sorts your results in order by the specified column. Meanwhile, the GROUP BY operator groups like-values and is often used with aggregating statements such as with COUNT in this example. Other aggregate functions include AVG, SUM, and MIN or MAX, that are touched on further along the notebook. An alias is also used for the second column to provide context and as expected, Toronto has the most rentals out of the three cities.

```sql
SELECT city, COUNT(city) AS TotalRentals
FROM cleaned_rental_datasets.compiled_rentals
GROUP BY city
ORDER BY TotalRentals DESC
```
<img width="362" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/f9424127-70c6-4364-bac4-d4c7242808aa">

#### Query 3: FROM Subquery (with ROUND)
Queries in SQL can be placed inside another query. The inner query is called a subquery and is executed first, before the outer query executes based on the inner query's results. Subqueries in the FROM clause are sometimes called derived tables or table expressions because the outer query will then use the results as a data source. Meanwhile, the ROUND function is self-explanatory and rounds your result to a specified decimal.

```sql
SELECT city, housing_type, temp_table.average_rent
FROM (
  SELECT city, housing_type, ROUND(AVG(rent), 2) AS average_rent
  FROM cleaned_rental_datasets.compiled_rentals
  GROUP BY city, housing_type
) AS temp_table
ORDER BY average_rent 
```
<img width="468" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/2d7e4b57-79d2-489b-badf-ebec47d59bbf">

Although this example demonstrates the process of creating a derived table and calling its specific columns, the query can be simplified as shown:

```sql
SELECT city, housing_type, ROUND(AVG(rent), 2) AS average_rent
FROM cleaned_rental_datasets.compiled_rentals
GROUP BY city, housing_type
ORDER BY average_rent 
```

Nevertheless, the results indicate that Scarborough renting is the lowest on average with Toronto being the most expensive. Meanwhile, apartment rentals are the cheaper option in all cities. Considering apartments and condominiums are similar in terms of what they offer, renting an apartment in any city will be the more sensible option for individuals with a smaller budget or just looking to save where they can.
