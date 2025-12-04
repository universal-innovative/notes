/\_
When we say SELECT column_name FROM table_name, Postresql reads this backwards, it has to decide which table_name we are referencing, it knows which database we are referencing as query editor opens with it, first it looks for the table name and then it looks for the corresponding column name.

In general its not good practice to use an asterisk (\*) in the SELECT statement if you don't really need all columns

### DISTINCT help us to know uniqui values inside a column

### COUNT function returns the number of input rows that match a specific condition of a query, SELECT COUNT(\*)

### WHERE stateme allows us to specify conditions on columns for the rows to be returned

Comparison Operators compare a column value to something

### 📒 SQL Comparison Operators

| Operator              | Meaning                                        | Example                                   | Result                                         |
| --------------------- | ---------------------------------------------- | ----------------------------------------- | ---------------------------------------------- |
| `=`                   | Equal to                                       | `WHERE salary = 50000`                    | Rows where salary is exactly 50000             |
| `<>` or `!=`          | Not equal to                                   | `WHERE department <> 'HR'`                | Rows where department is not HR                |
| `>`                   | Greater than                                   | `WHERE age > 30`                          | Rows with age above 30                         |
| `<`                   | Less than                                      | `WHERE age < 30`                          | Rows with age below 30                         |
| `>=`                  | Greater than or equal to                       | `WHERE experience >= 5`                   | Rows with 5 or more years of experience        |
| `<=`                  | Less than or equal to                          | `WHERE score <= 80`                       | Rows with score 80 or lower                    |
| `BETWEEN ... AND ...` | Within range (inclusive)                       | `WHERE age BETWEEN 18 AND 25`             | Rows where age is 18, 19, … 25                 |
| `IN (… , …)`          | Matches any in a list                          | `WHERE city IN ('Delhi','Bangalore')`     | Rows where city is either Delhi or Bangalore   |
| `NOT IN (… , …)`      | Not in list                                    | `WHERE city NOT IN ('Delhi','Bangalore')` | Rows where city is neither Delhi nor Bangalore |
| `LIKE`                | Pattern match                                  | `WHERE name LIKE 'A%'`                    | Rows where name starts with A                  |
| `ILIKE`               | Case-insensitive pattern match (Postgres only) | `WHERE name ILIKE 'a%'`                   | Rows where name starts with A or a             |
| `IS NULL`             | Value is NULL                                  | `WHERE manager_id IS NULL`                | Rows with no manager assigned                  |
| `IS NOT NULL`         | Value is not NULL                              | `WHERE manager_id IS NOT NULL`            | Rows where manager is assigned                 |

Logical operators allows us to combine multiple comparison operators

### 📒 SQL Logical Operators

| Operator     | Meaning                                                   | Example                                                                       | Result                                           |
| ------------ | --------------------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------ |
| `AND`        | All conditions must be true                               | `WHERE department = 'Sales' AND salary > 50000`                               | Rows in Sales **and** salary above 50k           |
| `OR`         | At least one condition must be true                       | `WHERE city = 'Delhi' OR city = 'Bangalore'`                                  | Rows where city is Delhi **or** Bangalore        |
| `NOT`        | Negates a condition                                       | `WHERE NOT department = 'HR'`                                                 | Rows where department is **not** HR              |
| `AND` + `OR` | Combine multiple conditions (use parentheses for clarity) | `WHERE (department = 'Sales' OR department = 'Marketing') AND salary > 40000` | Rows in Sales/Marketing **and** salary above 40k |

⚡ **Pro tip**: Always use parentheses `()` when mixing `AND` and `OR`, because `AND` has higher precedence (it’s evaluated first).

### ORDER BY is used to sort rows based on a column value, in either ascending or descending order, most of the times its last of the opertion

We can use **ORDER BY** multiple columns, it makes sense when one column has duplicate entries.

### LIMIT allows us to limit no of rows returned for a query.

goes at the very end of the query request, can be used with order by to get top n or last n entries.

### 📒 SQL Aggregate Functions

| Function                 | Description                                          | Example                                             | Result                        |
| ------------------------ | ---------------------------------------------------- | --------------------------------------------------- | ----------------------------- |
| `COUNT(*)`               | Counts **all rows**, including duplicates & NULLs    | `SELECT COUNT(*) FROM employees;`                   | Total number of employees     |
| `COUNT(column)`          | Counts rows where column is **NOT NULL**             | `SELECT COUNT(salary) FROM employees;`              | Employees with a salary value |
| `COUNT(DISTINCT column)` | Counts unique, non-NULL values                       | `SELECT COUNT(DISTINCT department) FROM employees;` | Number of unique departments  |
| `SUM(column)`            | Adds up all numeric values (ignores NULLs)           | `SELECT SUM(salary) FROM employees;`                | Total payroll amount          |
| `AVG(column)`            | Calculates average of numeric values (ignores NULLs) | `SELECT AVG(salary) FROM employees;`                | Average salary                |
| `MIN(column)`            | Finds smallest value in a column                     | `SELECT MIN(salary) FROM employees;`                | Lowest salary                 |
| `MAX(column)`            | Finds largest value in a column                      | `SELECT MAX(salary) FROM employees;`                | Highest salary                |

#### ⚡ Notes

- Aggregate functions return **one row** (a single value) unless combined with `GROUP BY`.
- NULL values are ignored by all aggregates except `COUNT(*)`.
- Often used together with `GROUP BY` and `HAVING`.

## GROUP BY

    It allows us to aggregate data and apply functions to better understand how data is distributed per category
    SELECT category_col, AGG(data_col)
    FROM table
    GROUP BY category_col

The GROUP BY clause must appear right after a FROM or
WHERE statement
In the SELECT statement, columns must either have an aggregate function or be in the GROUP BY call.

### The Rule Aggregation

#### In the SELECT statement, columns must either:

1. Have an **aggregate** function,
   or
2. Be in the **GROUP BY** clause.

#### Part1: Columns must have an aggregate funciton

- An aggregate function takes many rows and squeezes them into a single value.
- Example: COUNT(), SUM(), AVG(),MIN(),MAX().
- If a column is inside an aggregare, SQL doesn't need to worry about grouping —it's already being reduced to one number.

```sql
   SELECT COUNT(*), AVG(salary)
   FROM employees;
```

In resulting table both columns are aggregated. Therefore allowed.

#### Part2: Columns must be in the GROUP BY

- If we want to see column values in the result, SQL must know how to organize rows.
- That's what GROUP BY does: it splits rows into buckets before applying aggregates.

```SQL
 SELECT department, AVG(salary)
 FROM employees
 GROUP BY department
```

The resulting table has two colums department and avg_salary, the how will the rows be aggregated to this resulting table can't be done to one row we have several departments. answer is departments.

#### ❌ What happens if neither applies

```
    SELECT department, AVG(salary)
    FROM employees;
```

Error: _Column department must appear in the GROUP BY clause or be used in an aggregate function._

We have two columns department and avg salary,

- AVG(salary) → aggregated across the whole table.
- department → not aggregated, not grouped.
- SQL doesn’t know whether to return:
  - One row per department? (needs GROUP BY)
  - Or one row total? (but then which department to show?)

### HAVING

The HAVING allows us to filter **after** an aggrefation has alredy taken place. We cannot use WHERE to filter based off of aggregate results, because those happen afer a where is executed.

## JOINS

### AS

As clause allows us to create an 'alias' for a column or result.
SELECT col AS new_name
FROM table

The AS operator gets executed at the very end of a query, 'alias' are assigned at very end, meaning that we can not use the ALIAS inside a where operator.
We can just use them for data out put in the table.

SQL JOINs are specifically designed to combine data from two or more tables based on a related column (like a foreign key)

# 📒 SQL JOIN Reference

```sql
SELECT A.column1, B.column2, ...
FROM tableA A
<JOIN TYPE> JOIN tableB B
    ON A.key = B.key;
```

🔹 Replace `<JOIN TYPE>` with:

- `INNER`
- `LEFT` (or `LEFT OUTER`)
- `RIGHT` (or `RIGHT OUTER`)
- `FULL` (or `FULL OUTER`)

| JOIN Type                         | Venn Diagram Concept                                                 | SQL Example                                                                                                  | Use Case                                                                                                           |
| --------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| **INNER JOIN**                    | ![alt text](5311.png)🔵🟢 Only the overlap between Table A & Table B | `sql SELECT c.customer_id, p.amount FROM customer c INNER JOIN payment p ON c.customer_id = p.customer_id; ` | Get only customers **who made payments**. (Exclude customers without payments, exclude payments with no customer.) |
| **LEFT JOIN** (LEFT OUTER JOIN)   | ![alt text](5511.png)🔵🟢 Entire left circle + overlap               | `sql SELECT c.customer_id, p.amount FROM customer c LEFT JOIN payment p ON c.customer_id = p.customer_id; `  | Get **all customers**, even those without payments. Payments missing → NULL.                                       |
| **RIGHT JOIN** (RIGHT OUTER JOIN) | ![alt text](5711.png)🔵🟢 Entire right circle + overlap              | `sql SELECT c.customer_id, p.amount FROM customer c RIGHT JOIN payment p ON c.customer_id = p.customer_id; ` | Get **all payments**, even those that don’t match a customer. Customers missing → NULL.                            |
| **FULL JOIN** (FULL OUTER JOIN)   | ![alt text](5811.png)🔵🟢 Both entire circles + overlap              | `sql SELECT c.customer_id, p.amount FROM customer c FULL JOIN payment p ON c.customer_id = p.customer_id; `  | Get **all customers** and **all payments**, matched where possible, unmatched filled with NULL.                    |
| **CROSS JOIN**                    | 🔵 x 🟢 Cartesian product (all combinations)                         | `sql SELECT * FROM product CROSS JOIN store; `                                                               | Get every **possible pairing** of rows (e.g., all product–store combinations). ⚠️ Explodes row count!              |
| **SELF JOIN**                     | One circle joined to itself                                          | `sql SELECT e1.name, e2.name AS manager FROM employee e1 JOIN employee e2 ON e1.manager_id = e2.id; `        | Compare rows **within the same table** (e.g., employee–manager relationships).                                     |

---

## 🔎 Quick Visual (Venn concepts)

- **INNER JOIN** → overlap only.
- **LEFT JOIN** → everything in left + overlap.
- **RIGHT JOIN** → everything in right + overlap.
- **FULL JOIN** → all of both circles.
- **CROSS JOIN** → multiplication (no Venn, but a grid).
- **SELF JOIN** → one circle mapped back to itself.

---

⚡ **Cheat tip:**

- Use **INNER JOIN** when you only care about matches.
- Use **LEFT JOIN** when left table is “primary” (must keep all its rows).
- Use **FULL JOIN** rarely — when you need everything from both sides.
- Avoid **CROSS JOIN** unless you _really_ want all combinations.

---

👉 Do you want me to actually **draw and send you clean Venn diagram images** for each JOIN type so you can paste them directly into your notes?

### FULL OUTER JOIN

SELECT \* FROM TableA
FULL OUTER JOIN TableB
ON TableA.col_match = TableB.com_match

### LEFT OUTER JOIN

Records that are in left table, if there is no match with the right table, the results are null

SELECT \* FROM TableA
LEFT OUTER JOIN TableB
ON TableA.col_match = TableB.col_match

In INNER JOIN and FULL JOIN, Table order didn't matter, When it comes to LEFT OUTER JOIN or LEFT JOIN the order does matter because we have to specify what the left table is going to be for above query We will grab thing that are exclusive to TableA or could be found in both TableA and TableB, We are not gonna returning anyting exclusive to tableB, Will will return null for the fields not present in TableB

### RIGHT OUTER JOIN

SELECT \* FROM TableA
RIGHT OUTER JOIN TableB
ON TableA.col_match = TableB.col_match

This would be same as switching the table in LEFT OUTER JOIN

### UNIONS

Combines the result-set of two or more SELECT statements.
SELECT column_name(s) FROM table1
SELECT column_name(s) FROM table2

# Creacting Databases And Tables

### DATA TYPES

| Category      | Data Types                                 | Description                                                      | Example                          |
| ------------- | ------------------------------------------ | ---------------------------------------------------------------- | -------------------------------- |
| **Boolean**   | `BOOLEAN`                                  | Logical true/false values. Stored as `TRUE`, `FALSE`, or `NULL`. | `is_active BOOLEAN DEFAULT TRUE` |
| **Character** | `CHAR(n)`                                  | Fixed-length string. Pads with spaces if shorter than `n`.       | `CHAR(5)` → `'Hi   '`            |
|               | `VARCHAR(n)`                               | Variable-length string, max length `n`.                          | `VARCHAR(20)` → `"California"`   |
|               | `TEXT`                                     | Variable-length string, unlimited size.                          | `"This is a long paragraph..."`  |
| **Numeric**   | `SMALLINT`                                 | -32,768 to 32,767 (2 bytes).                                     | `25`                             |
|               | `INTEGER` / `INT`                          | -2,147,483,648 to 2,147,483,647 (4 bytes).                       | `1000`                           |
|               | `BIGINT`                                   | Very large integers (8 bytes).                                   | `9000000000`                     |
|               | `NUMERIC(p,s)` / `DECIMAL(p,s)`            | Exact fixed-point number. `p` = precision, `s` = scale.          | `NUMERIC(5,2)` → `123.45`        |
|               | `REAL`                                     | 4-byte floating-point (approx).                                  | `3.14`                           |
|               | `DOUBLE PRECISION`                         | 8-byte floating-point (approx).                                  | `123.456789`                     |
| **Temporal**  | `DATE`                                     | Calendar date (year, month, day).                                | `'2025-09-26'`                   |
|               | `TIME [WITHOUT TIME ZONE]`                 | Time of day.                                                     | `'14:35:00'`                     |
|               | `TIME WITH TIME ZONE` (`TIMETZ`)           | Time + time zone.                                                | `'14:35:00+05:30'`               |
|               | `TIMESTAMP [WITHOUT TIME ZONE]`            | Date + time.                                                     | `'2025-09-26 14:35:00'`          |
|               | `TIMESTAMP WITH TIME ZONE` (`TIMESTAMPTZ`) | Date + time + time zone.                                         | `'2025-09-26 14:35:00+05:30'`    |
|               | `INTERVAL`                                 | A span of time (duration).                                       | `'2 days 3 hours 15 minutes'`    |

⚡ **Cheat notes:**

- Use `BOOLEAN` for flags.
- Use `VARCHAR` for strings with known max length; `TEXT` if it’s unbounded.
- Use `NUMERIC` for money/financial data (exact precision).
- Use `REAL/DOUBLE PRECISION` for scientific/approximate values.
- Use `TIMESTAMPTZ` for real-world apps (because time zones matter).

### PRIMARY KEY AND FOREIGN KEYS

- A `primary key` is a column or group of columns that uniquely identifies each row in a table (e.g., `customer_id` in the `customer` table).
- **Primary keys** are often the target of joins, since **foreign keys in other tables reference them**.
- A `foreign key `is a column or group of columns in one table that refers to the **primary key in another table**.
- The table with the foreign key is the **referencing (child) table**.
- The table with the primary key being referenced is the **referenced (parent) table**.
- A table can have **multiple foreign keys**, depending on how it relates to other tables.
- Example: In the `payment` table, `payment_id` is its **primary key**, while `customer_id` is a **foreign key** referencing the `customer` table’s `customer_id` (primary key).

**Customer Table**

- `customer_id` → **Primary Key** (unique per customer).

**Payment Table**

- `payment_id` → **Primary Key** (unique per payment).
- `customer_id` → **Foreign Key** (points to `customer.customer_id`).

### 🔹 Relationship

- The **customer** table is the **parent (referenced table)**.
- The **payment** table is the **child (referencing table)**.
- Each payment **must belong to a valid customer**.
- But one customer can have **many payments**.

👉 This is a **One-to-Many relationship**:
**1 Customer → Many Payments**

### ✅ Example

**Customer Table**

```
customer_id | name
------------|------
1           | Alice
2           | Bob
```

**Payment Table**

```
payment_id | customer_id | amount
-----------|-------------|-------
101        | 1           | 100
102        | 1           | 150
103        | 2           | 200
```

- Alice (customer_id = 1) → has 2 payments.
- Bob (customer_id = 2) → has 1 payment.
- If you try to insert a payment with `customer_id = 3` (no such customer), PostgreSQL will throw a **foreign key violation error**.

✅ So yes:

- `customer` = **parent table**
- `payment` = **child table**

### CONSTRAINTS

- Constraints are the rules enforced on data columns on table.
- These are used to prevent invalid data from being entered into the database
- This ensures the accuracy and reliability of the data in the database.

-Constraints can be divided nto two main categories: - Column Constraints: Applied to individual columns - Table Constriants: Applied to entire table

- The most common column constraints used:

  - `NOT NULL` Ensures that a column cannot have NULL value.
  - `UNIQUE` Ensures all values in column are different
  - `PRIMARY KEY` Uniquely identifies each row/record in a database table.
  - `FOREIGN KEY` Constraints data based on columns in other tables. By setting up a foreign key, we are also setting up relationship to another table.
  - `CHECK` Ensures all values in a column safisfy certain conditions
  - `EXCLUSION` Ensures that if any two rows compared on the specified column or expression using specified operator that we included in exclusiton constriant, not all of these comparisons will return TRUE

  - Table constriants
    - `CHECK` Checks a condition on inserting or updating data
    - `REFERENCES` To constraint the value stored in the column that must exist in a column in another table.
    - `UNIQUE (column_list)` Forces the values stored in the columns listed inside the parentheses to be unique
    - `PRIMARY KEY (column_list)` Allows to define the primary key that consists of multiple columns.

### CREATE

```sql
 CREATE TABLE table_name(
    column_name TYPE column_constraint,
    column_name TYPE column_constraint,
    table_constraint table_constraint
 ) INHERITS existing_table_name;
```

#### SERIAL

- In PostgreSQL, a sequence is a special kind of database object that generates a sequence of integers.
- A sequences is often used as the primary key column in a table
- SERIAL create a sequence object and set the next value generated by the sequence as the default value for the column.
- This is perfect for the primary key, because it logs unque integer entries for you automatically upon insertion.
- If a row is later removed, the column with the SERIAL data type will `not` adjust.

### INSERT

Allows to add rows to table.

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

- You must match the **columns** list with the **values** list in order and count.
- If you provide values for all columns in exact order, you can skip the column list (not recommended).
  Got it 👍 — you want the **general `INSERT … SELECT` syntax** (insert data into one table from another). This is very common when migrating, archiving, or copying data.

```sql
INSERT INTO target_table (column1, column2, ...)
SELECT column1, column2, ...
FROM source_table
WHERE condition;
```

- Inserted row values must match up for the table, inculding constraint
- SERIAL columns do not need to be provided a value

### UPDATE

General syntax

```sql
   UPDATE table
   SET column1 = value1,
       column1 = value2,...
   WHERE condition;
```

Set based on another column

```sql
   UPDATE table
   SET column1 = column2,
   WHERE condition;
```

Set based on another table values

```sql
   UPDATE TableA
   SET column1 = TableB.column2,
   FROM TableB
   WHERE TableA.id - TableB.id;
```

Return affected rows

```sql
   UPDATE table
   SET column1 = column2
   RETURNING column1, column2
```

### DELETE

```sql
   DELETE FROM table
   WHERE condition;
```

```sql
   DELETE FROM tableA
   USING tableB
   WHERE tableA.id = tableB.id;
```

```sql
   DELETE FROM table
```

```sql
   DELETE FROM table
   RETURNING column1
```

### ALTER

- Adding, dropping, or renaming columns
- Changing a column's date type
- Set DEFAULT values for a column
- Add CHECK constraints
- Rename table

Got it 👍 — here’s a **cheat sheet for `ALTER TABLE`** in the same style you shared for `DELETE`.

---

# 📒 ALTER TABLE Syntax Reference

Add a new column

```sql
ALTER TABLE table_name
ADD COLUMN column_name data_type;
```

Drop (delete) a column

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

Rename a column

```sql
ALTER TABLE table_name
RENAME COLUMN old_name TO new_name;
```

Change column data type

```sql
ALTER TABLE table_name
ALTER COLUMN column_name TYPE new_data_type;
```

Set / drop default value

```sql
ALTER TABLE table_name
ALTER COLUMN column_name SET DEFAULT default_value;

ALTER TABLE table_name
ALTER COLUMN column_name DROP DEFAULT;
```

Add a constraint (e.g., foreign key)

```sql
ALTER TABLE child_table
ADD CONSTRAINT fk_name
FOREIGN KEY (column_name)
REFERENCES parent_table (column_name);
```

Drop a constraint

```sql
ALTER TABLE table_name
DROP CONSTRAINT constraint_name;
```

Rename the table itself

```sql
ALTER TABLE old_table_name
RENAME TO new_table_name;
```

⚡ **Pro tip:** `ALTER TABLE` is one of the most versatile commands in PostgreSQL — it can reshape your schema without dropping/recreating tables.

### DROP table

```sql
DROP TABLE table_name;
```

Drop a table if it exists

```sql
DROP TABLE IF EXISTS table_name;
```

Drop multiple tables

```sql
DROP TABLE table1, table2, table3;
```

Drop a table with all dependent objects (⚠️ use carefully)

```sql
DROP TABLE table_name CASCADE;
```

Drop a table but restrict if there are dependencies (default)

```sql
DROP TABLE table_name RESTRICT;
```

### CHECK Constraint

Add a CHECK constraint when creating a table

```sql
CREATE TABLE employees (
    emp_id SERIAL PRIMARY KEY,
    salary NUMERIC CHECK (salary > 0),
    age INT CHECK (age >= 18)
);
```

Add a CHECK constraint to an existing table

```sql
ALTER TABLE employees
ADD CONSTRAINT chk_salary_positive
CHECK (salary > 0);
```

Drop a CHECK constraint

```sql
ALTER TABLE employees
DROP CONSTRAINT chk_salary_positive;
```

Using multiple conditions in CHECK

```sql
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    price NUMERIC,
    discount NUMERIC,
    CHECK (price >= 0 AND discount >= 0 AND discount <= price)
);
```

Naming a CHECK constraint explicitly

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    quantity INT,
    CONSTRAINT chk_quantity CHECK (quantity > 0)
);
```

⚡ **Pro tips:**

- `CHECK` is row-level validation → ensures each row meets the condition.
- PostgreSQL allows complex `CHECK` expressions (with functions, multiple columns, etc.).
- If a row violates the condition, the insert/update fails with an error.

\_/

UPDATE
![alt text](Screen+Shot+2016-04-17+at+12.22.49+PM.png)



