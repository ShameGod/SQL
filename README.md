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
Not used yet

# Exercices: 

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



