<img width="1440" height="900" alt="select number of rows from each table" src="https://github.com/user-attachments/assets/ebf2d1a7-221b-44dc-9420-57c4bb1c36a1" /># Final project using Olist E-Commerce Dataset: PostgreSQL Analysis

Database: `olist` (PostgreSQL, managed in pgAdmin 4)
Tables: `customers`, `products`, `orders`, `order_items`

---

## 1. The Initial Data Source

The data comes from the Olist Brazilian e-commerce dataset. It is a set of related CSV files (comma-delimited, with a header row). Four of them are used here.

| Table | CSV file | Columns | Rows |
|---|---|---|---|
| customers | `olist_customers_dataset.csv` | 5 | 99,441 |
| products | `olist_products_dataset.csv` | 9 | 32,951 |
| orders | `olist_orders_dataset.csv` | 8 | 99,441 |
| order_items | `olist_order_items_dataset.csv` | 6 | 112,650 |

### Row counts

```sql
SELECT 'customers' AS table_name, COUNT(*) AS row_count FROM customers
UNION ALL SELECT 'products', COUNT(*) FROM products
UNION ALL SELECT 'orders', COUNT(*) FROM orders
UNION ALL SELECT 'order_items', COUNT(*) FROM order_items;
```

### Column counts

```sql
SELECT table_name, COUNT(*) AS column_count
FROM information_schema.columns
WHERE table_schema = 'public'
GROUP BY table_name
ORDER BY table_name;
```

### Rows and columns together

```sql
SELECT t.table_name,
       c.column_count,
       t.row_count
FROM (
    SELECT 'customers' AS table_name, COUNT(*) AS row_count FROM customers
    UNION ALL SELECT 'products', COUNT(*) FROM products
    UNION ALL SELECT 'orders', COUNT(*) FROM orders
    UNION ALL SELECT 'order_items', COUNT(*) FROM order_items
) t
JOIN (
    SELECT table_name, COUNT(*) AS column_count
    FROM information_schema.columns
    WHERE table_schema = 'public'
    GROUP BY table_name
) c ON c.table_name = t.table_name
ORDER BY t.table_name;
```

Why the row counts differ even though it is one dataset:

- `customers` and `orders` are both 99,441 because Olist creates a new `customer_id` for every order, so the two tables are one-to-one.
- `order_items` is larger (112,650) because one order can contain several items.
- `products` is smaller (32,951) because it is a catalog that lists each product once.

*<img width="1440" height="900" alt="table structure with datatypes" src="https://github.com/user-attachments/assets/122c2dcd-a19b-461b-9d3e-736f85e3a57e" />


*

---

## 2. Data Dictionary

### customers

| Column | Description | Type |
|---|---|---|
| customer_id | Key for each order. A customer gets a new ID for every order. | VARCHAR(32), primary key |
| customer_unique_id | Stable ID for a real customer. It repeats across orders for repeat buyers. | VARCHAR(32) |
| customer_zip_code_prefix | First 5 digits of the customer's zip code | VARCHAR(5) |
| customer_city | Customer's city | VARCHAR(100) |
| customer_state | Two-letter Brazilian state code | CHAR(2) |

### products

| Column | Description | Type |
|---|---|---|
| product_id | Unique product ID | VARCHAR(32), primary key |
| product_category_name | Product category (in Portuguese) | VARCHAR(100) |
| product_name_length | Number of characters in the product name | INTEGER |
| product_description_length | Number of characters in the description | INTEGER |
| product_photos_qty | Number of product photos | INTEGER |
| product_weight_g | Weight in grams | INTEGER |
| product_length_cm | Length in centimeters | INTEGER |
| product_height_cm | Height in centimeters | INTEGER |
| product_width_cm | Width in centimeters | INTEGER |

### orders

| Column | Description | Type |
|---|---|---|
| order_id | Unique order ID | VARCHAR(32), primary key |
| customer_id | Links to customers | VARCHAR(32), foreign key |
| order_status | Status such as delivered, shipped, canceled | VARCHAR(20) |
| order_purchase_timestamp | When the order was placed | TIMESTAMP |
| order_approved_at | Payment approval time | TIMESTAMP |
| order_delivered_carrier_date | When the order was handed to the carrier | TIMESTAMP |
| order_delivered_customer_date | When the customer received the order | TIMESTAMP |
| order_estimated_delivery_date | Estimated delivery date | TIMESTAMP |

### order_items

| Column | Description | Type |
|---|---|---|
| order_id | Links to orders | VARCHAR(32), foreign key |
| order_item_id | Item sequence number within the order | INTEGER |
| product_id | Links to products | VARCHAR(32), foreign key |
| seller_id | Seller who fulfilled the item | VARCHAR(32) |
| price | Item price | NUMERIC(10,2) |
| freight_value | Shipping cost for the item | NUMERIC(10,2) |

Primary key of `order_items`: (`order_id`, `order_item_id`).

---

## 3. Obstacles Overcome

1. **Column mismatch in order_items.** My first table definition included a `shipping_limit_date` column that does not exist in the CSV. The import failed with `invalid input syntax for type timestamp: "58.90"` because the price was being read into the date column. I inspected the file with `head -n 3`, saw the real header, and rebuilt the table to match.
2. **Failed imports in the pgAdmin dialog.** The dialog's NULL setting treated only the literal word `NULL` as a missing value, but the files use empty fields. I switched to `\copy` in the PSQL Tool, which loads empty fields as NULL by default.
3. **Load order and foreign keys.** `orders` depends on `customers`, and `order_items` depends on `orders` and `products`, so the tables had to be loaded in the order customers, products, orders, order_items.
4. **Two ID columns in customers.** `customer_id` is unique per order, not per person. To count actual people, I use `COUNT(DISTINCT customer_unique_id)`.
5. **Zip codes.** Stored as `VARCHAR(5)` instead of an integer so leading zeros are preserved.
6. **Finding the real error.** The pgAdmin failure banner does not show the cause. The reason is in **Tools → Processes → View details**, at the bottom of the log.
7. **Inconsistent quoting in the CSVs.** Some values are quoted and some are not, which the CSV parser handles correctly.

---

## 4. Table Structure and Data Types

### Create the tables

```sql
CREATE TABLE customers (
    customer_id               VARCHAR(32) PRIMARY KEY,
    customer_unique_id        VARCHAR(32) NOT NULL,
    customer_zip_code_prefix  VARCHAR(5),
    customer_city             VARCHAR(100),
    customer_state            CHAR(2)
);

CREATE TABLE products (
    product_id                  VARCHAR(32) PRIMARY KEY,
    product_category_name       VARCHAR(100),
    product_name_length         INTEGER,
    product_description_length  INTEGER,
    product_photos_qty          INTEGER,
    product_weight_g            INTEGER,
    product_length_cm           INTEGER,
    product_height_cm           INTEGER,
    product_width_cm            INTEGER
);

CREATE TABLE orders (
    order_id                       VARCHAR(32) PRIMARY KEY,
    customer_id                    VARCHAR(32) REFERENCES customers(customer_id),
    order_status                   VARCHAR(20),
    order_purchase_timestamp       TIMESTAMP,
    order_approved_at              TIMESTAMP,
    order_delivered_carrier_date   TIMESTAMP,
    order_delivered_customer_date  TIMESTAMP,
    order_estimated_delivery_date  TIMESTAMP
);

CREATE TABLE order_items (
    order_id       VARCHAR(32) REFERENCES orders(order_id),
    order_item_id  INTEGER,
    product_id     VARCHAR(32) REFERENCES products(product_id),
    seller_id      VARCHAR(32),
    price          NUMERIC(10,2),
    freight_value  NUMERIC(10,2),
    PRIMARY KEY (order_id, order_item_id)
);
```


### Load the data

All four tables were loaded in the PSQL Tool (right-click the database, PSQL Tool), in this order because of the foreign keys:

```
\copy public.customers FROM '/Users/maheshbashyal/Desktop/olist_customers_dataset.csv' WITH (FORMAT csv, HEADER)
\copy public.products FROM '/Users/maheshbashyal/Desktop/olist_products_dataset.csv' WITH (FORMAT csv, HEADER)
\copy public.orders FROM '/Users/maheshbashyal/Desktop/olist_orders_dataset.csv' WITH (FORMAT csv, HEADER)
\copy public.order_items FROM '/Users/maheshbashyal/Desktop/olist_order_items_dataset.csv' WITH (FORMAT csv, HEADER)

```


### Show structure and data types

```sql
SELECT table_name, column_name, data_type, character_maximum_length, is_nullable
FROM information_schema.columns
WHERE table_schema = 'public'
ORDER BY table_name, ordinal_position;
```

*<img width="1440" height="900" alt="select number of rows from each table" src="https://github.com/user-attachments/assets/c06908a8-199f-409c-99e9-c2341abf3538" />

*

---

## 5. SELECT * From Each Table

```sql
SELECT * FROM customers   LIMIT 20;
```

*<img width="1440" height="900" alt="rows from customers" src="https://github.com/user-attachments/assets/0eed85a1-bb35-48b4-bafe-cf5a45c787f2" />
*

```sql
SELECT * FROM products    LIMIT 20;
```

*<img width="1440" height="900" alt="rows from products" src="https://github.com/user-attachments/assets/91eaa556-0411-4800-9145-807f90b9bed6" />
*

```sql
SELECT * FROM orders      LIMIT 20;
```

*<img width="1440" height="900" alt="rows from orders" src="https://github.com/user-attachments/assets/ce2e2da0-5b38-45d6-9008-d7aa5437781c" />
*

```sql
SELECT * FROM order_items LIMIT 20;
```

*<img width="1440" height="900" alt="rows from order items" src="https://github.com/user-attachments/assets/704bafbf-2933-497a-b9a8-38406207dc4f" />

*

---

## 6. Interesting Queries

### 6.1 Join and group by: orders by customer state

```sql
SELECT c.customer_state,
       COUNT(o.order_id) AS total_orders,
       COUNT(*) FILTER (WHERE o.order_status = 'delivered') AS delivered_orders
FROM customers c
JOIN orders o ON o.customer_id = c.customer_id
GROUP BY c.customer_state
ORDER BY total_orders DESC;
```

*<img width="1440" height="900" alt="Interesting queries" src="https://github.com/user-attachments/assets/ed976925-a63a-4cfc-8056-c1c88d4ed9d8" />


*

### 6.2 Join and aggregate: revenue by product category

```sql
SELECT p.product_category_name,
       COUNT(*)                AS items_sold,
       ROUND(SUM(oi.price), 2) AS total_revenue,
       ROUND(AVG(oi.price), 2) AS avg_price
FROM order_items oi
JOIN products p ON p.product_id = oi.product_id
GROUP BY p.product_category_name
ORDER BY total_revenue DESC
LIMIT 10;
```

*<img width="1440" height="900" alt="product category by name" src="https://github.com/user-attachments/assets/65613a54-b212-475f-a044-5d67780aeaf8" />

*

### 6.3 Multi-table join: revenue by category and state

```sql
SELECT p.product_category_name,
       c.customer_state,
       COUNT(*)                AS items_sold,
       ROUND(SUM(oi.price), 2) AS total_revenue
FROM order_items oi
JOIN orders    o ON o.order_id    = oi.order_id
JOIN customers c ON c.customer_id = o.customer_id
JOIN products  p ON p.product_id  = oi.product_id
GROUP BY p.product_category_name, c.customer_state
ORDER BY total_revenue DESC
LIMIT 15;
```

*
<img width="1440" height="900" alt="multiple join by category and state" src="https://github.com/user-attachments/assets/029f8309-3aed-411a-b305-682ad089511c" />


*

### 6.4 Group by: order status breakdown

```sql
SELECT order_status, COUNT(*) AS order_count
FROM orders
GROUP BY order_status
ORDER BY order_count DESC;
```

*<img width="1440" height="900" alt="grouped by only single table" src="https://github.com/user-attachments/assets/ec387633-badd-4e04-a821-e6e94da8665a" />


*

### 6.5 Top 10 cities by customer records

```sql
SELECT customer_city, customer_state, COUNT(*) AS customer_count
FROM customers
GROUP BY customer_city, customer_state
ORDER BY customer_count DESC
LIMIT 10;
```

*<img width="1440" height="900" alt="top 10 cities by customer records" src="https://github.com/user-attachments/assets/3607ba8d-aaf3-4af3-828f-f145056462da" />


*


```

This shows why `customer_unique_id` is needed to count actual people, since `customer_id` is created per order.

*
*
