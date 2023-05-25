## Toronto Rentals SQL Project

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
