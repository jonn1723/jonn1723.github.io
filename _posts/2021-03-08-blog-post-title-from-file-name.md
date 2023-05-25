## Toronto Rentals SQL Project

This is a notebook to track the SQL concepts that I've learnt with the queries and concepts generally becoming increasingly more complex. I am currently looking to rent in Toronto and wish to gain a greater understanding of the rental market. Thus, I scraped a Toronto rental website using Python for all rentals in areas I'm looking to rent in, and performed data analysis to help me decide a suitable and affordable place to rent.

---

#### QUERY 1: SELECT DISTINCT
The SELECT DISTINCT statement returns only distinct/different values. Using the following query, we can see that we are only looking at condos or apartments in the cities North York, Scarborough, and Toronto - parameters that I set when scraping the website. This query verifies that the parameters I set up are correct.!

```tsql
SELECT DISTINCT city, housing_type
FROM cleaned_rental_datasets.compiled_rentals
```

#### Some PowerShell Code

```powershell
Write-Host "This is a powershell Code block";

# There are many other languages you can use, but the style has to be loaded first

ForEach ($thing in $things) {
    Write-Output "It highlights it using the GitHub style"
}
```
