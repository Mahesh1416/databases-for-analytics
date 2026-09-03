# Exercise 02: World Database – Joins, Grouping, and Data Quality

- Name: Mahesh Bashyal
- Course: Database for Analytics
- Module: 2
- Database Used: World Database (PostgreSQL)

---

## Instructions

- Answer each question below using SQL executed against the **World database**.
- All SQL commands **must be run by you**.
- For each SQL-based question:
  - Include the SQL command in a fenced code block
  - Include a **screenshot** showing the command and its results
- Store screenshots in the `screenshots/` folder and embed them below each answer.

---

## Question 1

When importing records from `worldPGSQL.sql`, **how many cities were imported**?

### Answer

_Write the number of cities imported._ 4079

### Screenshot

_Show evidence of how you determined this (for example, a COUNT query)._

```sql
-- Your SQL here SELECT COUNT (*) FROM city;
```

<img width="1440" height="900" alt="Question 1" src="https://github.com/user-attachments/assets/9546fdb2-1fbf-4d9f-a6b6-22ae292195d7" />


---

## Question 2

Using the World database, write the SQL command to
**display each country name**
along with the **name of each language spoken in that country**.

### SQL

```sql
-- Your SQL here SELECT country.name AS name_of_country,
       countrylanguage.language
FROM country
JOIN countrylanguage
  ON country.code = countrylanguage.countrycode;

### Screenshot

---
<img width="1440" height="900" alt="Question 2" src="https://github.com/user-attachments/assets/72dd97aa-1c35-4822-b297-47b1610201bd" />

## Question 3

Using the World database, write the SQL command
to **display each country name** along with the name
of each **official language spoken in that country**.

### SQL

```sql
-- Your SQL here SELECT country."Name" AS name_of_country,
       countrylanguage."Language"
FROM country
JOIN countrylanguage
  ON country."Code" = countrylanguage."CountryCode"
WHERE countrylanguage."IsOfficial" = 'T';
```

### Screenshot

<img width="1440" height="900" alt="Question 3" src="https://github.com/user-attachments/assets/2f4aeff0-80e8-4f27-ac1f-080585b37821" />


---

## Question 4

Consider the following two SQL statements:

```sql
SELECT *
FROM country, countrylanguage
WHERE country.code = countrylanguage.countrycode;
```

```sql
SELECT *
FROM country
LEFT OUTER JOIN countrylanguage
ON country.code = countrylanguage.countrycode;
```

**In your own words**, describe what data the
**second query returns that the first query does not**.

### 
_Write your explanation here._
When I ran and compared the two queries, I noticed that the first query doesn't list those entries where there is no matching with a language entry while in the second query, I noticed that it includes all the countries even those that did not have a matching language entry. 
---

## Question 5

Using the World database, write the SQL command
to **list all different forms of government** found in the data.
Do **not** repeat any form of government more than once.

### SQL

```sql
-- Your SQL here SELECT DISTINCT governmentform
FROM country;
```

### Screenshot

<img width="1440" height="900" alt="Question 5" src="https://github.com/user-attachments/assets/40c80669-cd53-4079-b279-39cdf1b2ed15" />


---

## Question 6

Using the World database, write the SQL command
to **list all names of cities and countries in one column**.
Label the column **"City or Country Name"**.

### SQL

```sql
-- Your SQL here SELECT name AS "City or Country Name"
FROM city

UNION

SELECT name
FROM country;
```

### Screenshot

<img width="1440" height="900" alt="Question 6" src="https://github.com/user-attachments/assets/04321a65-2879-4194-aec1-56237834b556" />


---

## Question 7

Using the World database, write the SQL command
to **list all countries by name**,
along with the **number of languages spoken in each country**.
Be sure to **sort by country name**.

### SQL

```sql
-- Your SQL here SELECT country.name AS name_of_country,
       COUNT(countrylanguage.language) AS number_of_languages
FROM country
LEFT JOIN countrylanguage
  ON country.code = countrylanguage.countrycode
GROUP BY country.name
ORDER BY country.name;
```

### Screenshot

<img width="1440" height="900" alt="Question 7" src="https://github.com/user-attachments/assets/94ba3f61-e10b-4e5f-970e-e6a3b1401260" />


---

## Question 8

Using the World database, write the SQL command
to **list all languages**, along with the
**number of countries where each language is spoken**.
Be sure to **sort by language name**.

### SQL

```sql
-- Your SQL here SELECT language,
       COUNT(countrycode) AS number_of_countries
FROM countrylanguage
GROUP BY language
ORDER BY language;
```

### Screenshot

<img width="1440" height="900" alt="Question 8" src="https://github.com/user-attachments/assets/a0145894-1f1b-4974-a30e-be2db3e01bd4" />


---

## Question 9

Using the World database, write the SQL command
to **list countries that have more than two official languages**,
along with the **number of official languages spoken**.

_Hint: There are 8 such countries in this dataset._

### SQL

```sql
-- Your SQL here SELECT country.name AS name_of_country,
       COUNT(countrylanguage.language) AS number_of_official_languages
FROM country
JOIN countrylanguage
  ON country.code = countrylanguage.countrycode
WHERE countrylanguage.isofficial = 'T'
GROUP BY country.name
HAVING COUNT(countrylanguage.language) > 2
ORDER BY country.name;
```

### Screenshot

<img width="1440" height="900" alt="Question 9" src="https://github.com/user-attachments/assets/66eab8c7-38be-4a4d-8f3c-47dc27d01503" />


---

## Question 10

Using the World database, write the SQL command to
**find cities where the district value is missing**.

Hint: Use `LIKE` and the dash (`-`)
since some rows use that instead of actual data.

### SQL

```sql
-- Your SQL here SELECT *
FROM city
WHERE (district) LIKE '–';
```

### Screenshot

<img width="1440" height="900" alt="Question 10" src="https://github.com/user-attachments/assets/b1715648-cc52-4079-9be2-147e5c7f68e1" />


---

## Question 11

Using the World database, write the SQL command to
**calculate the percentage of cities with missing district values**.

_Hint: The result should be approximately 0.4%._

### SQL

```sql
-- Your SQL here SELECT 
  ROUND(
    100.0 * COUNT(*) FILTER (WHERE TRIM(district) LIKE '–') / COUNT(*), 
    1
  ) AS percentage_missing_district
FROM city;
```

### Screenshot

<img width="1440" height="900" alt="Question 11" src="https://github.com/user-attachments/assets/d6faf609-0eca-4109-89dd-8d2a27e74ac5" />

