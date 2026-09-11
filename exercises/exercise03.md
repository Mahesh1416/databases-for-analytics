# Exercise 03: MongoDB – Document Queries and Analysis

- Name: Mahesh Bashyal
- Course: Database for Analytics
- Module: 3
- Database Used: MongoDB
- Dataset: `restaurants-json.json`

---

## Instructions

- Import the provided `restaurants-json.json` file into MongoDB.
- All commands must be **executed by you** in the MongoDB shell or MongoDB Compass.
- For each query:
  - Include the MongoDB command in a fenced code block
  - Include a **screenshot** showing the command and its result
- Store screenshots in the `screenshots/` folder and embed them below each answer.

---

## Question 1

When importing the documents from `restaurants-json.json`,
**how many documents were imported into your collection**?

### Answer

_25358._

### Screenshot

_Show evidence of how you determined this (for example, a count query)._

```javascript

db.restaurants.countDocuments({})
```

<img width="1440" height="900" alt="Question 1" src="https://github.com/user-attachments/assets/01aa98a3-4ecd-4e10-a1e9-504d2777afd6" />


---

## Question 2

Before writing queries on the data,
**what command** do you use to set the
**MongoDB shell to operate on the `44661` database**?

### MongoDB Command

```javascript

use ("44661")
```

### Screenshot

<img width="1440" height="900" alt="Question 2" src="https://github.com/user-attachments/assets/de1cde57-39d0-44db-9c2d-93470699c7a7" />


---

## Question 3

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**locate all documents in the `"Queens"` borough**.

### MongoDB Query

```javascript

db.restaurants.find({borough: "Queens"})
```

### Screenshot

<img width="1440" height="900" alt="Question 3" src="https://github.com/user-attachments/assets/5bb0a4bc-644e-49de-85fe-e5a830a276d0" />


---

## Question 4

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**find the number of restaurants in the `"Queens"` borough**.

### MongoDB Query

```javascript

db.restaurants.countDocuments({borough: "Queens"})
```

### Screenshot

<img width="1440" height="900" alt="Question 4" src="https://github.com/user-attachments/assets/d26490fa-8778-4b4b-a2f5-092752e42cd8" />


---

## Question 5

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**find the number of restaurants** in the `"Queens"` borough
**whose cuisine is `"Hamburgers"`**.

### MongoDB Query

```javascript

db.restaurants.countDocuments({borough: "Queens", cuisine: "Hamburgers"})
```

### Screenshot

<img width="1440" height="900" alt="Question 5" src="https://github.com/user-attachments/assets/122e12ff-c344-4676-9bbb-9c96f37c727c" />


---

## Question 6

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**find the number of restaurants in Zipcode `10460`**.

_Hint: Look up how to query **embedded documents**._

### MongoDB Query

```javascript

db.restaurants.countDocuments({"address.zipcode": "10460"})
```

### Screenshot

<img width="1440" height="900" alt="Question 6" src="https://github.com/user-attachments/assets/4612acf0-7a79-4da1-9b4d-402e57dc69f4" />


---

## Question 7

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**display only the names of restaurants in Zipcode `10460`**.

_Hint: Look up how to **project fields** in MongoDB._

Your output should resemble:

```json
{ name: "Wild Asia" }
{ name: "Terrace Cafe" }
{ name: "African Terrace" }
{ name: "Cool Zone" }
{ name: "Beaver Pond" }
...
```

### MongoDB Query

```javascript

db.restaurants.find({"address.zipcode": "10460"}, {_id: 0, name: 1})
```

### Screenshot

<img width="1440" height="900" alt="Question 7" src="https://github.com/user-attachments/assets/2068e052-0db8-43de-ab89-4bbafd8fca51" />


---

## Question 8

Using your `restaurants` collection in the `44661` database,
write the MongoDB query needed to
**display only the names of restaurants whose name contains `"IHOP"`**,
ignoring case.

Your results should include:

- `"Ihop"`
- `"Ihop Restaurant"`

### MongoDB Query

```javascript

db.restaurants.find({name:/IHOP/i}, {_id: 0, name: 1}).forEach(r => print(r.name))
```

### Screenshot

<img width="1440" height="900" alt="Question 8" src="https://github.com/user-attachments/assets/aec50db4-bf3e-40ad-815f-052f6211fa22" />

