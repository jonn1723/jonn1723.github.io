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

#### Query 4: FROM Subquery (with CONCAT)
The CONCAT operator is short for concatenate and functions similarly to the its Microsoft Excel equivalent by adding two or more strings together.

The query written simply distinguishes between apartment or condo rentals in Toronto, North York, and Scarborough. While the number of rentals between condos and apartments in North York and Scarborough are relatively similar, Toronto has a significantly greater number of condo rentals in comparison.

```sql
SELECT cityHousing, 
  COUNT(cityHousing) AS totalRentals
FROM (
  SELECT CONCAT(city, ' ', housing_type) AS cityHousing
  FROM cleaned_rental_datasets.compiled_rentals
)
  GROUP BY cityHousing
```
<img width="362" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/3850b391-cc5d-4128-8fb4-77875e9337a0">

#### Query 5: MIN or MAX (with IN operator)
The MIN and MAX functions simply return the smallest or largest value of a specified column respectively. As I do not have an unlimited budget, the MIN function is used.

The IN function is also used in this subquery which allows us to list the associated address, bed, and bath values all in a single query. Otherwise, two separate queries would be needed. Using the MAX function in the SELECT statement also doesn't work since columns like address can't be group or aggregated.

```sql
SELECT rent, city, address
FROM cleaned_rental_datasets.compiled_rentals
WHERE rent IN (
  SELECT MIN(rent) -- or MAX
  FROM cleaned_rental_datasets.compiled_rentals
  GROUP BY city
) 
```
<img width="468" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/d3eb4b96-626f-4aed-937b-eb9e0c4f886f">

One rental seems to stand out with its rent at a mere 900. Renting for just 900 a month already seems ludicrous enough on its own, but the rental comes with 5-baths as well! Sure enough, the rental was taken down by the time I checked a few days after obtaining the scraped data. Subsequently, I decided to run this query again while excluding this address.

```sql
SELECT DISTINCT rent, city, address, beds, baths
FROM cleaned_rental_datasets.compiled_rentals
WHERE rent IN (
  SELECT MIN(rent) -- or MAX
  FROM cleaned_rental_datasets.compiled_rentals
  WHERE address != '2951 Dundas Street West '
  GROUP BY city
) 
```
<img width="468" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/0cff7f66-9bfe-4e8c-afec-5b1a1d0065e5">

#### Query 6: LIKE operator (with IN operator)
The LIKE operator is used in the WHERE clause to determine if a string matches the specified pattern. Two wildcards are commonly used in conjunction with the LIKE operator being the percent and underscore wildcards. The percent wildcard denotes any string of zero or more characters while the underscore wildcard denotes specifically one single character.

```sql
SELECT city, housing_type, rent, address
FROM cleaned_rental_datasets.compiled_rentals
WHERE (
  address LIKE '%D_n_%'
  AND address NOT LIKE '%Dundas%'
  AND address LIKE '%West%'
AND
  city IN (
    SELECT city
    FROM cleaned_rental_datasets.compiled_rentals
    Where city = 'Toronto'
  )
) 
```
<img width="468" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/afaaaaff-38d5-4377-99bf-e1be41269701">

#### Query 7: JOINs
The JOIN clause combines rows from multiple tables based off a shared/similar column. Generally speaking, there are 4-types of JOINs. The first is (INNER) JOIN which returns only records with matching records on both sides. Next, a LEFT (OUTER) JOIN returns all records from the left table and matching records from the right. RIGHT (OUTER) JOIN is similar but with all records from the right table instead. Lastly, a FULL (OUTER) JOIN returns all records from both tables.

The compiled table being referred to throughout the project is a compilation of 6 different tables that were cleaned in Microsoft Excel before import into BigQuery. Therefore, the table columns are identical and using JOINs aren't exactly necessary. Since apartments were established earlier to be more affordable than condos, a way JOINs can be used is to compare the difference between two apartment rentals of the same monthly rent in different cities. Judging from the first few results, it seems as if for the same price, Toronto apartments are more spacious compared to those in North York.

```sql
SELECT T.rent, T.city, T.size AS torontoSize, N.city, N.size AS northYorksize, 
  T.address AS torontoAddress, N.address AS northYorkAddress
FROM cleaned_rental_datasets.toronto_apartments AS T
LEFT JOIN cleaned_rental_datasets.north_york_apartments AS N
ON T.rent = N.rent
WHERE T.size IS NOT NULL
  AND N.size IS NOT NULL
  AND T.address != N.address
ORDER BY T.rent
```
<img width="468" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/1707530e-968a-409f-a1a2-54c8131c4b69">

#### Query 8: CASE expressions (with WHERE subquery)
Case expressions are similar to if-else statements and executes the THEN clause if the WHEN clause is fulfilled. If no THEN clauses are satisfied, the ELSE statement is executed.

Apartment rentals are more affordable than condos while Toronto rentals are generally more spacious than rentals in North York and Scarborough so the search results filter for only Toronto Apartment rentals. The where subquery also filters for non-null values and results where the cost is lower than the average.

The CASE expression accounts for occasional "0" values in the size columns that were likely used as a placeholder on the rental website. Otherwise, the query finds the cost-per-square-feet and arranges it in ascending order.

```sql
SELECT city, housing_type, rent, beds, baths, size,
CASE
  WHEN size > 0 THEN ROUND(rent / size, 2)
  ELSE 0 END
  AS cost_per_foot
FROM cleaned_rental_datasets.compiled_rentals
WHERE 
  rent < (
    SELECT AVG(rent)
    FROM cleaned_rental_datasets.compiled_rentals
  ) 
AND size IS NOT NULL
AND city = 'Toronto'
AND housing_type = 'Apartment'
ORDER BY cost_per_foot
LIMIT 10
```
<img width="468" alt="image" src="https://github.com/jonn1723/jonn1723.github.io/assets/127183309/e76ad771-3ad3-4a16-afb4-ae4cb58c9470">

Overall, this project may not be the most efficient way to look for rentals in Toronto, but it surely provided some insight into the rental market and helped me establish a baseline for my future searches. Of course, the queries also don't take into account factors such as interior design or building amenities. Rent and size solely cannot be used to decide on an appropriate rental. However, I now know that I may want to look into Toronto apartment rentals and can start browsing rentals starting with the first rental in the most recent query.

Thank you for reading and please leave any comments or suggestions!
