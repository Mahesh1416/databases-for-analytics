# Exercise 05: SQLDA Database - Dates, Data Quality, Arrays, and JSON

- Name: Mahesh Bashyal
- Course: Database for Analytics
- Module: 5
- Database Used: `sqlda` (Sample Datasets)
- Tools Used: PostgreSQL (pgAdmin or psql)

---

## Instructions

- Use the **sqlda** database from the "Loading the Sample Datasets" instructions.
- For each SQL task:
  - Include your SQL in a fenced code block
  - Execute it and include a **screenshot** showing the query and results
- Store screenshots in the `screenshots/` folder and embed them below each answer.
- For explanation questions:
  - Write your answer in complete sentences
  - Include a screenshot if requested

---

## Question 1

Using the `sqlda` database, write the SQL needed
to show a **list of years** that emails were sent.

Your results should list years like this (order matters):

```text
year
2011
2013
2014
2015
2016
2017
2018
2019
```

### SQL

```sql
-- 
SELECT DISTINCT
    EXTRACT(YEAR FROM sent_date) AS year
FROM emails
ORDER BY year;
```

### Screenshot

<img width="1440" height="900" alt="Question 1" src="https://github.com/user-attachments/assets/86f8b8cb-8aa9-4033-ab11-6b4c550b2c64" />


---

## Question 2

Using the `sqlda` database, write the SQL needed to
show the **number of messages sent by year**,
ordered by year (as shown in the prompt).

Output should resemble:

```text
count   year
...
```

### SQL

```sql
-- 
SELECT
    COUNT(*) AS count,
    EXTRACT(YEAR FROM sent_date) AS year
FROM emails
GROUP BY year
ORDER BY year;
```

### Screenshot

<img width="1440" height="900" alt="Question 2" src="https://github.com/user-attachments/assets/5c1b2685-097d-456d-a8ea-9efd9eb9fae7" />

---

## Question 3

Using the `sqlda` database, write the SQL needed to show:

- the **sent date**
- the **opened date**
- the **interval** between the two

Only include emails that contain **both** a sent date and an opened date.

### SQL

```sql
-- 
SELECT
    sent_date,
    opened_date,
    opened_date - sent_date AS interval
FROM emails
WHERE sent_date IS NOT NULL
    AND opened_date IS NOT NULL;
```

### Screenshot

<img width="1440" height="900" alt="Question 3" src="https://github.com/user-attachments/assets/4bc95a3a-dddf-4db3-97d5-af1d14b285dd" />


---

## Question 4

Using the `sqlda` database,
write the SQL needed to
show emails that contain an **opened date BEFORE the sent date**.

### SQL

```sql
-- 
SELECT email_id, opened_date, sent_date
FROM emails
WHERE opened_date < sent_date;
```

### Screenshot

<img width="1440" height="900" alt="Question 4" src="https://github.com/user-attachments/assets/368ef2a9-b149-4cae-a234-5d7b08e8db3e" />


---

## Question 5

Using the `sqlda` database:
there are **over 100 emails**
that contain an opened date **BEFORE** the sent date.

After looking at the data, **why is this the case?**

### Answer

_Here we can notice that the opened times are probably in the local time zone while the sent date/time are in the default time zone. If you look carefully, we notice that the sent date/time have the same stamp but the opened dates/time are different_

---

## Question 6

Using the `sqlda` database, explain in your own words what the following code does:

```sql
CREATE TEMP TABLE customer_points AS (
    SELECT
        customer_id,
        point(longitude, latitude) AS lng_lat_point
    FROM customers
    WHERE longitude IS NOT NULL
    AND latitude IS NOT NULL
);

CREATE TEMP TABLE dealership_points AS (
    SELECT
        dealership_id,
        point(longitude, latitude) AS lng_lat_point
    FROM dealerships
);

CREATE TEMP TABLE customer_dealership_distance AS (
    SELECT
       customer_id,
       dealership_id,
       c.lng_lat_point <@> d.lng_lat_point AS distance
    FROM customer_points c
    CROSS JOIN dealership_points d
);
```

### Answer

_This code creates a pipeline to connect the customers to the nearest dealership. It creates three temporary tables which helps to do that. The first one builds a point for customer filtering out any missing coordinates. The second one does similar work for dealership. The third query cross joins every customer points with every dealership points and the distance between them._

---

## Question 7

Using the `sqlda` database,
write SQL to display an
**array of salespeople for each dealership**,
sorted by dealership.

For example - dealership 1 is below:

```text
"{""Fidell,Granville"",""Onele,Jereme"",""Sheriff,Lelia"",""McSpirron,Massimiliano"",""Rennick,Nadia"",""Mace,Eveleen"",""Oxteby,Dukie"",""Spong,Marcos"",""Wogden,Quent"",""Duny,Sandye"",""Loraine,Englebert"",""Meere,Ira"",""Gibbens,Cristine"",""Prine,Lyda"",""McCoughan,Sheff"",""Schule,Giselbert"",""McAndie,Eleen"",""Dosedale,Dorie"",""Nafziger,Shay""}"
```

### SQL

```sql
-- 
SELECT
    dealership_id,
    ARRAY_AGG(last_name || ',' || first_name) AS salespeople
FROM salespeople
GROUP BY dealership_id
ORDER BY dealership_id;
```

### Screenshot

<img width="1440" height="900" alt="Question 7" src="https://github.com/user-attachments/assets/3c21d529-7a59-49be-a80a-c65fd2e7000e" />


---

## Question 8

Using the `sqlda` database, write SQL to display:

- an **array of salespeople for each dealership**
- the **state** of the dealership
- the **number of salespeople** for the dealership

Sort by **state**.

Reference image:

![05-ExerciseArray](./instructions/05-ExerciseArray.jpg)

### SQL

```sql
-- 
SELECT
    d.dealership_id,
    d.state,
    COUNT(s.salesperson_id) AS number_of_salespeople,
    ARRAY_AGG(s.last_name || ',' || s.first_name) AS salespeople
FROM salespeople s
JOIN dealerships d
    ON s.dealership_id = d.dealership_id
GROUP BY d.dealership_id, d.state
ORDER BY d.state;
```

### Screenshot

<img width="1440" height="900" alt="Question 8" src="https://github.com/user-attachments/assets/e99f51cf-1e93-4b09-8019-24246dede6c4" />



---

## Question 9

Using the `sqlda` database, write the SQL needed to convert
the **customers** table to **JSON**.

### SQL

```sql
-- 
SELECT 
row_to_json(customers)
FROM customers;

```

### Screenshot

<img width="1440" height="900" alt="Question 9" src="https://github.com/user-attachments/assets/84882155-8b46-4070-ac33-8808d903f24b" />


---

## Question 10

Using the `sqlda` database, write SQL to display:

- an **array of salespeople for each dealership**
- the **state**
- the **number of salespeople**
- sorted by **state**

Then **convert this result to JSON**.

Reference image:

![05-ExerciseArray-1](./instructions/05-ExerciseArray-1.jpg)

### SQL

```sql
-- 
SELECT row_to_json(dealership_summary)
FROM (
    SELECT
        d.dealership_id,
        d.state,
        COUNT(s.salesperson_id) AS number_of_salespeople,
        ARRAY_AGG(s.last_name || ',' || s.first_name) AS salespeople
    FROM salespeople s
    JOIN dealerships d
        ON s.dealership_id = d.dealership_id
    GROUP BY d.dealership_id, d.state
    ORDER BY d.state
) AS dealership_summary;
```

### Screenshot

<img width="1440" height="900" alt="Question 10" src="https://github.com/user-attachments/assets/1f7419cf-3964-4477-b637-ebd422784be2" />

