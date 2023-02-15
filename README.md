# Introduction
repository where I save all my work with SQL and all the stuff that I learned

# Order of execution of SQL queries:
## From / Join:
To determine the data of interest.
## Where:
To filter the records that do not meet the conditions
## GROUP BY: 
To group records based on the data in one or more columns
## HAVING: 
This clause filters the records from the GROUP BY operation that do not meet some conditions
## SELECT: 
To get the desired results 
## ORDER BY: 
To order in ascending or descending order 
## LIMIT/ OFFSET:
Not used yet:

# JOINS: 
![image](https://user-images.githubusercontent.com/42012627/218751410-474d4ffd-b5fe-43d0-aa87-93476fe2d72d.png)
source: https://dataschool.com/how-to-teach-people-sql/sql-join-types-explained-visually/

# Useful functions and tips:
* MySQL uses three value logic **True, False, Unknown** when I do a condition on a null value it returns a Unknown result. to evaluate a null value we can use the operator *IS NULL*

* **Normalisation**: is the process of splitting a table into multiple other tables. For example if we have a table of cars with a column for the engine, we can split the table into one for the cars and another one for the engines, so that the engines can grow separatly of the cars

* **Case When**: It is a way of adding if and else statement to an SQL Query. It is the equivalent of doing a Select ... WHERE ..., with the possibility of adding an else statement

* **DATEDIFF**: returns the time difference between two dates

* **LIKE**: to see if the value of a column contains an element

* **Declaring two tables**: declaring two tables isn't a good practice because the result is a table with multiple lines corresponding to all the possible combination. You should rather delare a JOIN.


# Exercices: 

## Basic queries (SELECT, WHERE ..):

### Select with condition
https://leetcode.com/problems/big-countries
```
SELECT c.name, c.population, c.area
FROM World as c
WHERE c.population >= 25000000
    OR c.area >= 3000000
```
### Not in condition: 
https://leetcode.com/problems/customers-who-never-order
```
Select c.name as Customers
From customers as c
Where c.id NOT IN (
    SELECT customerId From Orders 
)
```
### INSERT syntax: 
``` INSERT INTO movies VALUES (4, "Toy Story 4", "El Directore", 2015, 90); ```
### UPDATE syntax: 

**NOTE: It is strongly recommanded to run a SELECT query before an UPDATE query to make sure not to edit an undesired line**

```
UPDATE mytable
SET column = value_or_expr, 
    other_column = another_value_or_expr, 
    …
WHERE condition;
```

### DELETE syntax:
**NOTE: It is strongly recommanded to run a SELECT query before an UPDATE query to make sure not to edit an undesired line**
``` 
DELETE FROM mytable
WHERE condition; 
```

### CREATE a table: 
* Syntax: 
```
CREATE TABLE IF NOT EXISTS mytable (
    column DataType TableConstraint DEFAULT default_value,
    another_column DataType TableConstraint DEFAULT default_value,
    …
);
```
* Data types: each database have its own special types but there are some common types such as 
    - Integer, Float, Double, Real 
    - Boolean: Can ce represented as integer (1 for true and 0 for false)
    - VARCHAR, TEXT
    - DATE, DATETIME
    - BLOB: binary objects 

* Contrains: 
    - PRIMARY KEY
    - FOREIGN KEY
    - AUTOINCREMENT
    - UNIQUE
    - NOT NULL
    - CHECK

### ALTER a table:
Very similat to create a table, here is its syntax to :
* add a column:
```
ALTER TABLE mytable
ADD column DataType OptionalTableConstraint 
    DEFAULT default_value;
```
* remove a column:
```
ALTER TABLE mytable
DROP column_to_be_deleted;
```
* rename the table: 
```
ALTER TABLE mytable
RENAME TO new_table_name;
```

### DROP a table: 
Different from Delete because it edits the database schema: 
```
DROP TABLE IF EXISTS mytable;
```

## Aggregate functions:

### Aggregate multiple products in a single cell for a group by statement
https://leetcode.com/problems/group-sold-products-by-the-date/
```
SELECT a.sell_date, 
    count( DISTINCT product) as num_sold, 
    GROUP_CONCAT(DISTINCT product ORDER BY product ASC SEPARATOR ',') as products
FROM Activities as a
GROUP BY a.sell_date 
ORDER BY a.sell_date ASC 
```

### MIN():
https://leetcode.com/problems/game-play-analysis-i/
```
SELECT a.player_id, MIN(a.event_date) as first_login
FROM Activity as a
GROUP BY a.player_id
```

### Distinct:
https://sqlbolt.com/lesson/select_queries_with_outer_joins task 3
```
SELECT DISTINCT Building_name,  role
FROM buildings LEFT JOIN employees
ON buildings.building_name=employees.building
```

## Windowing:
*Definition from wikipedia: a window function or analytic function is a function that uses values from one or more rows to return a value for each row (different from an aggregation function which returns a signe value for multiple rows) Windowing functions have an OVER clause*

syntax: ``` function_name ( * ) OVER window_name ```
The window_name corresponds to a partition, if there is no window_name the hole table is a signle partition. The function_name is applied to every partition. 

Here is a list of functions that can be used as windowing functions: 
* For aggregation: AVG, MAX, MIN, SUM, COUNT
* For Ranking: 
    * ROW_NUMBER(): assigns a rank to all the rows starting from 1 to the last element. **it requires an OVER clause**. Read this https://www.stratascratch.com/blog/the-ultimate-guide-to-sql-window-functions.
    * RANK(): Very similar to ROW_NUMBER except the fact that it assigns the same rank to the row with the same value. It also skips ranks. It is usefull for queries like this:
    ```
    SELECT from_user,
       total_emails
    FROM
      (SELECT from_user,
              COUNT(*) AS total_emails,
              RANK() OVER (
                           ORDER BY count(*) DESC) rnk
       FROM google_gmail_emails
       GROUP BY from_user) a
    WHERE rnk = 1
    ```
* For value: functions that assign values to rows from other rows:
    * LAG(): Selects the values from the previous table. It needs the rows to be ordered, example: 
    ```
    SELECT inspection_date::DATE,
           COUNT(violation_id),
           LAG(COUNT(violation_id)) OVER(ORDER BY inspection_date::DATE)
    FROM sf_restaurant_health_violations
    GROUP BY 1
    ```
    * LEAD(): is the opposite of LAG 
    * 

## Join operation with the same table: 
https://leetcode.com/problems/rising-temperature/submissions/894751379/

```
SELECT w1.id as id
FROM weather as w1 JOIN weather as w2
ON DATEDIFF(w1.recordDate, w2.recordDate)=1 
AND w1.temperature > w2.temperature
```

## Deleting elements matching a condition:
https://leetcode.com/problems/delete-duplicate-emails/submissions/894083627/
```
DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id
```

## Check that an element does NOT exist in a table (easy)
https://leetcode.com/problems/customers-who-never-order/submissions/

```
SELECT c.name as customers
FROM customers as c
WHERE NOT EXISTS 
(
    SELECT o.id 
    FROM orders as o
    WHERE o.customerid=c.id
)
```

## Using GroupBY only: 
https://leetcode.com/problems/department-highest-salary/submissions/

```
SELECT e.name as Employee, d.name as Department, e.Salary
FROM Employee e JOIN Department d
ON e.departmentId=d.id,
(
    Select d.name, MAX(e.salary) as max
    FROM Employee e JOIN Department d 
    ON e.departmentId=d.id
    GROUP BY d.name
) as department_max
WHERE d.name=department_max.name AND e.Salary=department_max.max
```



## Using Partition with DANSE_RANK()
https://leetcode.com/problems/department-top-three-salaries/

```
SELECT Department, employee, salary FROM (
    SELECT d.name AS Department
        , e.name AS employee
        , e.salary
        , DENSE_RANK() OVER (w) AS drk
    FROM Employee e JOIN Department d ON e.DepartmentId= d.Id
    WINDOW w AS (PARTITION BY d.name ORDER BY e.salary DESC)
) as t WHERE t.drk <= 3
```



