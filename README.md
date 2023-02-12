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

# Useful functions and tips:
* MySQL uses three value logic **True, False, Unknown** when I do a condition on a null value it returns a Unknown result. to evaluate a null value we can use the operator *IS NULL*

* **sum**: sum of int values used with group by of where

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



