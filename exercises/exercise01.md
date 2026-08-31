# Exercise 01: World Database SQL Practice

- Name:
- Course: Database for Analytics
- Module: 1
- Database Used: World Database

---

See:

[MySQL: Setting Up the World Database](https://dev.mysql.com/doc/world-setup/en/)

---

## Instructions

- Answer each question below.
- All SQL commands **must be executed** against the World database.
- For each SQL command:
  - Include the SQL in a fenced code block
  - Include a **screenshot** showing the command and results
- Store screenshots in the `screenshots/` folder and embed them below each answer.

---

## Question 1

**Compare and contrast the data types used for:**

- `country.Population`
- `country.LifeExpectancy`

Why were these data types selected?

### Answer

_country.Population is a data type integer because population value is a whole number as population cannot be based on decimals.
LifeExpectancy is data type decimal because whenever we calculate expectancy we can get a decimal number.

### Screenshot

<img width="1440" height="900" alt="Question 1" src="https://github.com/user-attachments/assets/7ce24dca-3544-4051-88be-9474fbcbf215" />


```sql
DESCRIBE country;
```

<img width="1440" height="900" alt="Question 1" src="https://github.com/user-attachments/assets/a7e0dce1-f24c-4fce-8b36-b9f61fe8a57f" />


---

## Question 2

**What is the data type of `country.IndepYear`?**
Why do you think this data type was selected?

_The type of data was small integer. This data type was selected because this allows numerical calculations when needed.

### Screenshot

```sql
DESCRIBE country;
```

<img width="1440" height="900" alt="Question 1" src="https://github.com/user-attachments/assets/2ac9bd83-0b51-4c94-9de6-3c0238a55572" />


---

## Question 3

**Make a case for a different data type for `country.IndepYear`.**
Explain why your proposed data type might be better in some situations.

_We can set it for a text format. This would be beneficial in the case when we do not need to perform mathematical calculations.


---

## Question 4

Write a SQL command to **list the names of all cities in alphabetical order**.

### SQL

```sql
SELECT Name
FROM city
ORDER BY Name;
```

### Screenshot

<img width="1440" height="900" alt="Question 4" src="https://github.com/user-attachments/assets/d12b0f74-e543-403e-8cc2-be200e6ef863" />



---

## Question 5

Write a SQL command to
**list all forms of government from the `country` table**,
showing **each only once**, sorted alphabetically.

### SQL

```sql
SELECT DISTINCT GovernmentForm
FROM country
ORDER BY GovernmentForm;
```

### Screenshot

<img width="1440" height="900" alt="Question 5" src="https://github.com/user-attachments/assets/614569c2-2ac4-464c-855c-fcae65fb49de" />


---

## Question 6

Write a SQL command to **list all countries in the `Oceania` continent**.

### SQL

```sql
SELECT Name
FROM country
WHERE Continent = 'Oceania';
```

### Screenshot

<img width="1440" height="900" alt="Question 6" src="https://github.com/user-attachments/assets/a47c7042-c9ec-490e-85d3-40a20b06d3f5" />


---

## Question 7

Write a SQL command to **list the names and country code of all cities**.

### SQL

```sql
SELECT Name, CountryCode
FROM city;
```

### Screenshot

<img width="1440" height="900" alt="Question 7" src="https://github.com/user-attachments/assets/74c57890-84a7-4bf8-9975-af6a9aa3a5fc" />


---

## Question 8

Write a SQL command to **update the city named `"Nashville-Davidson"` to `"Nashville"`**.

### SQL

```sql
UPDATE city
SET Name = 'Nashville'
WHERE Name = 'Nashville-Davidson';
```

### Screenshot

<img width="1440" height="900" alt="Question 8" src="https://github.com/user-attachments/assets/8cf93df9-349a-4f50-9548-29bb7c9df3db" />


---

## Question 9

Write a SQL command to **insert a new country named `"Narnia"`**
with a country code of `"NAR"`.
Use reasonable values for the remaining columns.

### SQL

```sql
INSERT INTO country (Code, Name, Continent, Region, Population)
VALUES ('NAR', 'Narnia', 'Europe', 'Fantasy', 1000000);
```

### Screenshot

<img width="1440" height="900" alt="Question 9" src="https://github.com/user-attachments/assets/98226ff9-dca1-4f79-9b57-5875ef33ae15" />


---

## Question 10

Write a SQL command to **delete the country with the country code `"NAR"`**.

### SQL

```sql
DELETE FROM country
WHERE Code = 'NAR';
```

### Screenshot

<img width="1440" height="900" alt="Question 10" src="https://github.com/user-attachments/assets/4963e23d-728e-408c-acfe-a5651f6d28f6" />

