# Exercise 04: Advanced SQL, Jupyter, and Visualization

- Name:
- Course: Database for Analytics
- Module:
- Database Used: World Database
- Tools Used: PostgreSQL, SQLAlchemy, Pandas, Jupyter Notebooks

---

## Instructions

- Complete each task using the **World database** installed earlier.
- For SQL questions:
  - Write the SQL command in a fenced code block
  - Execute the command and include a **screenshot of the results**
- For Jupyter Notebook questions:
  - Include the required Python statements
  - Include **screenshots of the notebook output**
- Store all screenshots in the `screenshots/` folder and embed them below each question.

---

## Question 1

Considering the World database, write a SQL statement that will
**display the names of countries**
that speak **more than two official languages**,
along with the **number of official languages spoken**.

- Sort the results by **number of languages**, from **most to least**.
- _Hint: There are fewer than 10 countries in the results._

### SQL

```sql
-- Your SQL here
SELECT c.Name, COUNT(*) AS NumLanguages
FROM Country c
JOIN CountryLanguage cl ON c.Code = cl.CountryCode
WHERE cl.IsOfficial = 'T'
GROUP BY c.Name
HAVING COUNT(*) > 2
ORDER BY NumLanguages DESC;
```

### Screenshot

<img width="1440" height="900" alt="Question 1" src="https://github.com/user-attachments/assets/44449564-25cf-4241-b902-a94ca8b5e70e" />


---

## Question 2

Using **Jupyter Notebooks**, you must use the
`create_engine` command to connect to your database.

After the `create_engine` command is executed,
**what are the three statements** required to
execute the query from Question 1 and
**display the results in the notebook**?

### Python Code

```python
# Your three Python statements here
# Import necessary libraries
import pandas as pd
from sqlalchemy import create_engine, text

# Create the engine
engine = create_engine("postgresql+psycopg2://maheshbashyal:Himacbook1@localhost:5432/world")

# Define the SQL query
query = """
SELECT c.name, COUNT(*) AS numlanguages
FROM country c
JOIN countrylanguage cl ON c.code = cl.countrycode
WHERE cl.isofficial = 'T'
GROUP BY c.name
HAVING COUNT(*) > 2
ORDER BY numlanguages DESC;
"""

# 1. Connect
connection = engine.connect()

# 2. Execute
result = connection.execute(text(query))

# 3. Load into DataFrame and display
df = pd.DataFrame(result.fetchall(), columns=result.keys())
df
```

### Screenshot

<img width="1440" height="900" alt="Screenshot 2026-09-19 at 4 56 41 PM" src="https://github.com/user-attachments/assets/804adf90-2b34-4158-9b51-3041584b339f" />


---

## Question 3

Using **Jupyter Notebooks**, write the Python code needed
to produce the following graph:

![countries.jpg](./instructions/04-countries.jpg)

(The graph shows country-level results derived from the World database.)

### Python Code

```python
# Your Python code here
import matplotlib.pyplot as plt

df.plot(x='name', y='numlanguages', kind='bar', legend=True, figsize=(4, 5))

plt.xlabel('name')
plt.ylabel('')
plt.yticks([0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4])
plt.tight_layout()
plt.show()
```

### Screenshot

![Q3 Screenshot](screenshots/q3_countries_graph.png)
