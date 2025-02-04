# [LeetCode - SQL 50](https://leetcode.com/studyplan/top-sql-50/)

Below are the queries I came up with to solve the LeetCode problems from the SQL 50 Study Plan, using PostgreSQL.

## [Recyclable and Low Fat Products](https://leetcode.com/problems/recyclable-and-low-fat-products/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT product_id
FROM PRODUCTS
WHERE low_fats = 'Y'
    AND recyclable = 'Y';
```

## [Find Customer Referee](https://leetcode.com/problems/find-customer-referee/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT name
FROM Customer
WHERE COALESCE(referee_id,0) <> 2;
```

## [Big Countries](https://leetcode.com/problems/big-countries/submissions/1151532070/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT name, population, area
FROM World
WHERE area >= 3000000
    OR population >= 25000000;
```

## [Article Views I](https://leetcode.com/problems/article-views-i/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT DISTINCT author_id AS id
FROM Views
WHERE author_id = viewer_id;
```

## [Invalid Tweets](https://leetcode.com/problems/invalid-tweets/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT tweet_id
FROM Tweets
WHERE LENGTH(content) > 15;
```

## [Replace Employee ID With The Unique Identifier](https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT u.unique_id, e.name
FROM Employees e
    LEFT JOIN EmployeeUNI u
    ON e.id = u.id;
```

## [Product Sales Analysis](https://leetcode.com/problems/product-sales-analysis-i/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT p.product_name, s.year, s.price
FROM Sales s
    LEFT JOIN Product p
    ON s.product_id = p.product_id;
```

## [Customer Who Visited but Did Not Make Any Transactions](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT
v.customer_id,
COUNT(v.visit_id) - COUNT(t.transaction_id) AS count_no_trans
FROM Visits v
    LEFT JOIN Transactions t
    ON v.visit_id = t.visit_id
GROUP BY v.customer_id
HAVING (COUNT(v.visit_id) - COUNT(t.transaction_id)) > 0;
```

## [Rising Temperature](https://leetcode.com/problems/rising-temperature/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT w.id
FROM Weather w
WHERE w.temperature > (SELECT z.temperature FROM Weather z WHERE w.recordDate - interval '1 day' = z.recordDate);
```

## [Average Time of Process per Machine](https://leetcode.com/problems/average-time-of-process-per-machine/?envType=study-plan-v2&envId=top-sql-50)
```
WITH a AS (
SELECT machine_id,
    process_id,
    SUM(
        CASE WHEN activity_type = 'end' THEN timestamp ELSE 0 END -
        CASE WHEN activity_type = 'start' THEN timestamp ELSE 0 END
    ) AS x
FROM Activity
GROUP BY 1,2
)
SELECT machine_id, ROUND(CAST(AVG(x) AS numeric),3) AS processing_time
FROM a
GROUP BY 1;
```

## [Employee Bonus](https://leetcode.com/problems/employee-bonus/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT e.name, b.bonus
FROM Employee e
    LEFT JOIN Bonus b
    ON e.empID = b.empId
WHERE COALESCE(b.bonus,0) < 1000;
```

## [Students and Examinations](https://leetcode.com/problems/students-and-examinations/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT s1.student_id, s1.student_name, s2.subject_name,
    (SELECT COUNT(e.student_id) FROM Examinations e WHERE e.student_id = s1.student_id AND e.subject_name = s2.subject_name) AS attended_exams
FROM Students s1
    CROSS JOIN Subjects s2
GROUP BY 1,2,3
ORDER BY 1,3;
```

## [Managers with at Least 5 Direct Reports](https://leetcode.com/problems/managers-with-at-least-5-direct-reports/description/?envType=study-plan-v2&envId=top-sql-50)
```
WITH table_1 AS (
    SELECT managerId, COUNT(1) AS direct
    FROM Employee
    GROUP BY 1
    HAVING COUNT(1) >= 5
)
SELECT e.name
FROM Employee e
    INNER JOIN table_1 t
    ON t.managerId = e.id;
```

## [Confirmation Rate](https://leetcode.com/problems/confirmation-rate/description/?envType=study-plan-v2&envId=top-sql-50)
```
WITH rate AS (
    SELECT user_id,
    SUM(CASE WHEN action = 'confirmed' THEN 1 ELSE 0 END) AS confirmed,
    COUNT(1) AS total
    FROM Confirmations
    GROUP BY 1
)
SELECT s.user_id, ROUND(COALESCE(r.confirmed/r.total::numeric,0),2) AS confirmation_rate
FROM Signups s
    LEFT JOIN rate r
    ON s.user_id = r.user_id;
```

## [Not Boring Movies](https://leetcode.com/problems/not-boring-movies/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT *
FROM Cinema
WHERE id % 2 <> 0
    AND description NOT LIKE 'boring'
ORDER BY rating DESC;
```

## [Average Selling Price](https://leetcode.com/problems/average-selling-price/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT p.product_id,
    COALESCE(ROUND(SUM(u.units*p.price) / SUM(u.units)::numeric,2),0) AS average_price
FROM Prices p
    LEFT JOIN UnitsSold u
    ON u.product_id = p.product_id
        AND u.purchase_date BETWEEN p.start_date AND p.end_date 
GROUP BY 1;
```

## [Project Employees I](https://leetcode.com/problems/project-employees-i/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT p.project_id, ROUND(AVG(e.experience_years),2) AS average_years
FROM Project p
    INNER JOIN Employee e
    ON p.employee_id = e.employee_id
GROUP BY 1;
```

## [Percentage of Users Attended a Contest](https://leetcode.com/problems/percentage-of-users-attended-a-contest/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT r.contest_id,
ROUND(100*COUNT(1) / (SELECT COUNT(1) FROM Users)::numeric,2) AS percentage
FROM Register r
GROUP BY 1
ORDER BY 2 DESC, 1 ASC;
```

## [Queries Quality and Percentage](https://leetcode.com/problems/queries-quality-and-percentage/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT q.query_name,
    ROUND(AVG(rating/position::numeric),2) AS quality,
    ROUND(100*SUM(CASE WHEN rating < 3 THEN 1 ELSE 0 END) / COUNT(1)::numeric,2) AS poor_query_percentage
FROM Queries q
WHERE q.query_name IS NOT NULL
GROUP BY 1;
```

## [Monthly Transactions I](https://leetcode.com/problems/monthly-transactions-i/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT TO_CHAR(trans_date, 'YYYY-MM') AS month, country,
    COUNT(1) AS trans_count,
    SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY 1,2;
```

## [Immediate Food Delivery II](https://leetcode.com/problems/immediate-food-delivery-ii/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT ROUND(100 * COUNT(CASE WHEN d.order_date = d.customer_pref_delivery_date THEN 1 ELSE NULL END) / COUNT(1)::numeric, 2) AS immediate_percentage
FROM Delivery d
WHERE d.order_date = (SELECT MIN(z.order_date) FROM Delivery z WHERE z.customer_id = d.customer_id);
```

## [Game Play Analysis IV](https://leetcode.com/problems/game-play-analysis-iv/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT ROUND(COUNT(1) / (SELECT COUNT(DISTINCT c.player_id) FROM Activity c)::numeric,2) AS fraction
FROM Activity a
WHERE a.event_date = (SELECT MIN(b.event_date) FROM Activity b WHERE b.player_id = a.player_id) + interval '1 day';
```

## [Number of Unique Subjects Taught by Each Teacher](https://leetcode.com/problems/number-of-unique-subjects-taught-by-each-teacher/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT teacher_id, COUNT(DISTINCT subject_id) AS cnt
FROM Teacher
GROUP BY 1;
```

## [User Activity for the Past 30 Days I](https://leetcode.com/problems/user-activity-for-the-past-30-days-i/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT activity_date AS day,
    COUNT(DISTINCT user_id) AS active_users
FROM Activity
WHERE activity_date BETWEEN DATE '2019-07-27' - interval '29 days' AND '2019-07-27' 
GROUP BY 1;
```

## [Product Sales Analysis III](https://leetcode.com/problems/product-sales-analysis-iii/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT s.product_id, s.year AS first_year, s.quantity, s.price
FROM Sales s
JOIN (SELECT product_id, MIN(year) AS year FROM Sales GROUP BY 1) a
    ON s.product_id = a.product_id
    AND s.year = a.year;
```

## [Classes More Than 5 Students](https://leetcode.com/problems/classes-more-than-5-students/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT class
FROM Courses
GROUP BY 1
HAVING COUNT(1) >= 5;
```

## [Find Followers Count](https://leetcode.com/problems/find-followers-count/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT user_id, COUNT(1) AS followers_count
FROM Followers
GROUP BY 1
ORDER BY 1;
```

## [Biggest Single Number](https://leetcode.com/problems/biggest-single-number/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT MAX(num) AS num
FROM (SELECT num
    FROM MyNumbers
    GROUP BY 1
    HAVING COUNT(1) = 1
    ORDER BY 1 DESC);
```

## [Customers Who Bought All Products](https://leetcode.com/problems/customers-who-bought-all-products/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT c.customer_id
FROM Customer c
GROUP BY 1
HAVING COUNT(DISTINCT c.product_key) = (SELECT COUNT(DISTINCT p.product_key) FROM Product p);
```

## [The Number of Employees Which Report to Each Employee](https://leetcode.com/problems/the-number-of-employees-which-report-to-each-employee/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT e.employee_id, e.name, a.reports_count, a.average_age
FROM Employees e
JOIN (SELECT reports_to, COUNT(1) AS reports_count, ROUND(AVG(age)) AS average_age
    FROM Employees
    GROUP BY 1
    HAVING COUNT(1) > 0) a
    ON e.employee_id = a.reports_to
ORDER BY 1;
```

## [Primary Department for Each Employee](https://leetcode.com/problems/primary-department-for-each-employee/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT e.employee_id, e.department_id
FROM Employee e
JOIN (SELECT employee_id, COUNT(1) AS cnt FROM Employee GROUP BY 1) a
    ON e.employee_id = a.employee_id
WHERE e.primary_flag = 'Y'
    OR a.cnt = 1;
```

## [Triangle Judgement](https://leetcode.com/problems/triangle-judgement/submissions/1170839974/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT *,
    CASE WHEN x+y>z AND x+z>y AND y+z>x THEN 'Yes' ELSE 'No' END AS triangle
FROM Triangle;
```

## [Consecutive Numbers](https://leetcode.com/problems/consecutive-numbers/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT DISTINCT a.num AS ConsecutiveNums
FROM (SELECT *,
    LEAD(num,1) OVER() num1,
    LEAD(num,2) OVER() num2
    FROM logs) a
WHERE num = num1
    AND num = num2;
```

## [Product Price at a Given Date](https://leetcode.com/problems/product-price-at-a-given-date/description/?envType=study-plan-v2&envId=top-sql-50)
```
WITH p1 AS (
    SELECT product_id, MAX(change_date) AS date
    FROM Products
    WHERE change_date <= '2019-08-16'
    GROUP BY 1
),
p2 AS (
    SELECT p.product_id, p.new_price
    FROM Products p
    INNER JOIN p1
        ON p.product_id = p1.product_id
        AND p.change_date = p1.date
)
SELECT p3.product_id, COALESCE(MAX(p2.new_price),10) AS price
FROM Products p3
LEFT JOIN p2
    ON p3.product_id = p2.product_id
GROUP BY 1;
```

## [Last Person to Fit in the Bus](https://leetcode.com/problems/last-person-to-fit-in-the-bus/description/?envType=study-plan-v2&envId=top-sql-50)
```
WITH a AS (
    SELECT person_name,
        SUM(weight) OVER (ORDER BY turn ASC) AS c_weight
    FROM Queue
    ORDER BY turn DESC
)
SELECT person_name
FROM a
WHERE c_weight <= 1000
LIMIT 1;
```

## [Count Salary Categories](https://leetcode.com/problems/count-salary-categories/description/?envType=study-plan-v2&envId=top-sql-50)
```
WITH a AS (
    SELECT CASE
        WHEN income < 20000 THEN 'Low Salary'
        WHEN income <= 50000 THEN 'Average Salary'
        ELSE 'High Salary' END AS category,
        COALESCE(COUNT(1),0) AS accounts_count
    FROM Accounts
    GROUP BY 1
),
b as (
    SELECT 'Low Salary' AS category
    UNION ALL
    SELECT 'Average Salary'
    UNION ALL
    SELECT 'High Salary'
)
SELECT b.category, COALESCE(a.accounts_count,0) AS accounts_count
FROM b
LEFT JOIN a
    ON a.category = b.category;
```

OR

```
SELECT 'Low Salary' AS category, COALESCE(COUNT(1),0) AS accounts_count
FROM Accounts
WHERE income < 20000
UNION ALL
SELECT 'Average Salary' AS category, COALESCE(COUNT(1),0) AS accounts_count
FROM Accounts
WHERE income BETWEEN 20000 AND 50000
UNION ALL
SELECT 'High Salary' AS category, COALESCE(COUNT(1),0) AS accounts_count
FROM Accounts
WHERE income > 50000;
```

## [Employees Whose Manager Left the Company](https://leetcode.com/problems/employees-whose-manager-left-the-company/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT employee_id
FROM Employees
WHERE manager_id NOT IN (SELECT employee_id FROM Employees)
    AND salary < 30000
ORDER BY 1;
```

## [Exchange Seats](https://leetcode.com/problems/exchange-seats/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT a.id,
    CASE WHEN (SELECT COUNT(1) FROM Seat) % 2 <> 0 AND a.id + 1 NOT IN (SELECT b.id FROM Seat b) THEN student
    WHEN a.id % 2 = 0 THEN (SELECT b.student FROM Seat b WHERE a.id = b.id + 1)
    ELSE (SELECT b.student FROM Seat b WHERE a.id = b.id - 1) END AS student
FROM Seat a
ORDER BY 1;
```

OR

```
SELECT
    CASE WHEN id = (SELECT MAX(id) FROM Seat) AND id % 2 <> 0 THEN id
    WHEN id % 2 = 0 THEN id - 1
    ELSE id + 1 END AS id,
    student    
FROM Seat a
ORDER BY 1;
```

## [Movie Rating](https://leetcode.com/problems/movie-rating/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT name AS results
FROM (SELECT u.name 
    FROM Users u
    JOIN MovieRating r
        ON u.user_id = r.user_id
    GROUP BY 1
    ORDER BY COUNT(1) DESC, 1 ASC
    LIMIT 1)
UNION ALL
SELECT title AS results
FROM (SELECT m.title
    FROM Movies m
    JOIN MovieRating r
        ON m.movie_id = r.movie_id
    WHERE EXTRACT(YEAR FROM r.created_at) = 2020 AND EXTRACT(MONTH FROM r.created_at) = 2
    GROUP BY 1
    ORDER BY AVG(r.rating) DESC, 1 ASC
    LIMIT 1);
```

## [Restaurant Growth](https://leetcode.com/problems/restaurant-growth/description/?envType=study-plan-v2&envId=top-sql-50)
```
WITH a AS (
    SELECT visited_on,
        RANK() OVER (ORDER BY visited_on) AS rank,
        SUM(amount) AS amount
    FROM Customer
    GROUP BY 1
    ORDER BY 1
),
b AS (
SELECT visited_on,
    SUM(amount) OVER (ORDER BY visited_on ROWS 6 PRECEDING) AS amount,
    AVG(amount) OVER (ORDER BY visited_on ROWS 6 PRECEDING) AS average_amount,
    rank
FROM a
)
SELECT visited_on, amount, ROUND(average_amount,2) AS average_amount
FROM b
WHERE rank >= 7;
```

## [Friend Requests II: Who Has the Most Friends](https://leetcode.com/problems/friend-requests-ii-who-has-the-most-friends/?envType=study-plan-v2&envId=top-sql-50)
```
WITH a AS (
    SELECT requester_id AS id, COUNT(1) as num
    FROM RequestAccepted
    GROUP BY 1
    UNION ALL 
    SELECT accepter_id AS id, COUNT(1) as num
    FROM RequestAccepted
    GROUP BY 1
)
SELECT id, SUM(num) AS num
FROM a
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```

## [Investments in 2016](https://leetcode.com/problems/investments-in-2016/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT ROUND(SUM(tiv_2016)::NUMERIC,2) AS tiv_2016
FROM Insurance
WHERE tiv_2015 IN
    (SELECT tiv_2015
    FROM Insurance
    GROUP BY 1
    HAVING COUNT(1) > 1)
AND (lat, lon) IN
    (SELECT lat, lon
    FROM Insurance
    GROUP BY 1,2
    HAVING COUNT(1) = 1);
```

## [Department Top Three Salaries](https://leetcode.com/problems/department-top-three-salaries/?envType=study-plan-v2&envId=top-sql-50)
```
WITH a AS (
    SELECT d.name AS Department, e.name AS Employee, e.salary AS salary,
        DENSE_RANK() OVER (PARTITION BY d.name ORDER BY salary DESC) AS rank
    FROM Employee e
    JOIN Department d
        ON d.id = e.departmentId
)
SELECT Department, Employee, Salary
FROM a
WHERE rank <= 3;
```

## [Fix Names in a Table](https://leetcode.com/problems/fix-names-in-a-table/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT user_id, CONCAT(UPPER(LEFT(name,1)),LOWER(RIGHT(name,LENGTH(name)-1))) AS name
FROM Users
ORDER BY 1;
```

## [Patients With a Condition](https://leetcode.com/problems/patients-with-a-condition/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT *
FROM Patients
WHERE conditions LIKE 'DIAB1%'
    OR conditions LIKE '% DIAB1%';
```

## [Delete Duplicate Emails](https://leetcode.com/problems/delete-duplicate-emails/?envType=study-plan-v2&envId=top-sql-50)
```
DELETE FROM Person a USING (
    SELECT MIN(id) AS id, email
    FROM Person
    GROUP BY 2
    HAVING COUNT(1) > 1
) b
WHERE a.email = b.email
	AND a.id <> b.id;
```

OR

```
DELETE FROM Person p1 USING Person p2
WHERE p1.Email = p2.Email
    AND p1.id > p2.id;
```

## [Second Highest Salary](https://leetcode.com/problems/second-highest-salary/?envType=study-plan-v2&envId=top-sql-50)
```
WITH a AS (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rank
    FROM Employee
)
SELECT 
    CASE WHEN (SELECT COUNT(1) FROM a) < 2 THEN null
    ELSE (SELECT DISTINCT salary FROM a WHERE rank = 2)
    END AS SecondHighestSalary;
```

## [Group Sold Products By The Date](https://leetcode.com/problems/group-sold-products-by-the-date/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT sell_date, COUNT(DISTINCT product) AS num_sold,
    string_agg(DISTINCT product, ',' ORDER BY product ASC) AS products
FROM Activities
GROUP BY 1
ORDER BY 1;
```

## [List the Products Ordered in a Period](https://leetcode.com/problems/list-the-products-ordered-in-a-period/description/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT p.product_name, SUM(o.unit) AS unit
FROM Products p
JOIN Orders o
    ON p.product_id = o.product_id
WHERE EXTRACT(YEAR FROM o.order_date) = 2020
    AND EXTRACT(MONTH FROM o.order_date) = 2
GROUP BY 1
HAVING SUM(o.unit) >= 100;
```

## [Find Users With Valid E-Mails](https://leetcode.com/problems/find-users-with-valid-e-mails/?envType=study-plan-v2&envId=top-sql-50)
```
SELECT *
FROM Users
WHERE mail ~ '^[A-Za-z][A-Za-z_0-9\-\.\_]*@leetcode\.com$';
```

That's the end of LeetCode - SQL 50 :) <br>
Check out my other exercises!