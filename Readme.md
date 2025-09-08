# Postgres-Data-Handling-and-Aggregation-Essentials

Practice Task: https://spangle-heron-ba0.notion.site/Practice-SQL-Queries-264963b8606180b6b598f99680a378b8
My Notion : https://www.notion.so/Practice-SQL-Queries-2641351446ad807882fdc9be365df9d9?source=copy_link


In this module, you’ll learn essential data manipulation concepts in PostgreSQL. From handling NULL values with COALESCE to implementing LIMIT and OFFSET for pagination, updating and deleting records, everything is covered. You’ll also explore how to group data with GROUP BY and refine results using HAVING for deeper analysis.

## 46-1 Handling NULL with COALESCE
- If we consider a switch either the switch can be off, on or either the switch do not exist. 

### IS METHOD FOR NULL VALUE 
- There is one saying whatever we do with postgres it will give null 

```sql
select null = null
```
- it will give null 

```sql 
select null <> null 
```
- This is also null 

- Make a query that will give us the students whose email is not null... 


```sql
select * from students where email = null 
```

```sql 
select * from students where email <> null 
```
- this is wrong this will give nothing because null gives null 

- we have to use `is` method 

```sql 
select * from students where email is null;
```
```sql 
select * from students where email is not null;
```

### COALESCE FOR NULL VALUE 

```SQL 
select coalesce(null,null,2,3)
```
- This does something it skips null and when ever it gets value it will show the value and will not go further. like it will show result 2. where it is needed? 
- when we design a database it goes from backend to frontend. if any null value goes to frontend, application will crash. 

- lets see practical example of `coalesce`

```sql
select coalesce(email, 'Not Provided') as email, * from students;
```
- it will give the email field where the email field it will give us `Not Provided`

## 46-2 LIMIT, OFFSET & Pagination
### Limit 
```sql 
select * from students limit 5;
```
- it will show 5 data by limiting among all. 

```sql
select * from students 
where country IN ('Bangladesh','USA','Spain') limit 1;

```

### OFFSET 

```sql
select * from students  limit 3 offset 2;
```
- It will skip first 2 data and send 


### Lets do the pagination using Limit and Offset 

```sql 
select * from students  limit 3 offset 2 * 0; -- first page
select * from students  limit 3 offset 2 * 1; -- second page 
select * from students  limit 3 offset 2 * 2; -- Third page
select * from students  limit 3 offset 2 * 3; -- fourth page 
```

## 46-3 Updating Data

- suppose 31 no user has null email. lets update the email where the email is null 

```sql 
update students set email = 'default@gmail.com' where email is null
```
- multiple field update of a single row 

```sql 
update students 
  set first_name = 'Dustbin', age = 100
  where student_id = 1;
```

```sql 
update students 
  set grade ='D'
  where student_id = 1 or student_id = 2;
```
- same thing in different method 

```sql 
update students 
  set grade ='F'
  where student_id in (1,2);
```

## 46-4 Deleting Data

```sql 
delete from students; 
```
- this will delete all the data of the table. 


```sql 
delete from students where grade = 'F'; 
```
- who have got F will be removed 

```sql 
delete from students where age > 20 and grade = 'F'; 
```

## 46-5 GROUP BY Explained

```sql
select country from students group by country;
```
![alt text](image.png)

- group by split then combine and then join; 

- if we want to grab avg age 

```sql 
select country, avg(age) from students group by country;
```
- count students by country 

```sql 
select country, count(*) from students group by country;
```

- count students by grade 

```sql 
select grade, count(*) from students group by grade;
```

## 46-6 GROUP BY with HAVING
- Courses with more than 1 students 

```sql 
select course, count(*) from students group by course having count(*) >1;
```
- its like where but not exactly same. where runs over entire table rows but having runs over the grouped rows only. 

- lets find countries where average age opf students is greater than 21 

```sql
select country, avg(age) from students group by country having avg(age) > 21;
```

# Practice Problem 

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  age INT,
  country VARCHAR(50),
  email VARCHAR(100)
);



INSERT INTO users (first_name, last_name, age, country, email) VALUES
('Arif',    'Hossain',   25, 'Bangladesh', 'arif@example.com'),
('Sara',    'Khan',      30, 'India',      'sara@example.com'),
('Rafi',    'Ahmed',     22, 'Pakistan',   'rafi@example.com'),
('Nusrat',  'Akter',     28, 'Nepal',      'nusrat@example.com'),
('Tanvir',  'Rahman',    35, 'Sri Lanka',  'tanvir@example.com'),
('Mina',    'Begum',     20, 'Maldives',   'mina@example.com'),
('Fahim',   'Chowdhury', 40, 'Bhutan',     'fahim@example.com'),
('Rumana',  'Sultana',   27, 'Afghanistan','rumana@example.com'),
('Jahid',   'Islam',     24, 'Bangladesh', 'jahid@example.com'),
('Tania',   'Sultana',   29, 'Nepal',      'tania@example.com');

select * from users;

drop table users; 

alter table hugers rename to users;

alter table users 
  add column address text;

alter table users
  drop column address;

ALTER TABLE users 
ADD COLUMN rating DOUBLE PRECISION;

alter table users 
  rename column rating to user_rating;

ALTER TABLE users 
ALTER COLUMN user_rating TYPE NUMERIC(10,2);

ALTER TABLE users 
ALTER COLUMN user_rating set not null;

ALTER TABLE users 
ALTER COLUMN user_rating drop not null;

ALTER TABLE users 
ALTER COLUMN user_rating set default 2;

ALTER TABLE users 
add constraint unique_employee_email unique(email);

alter table users 
  drop constraint unique_employee_email;

select * from users; 

select first_name, email, age from users
  where age > 25;

select country, count(*) from users 
  group by country;

select min(age) from users;

select max(age) from users;
select avg(age) from users;

select * from users
  order by age ASC;

INSERT INTO users (first_name, last_name, age, country, email) VALUES
('Imran', 'Haque', 26, 'Bangladesh', NULL),
('Lina',  'Begum', 23, 'India', NULL);

select coalesce(email, 'Not Provided') from users;

update users set country = null where id = 12

select coalesce(country, 'unknown') as country, count(*) as user_count 
  from users
group by coalesce(country, 'unknown')


SELECT *
FROM users
ORDER BY id
LIMIT 5;


SELECT *
FROM users
ORDER BY id
LIMIT 5 OFFSET 5;

SELECT *
FROM users
ORDER BY age ASC
LIMIT 3;

```

## Date and Foreign Key Related Practice

```sql 
create database test_db;
show timezone;

select now();

select now()::date;
select now()::time;
select to_char(now(),'yyy/mm/dd');
select to_char(now(),'dd');
select to_char(now(),'mm');

select current_date - interval '1 month';
select current_date - interval '1 day';
select current_date - interval '1 year 2 month';

select age(current_date, '1999-07-02');

select extract(year from '2025-01-25'::date);
select extract(month from '2025-01-25'::date);
select extract(day from '2025-01-25'::date);


SELECT extract(year from dob) as birth_year, count(*)
FROM students
GROUP BY birth_year;

CREATE TABLE post (
    id serial PRIMARY KEY,
    title TEXT NOT NULL,
    user_id INTEGER REFERENCES "user" (id) ON DELETE CASCADE
)

CREATE TABLE post (
    id serial PRIMARY KEY,
    title TEXT NOT NULL,
    user_id INTEGER REFERENCES "user" (id) ON DELETE SET NULL
)

CREATE TABLE post (
    id serial PRIMARY KEY,
    title TEXT NOT NULL,
    user_id INTEGER REFERENCES "user" (id) ON DELETE RESTRICT
)

CREATE TABLE post (
    id serial PRIMARY KEY,
    title TEXT NOT NULL,
    user_id INTEGER REFERENCES "user"(id) ON DELETE SET DEFAULT  DEFAULT 2
)

```

### Join date and group by Practice 

```sql 
CREATE TABLE
  "user" (
    id serial PRIMARY KEY,
    username VARCHAR(25) NOT NULL
  );

CREATE TABLE
  post (
    id serial PRIMARY KEY,
    title TEXT NOT NULL,
    user_id INTEGER REFERENCES "user" (id) NOT Null
  );

-- Insert users
INSERT INTO
  "user" (username)
VALUES
  ('alice'),
  ('bob'),
  ('charlie'),
  ('diana');

-- Insert posts
INSERT INTO
  post (title, user_id)
VALUES
  ('Alice first post', 1),
  ('Bob travel blog', 2),
  ('Charlie on coding', 3),
  ('Alice second post', 1),
  ('Diana book review', 4),
  ('Another tech tip from Bob', 2);

SELECT * from "user";

SELECT * from post;

SELECT title, username FROM post
  JOIN "user" ON post.user_id = "user".id;

ALTER table post
alter column user_id set NOT Null;

ALTER table post
alter column user_id on delete cascade;

alter table post 
drop column user_id;



ALTER TABLE post
ADD COLUMN user_id INTEGER NOT NULL
REFERENCES "user" (id) ON DELETE RESTRICT ON UPDATE CASCADE;


CREATE TABLE post (
    id serial PRIMARY KEY,
    title TEXT NOT NULL,
    user_id INTEGER REFERENCES "user" (id) ON DELETE CASCADE ON UPDATE CASCADE
)

select title, username from post
  join "user" on post.user_id = "user".id;

SELECT * from post
JOIN "user" ON post.user_id = "user".id;

SELECT post.id FROM post JOIN "user" ON post.user_id = "user".id;
SELECT "user".id FROM post JOIN "user" ON post.user_id = "user".id;

select "user".id from post 
  Inner join "user" on post.user_id = "user".id;

SELECT * FROM post
INNER JOIN "user" ON post.user_id = "user".id;

SELECT * FROM "user"
INNER JOIN post ON post.user_id = "user".id;

INSERT INTO post (title, user_id) VALUES ('Alice first post', NULL);

INSERT INTO "user" (username) VALUES ('Sazid');

SELECT * FROM post INNER JOIN "user" ON post.user_id = "user".id;

SELECT * FROM post
LEFT JOIN "user" ON post.user_id = "user".id;

SELECT * FROM post
right JOIN "user" ON post.user_id = "user".id;


SELECT * FROM post
FULL OUTER JOIN "user" on post.user_id = "user".id


CREATE TABLE employees(
    emp_id INT,
    emp_name VARCHAR(50),
    dept_id INT
);

CREATE TABLE departments(
    dept_id INT,
    dept_name VARCHAR(50)
);

DROP table employees

DROP table departments

INSERT INTO employees VALUES(1,'John Doe', 101);
INSERT INTO employees VALUES(2,'Jane Smith', 102);

INSERT INTO departments VALUES( 102,'Marketing');
INSERT INTO departments VALUES( 101,'Human Resources');

SELECT * FROM employees;

SELECT * FROM departments;

SELECT * FROM employees
CROSS JOIN departments;

SELECT * FROM employees
NATURAL JOIN departments;


CREATE TABLE employees(
    employee_id SERIAL PRIMARY KEY,
    employee_name VARCHAR(50),
    department_id INTEGER REFERENCES departments(department_id),
    salary DECIMAL(10,2),
    hire_date DATE
)

CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(50)
);

-- Inserting sample data into the departments table
INSERT INTO departments (department_name) VALUES
    ('HR'),
    ('Marketing'),
    ('Finance'),
    ('IT'),
    ('Sales'),
    ('Engineering'),
    ('Customer Support'),
    ('Administration'),
    ('Research'),
    ('Quality Assurance');

-- Inserting sample data into the employees table with a foreign key constraint
INSERT INTO employees (employee_name, department_id, salary, hire_date) VALUES
    ('John Doe', 1, 60000.00, '2022-01-10'),
    ('Jane Smith', 2, 75000.50, '2021-05-22'),
    ('Bob Johnson', 3, 80000.75, '2020-11-15'),
    ('Alice Williams', 4, 90000.25, '2019-08-03'),
    ('David Lee', 5, 65000.50, '2020-03-18'),
    ('Sara Brown', 6, 70000.00, '2021-09-28'),
    ('Mike Miller', 7, 55000.75, '2022-02-05'),
    ('Emily Davis', 8, 95000.00, '2018-12-12'),
    ('Chris Wilson', 9, 72000.50, '2020-06-30'),
    ('Amy White', 10, 68000.25, '2021-11-09'),
    ('John Johnson', 1, 62000.00, '2022-01-15'),
    ('Jessica Thompson', 2, 78000.50, '2021-06-05'),
    ('Michael Harris', 3, 85000.75, '2020-11-25'),
    ('Emma Martinez', 4, 92000.25, '2019-09-15'),
    ('James Taylor', 5, 67000.50, '2020-04-08'),
    ('Sophia Anderson', 6, 72000.00, '2021-10-10'),
    ('William Jackson', 7, 56000.75, '2022-02-10'),
    ('Olivia Nelson', 8, 97000.00, '2018-12-20'),
    ('Daniel White', 9, 73000.50, '2020-07-05'),
    ('Ava Wilson', 10, 69000.25, '2021-11-15'),
    ('Matthew Brown', 1, 63000.00, '2022-01-20'),
    ('Emily Garcia', 2, 76000.50, '2021-06-15'),
    ('Christopher Allen', 3, 86000.75, '2020-12-05'),
    ('Madison Hall', 4, 93000.25, '2019-09-25'),
    ('Andrew Cook', 5, 68000.50, '2020-04-18'),
    ('Abigail Torres', 6, 73000.00, '2021-10-20'),
    ('Ethan Murphy', 7, 57000.75, '2022-02-15'),
    ('Ella King', 8, 98000.00, '2018-12-28'),
    ('Nathan Rivera', 9, 74000.50, '2020-07-15'),
    ('Mia Roberts', 10, 70000.25, '2021-11-20');

SELECT * FROM employees
  Inner join departments on ;

SELECT * FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id;

SELECT * FROM employees
INNER JOIN departments USING(department_id);

select department_name, round(avg(salary)) as avg_salary from employees
  INNER JOIN departments USING(department_id)
    group by department_name;

SELECT department_name, count(*) as employee_count FROM employees
INNER JOIN departments USING(department_id)
GROUP BY department_name;

SELECT department_name, round(avg(salary)) as avg_salary FROM employees
INNER JOIN departments USING(department_id)
GROUP BY department_name
ORDER BY avg_salary DESC
LIMIT 1;

SELECT extract(year from hire_date) as hired_years, count(*) FROM employees
GROUP BY hired_years;


CREATE TABLE orders(
    order_id SERIAL PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2)
)


INSERT INTO orders (customer_id, order_date, total_amount) VALUES
(101, '2025-05-01', 199.99),
(102, '2025-05-02', 89.50),
(103, '2025-05-03', 145.00),
(104, '2025-05-04', 320.75),
(101, '2025-05-05', 25.99),
(105, '2025-05-06', 470.00),
(102, '2025-05-07', 129.49),
(106, '2025-05-08', 250.00),
(107, '2025-05-09', 78.90),
(108, '2025-05-10', 199.00);

SELECT customer_id, COUNT(*) AS total_orders, SUM(total_amount) AS total_spent
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 1;

SELECT extract(month FROM order_date) as order_months, count(order_date) FROM orders
WHERE EXTRACT(YEAR FROM order_date) = 2025
GROUP BY order_months;


CREATE TABLE departments (
    id SERIAL PRIMARY KEY,
    name VARCHAR(20)
);

-- Create courses table
CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    title VARCHAR(20)
);

-- Create students table
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(20),
    department_id INTEGER REFERENCES departments(id),
    last_login DATE DEFAULT CURRENT_DATE
);
drop table students;
drop table employees;
drop table departments;

INSERT INTO departments (name) VALUES
('CSE'),
('EEE'),
('BBA'),
('Mechanical'),
('Civil'),
('Architecture');

INSERT INTO courses (title) VALUES
('Database Systems'),
('Operating Systems'),
('Digital Logic'),
('Microeconomics'),
('Thermodynamics'),
('Structural Design'),
('Software Engineering'),
('Circuit Analysis'),
('Marketing Basics'),
('Design Principles');


INSERT INTO students (name, department_id, last_login) VALUES
-- May 2025 logins
('Alice', 1, '2025-05-15'),
('Bob', 2, '2025-05-08'),
('David', 3, '2025-05-17'),
('Ian', 6, '2025-05-18'),
('Jane', 1, '2025-05-10'),
('Karl', 3, '2025-05-19'),
('Mike', 5, '2025-05-05'),
('Paul', 2, '2025-05-13'),
('Tina', 6, '2025-05-12'),
('Charlie', 1, '2025-04-15'),
('Frank', 1, '2025-04-22'),
('Hannah', 5, '2025-04-25'),
('Luna', 4, '2025-04-19'),
('Nora', 1, '2025-04-30'),
('Quinn', 3, '2025-04-28'),
('Rose', 4, '2025-04-21'),
('Steve', 5, '2025-04-20'),
('Oscar', 6, '2025-04-05'),
('Grace', 2, '2025-03-21'),
('Yasmin', 3, '2025-03-05'),
('Zane', 4, '2025-03-29'),
('Liam', 2, '2025-03-10');

SELECT * FROM students

SELECT * FROM students
WHERE last_login >= CURRENT_DATE - INTERVAL '30 days';

SELECT * FROM students
WHERE last_login >= DATE '2025-05-01' - INTERVAL '30 days';

SELECT extract(month from last_login) as login_month, count(*) as students FROM students
GROUP BY login_month


SELECT extract(month from last_login) as login_month, count(*) as logged_students FROM students
GROUP BY login_month
HAVING count(*)  > 4


CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(20),
    department_id INTEGER REFERENCES departments(id),
    last_login DATE DEFAULT CURRENT_DATE
);

INSERT INTO students (name, department_id, last_login) VALUES
('Alice', 20, '2025-05-15');


CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(20),
    department_id INTEGER REFERENCES departments(id) ON DELETE CASCADE,
    last_login DATE DEFAULT CURRENT_DATE
);

SELECT * from departments

SELECT * from students

DELETE from departments WHERE name = 'CSE'

CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(20),
    department_id INTEGER REFERENCES departments(id) ON DELETE SET NULL,
    last_login DATE DEFAULT CURRENT_DATE
);

SELECT * from students
INNER JOIN departments ON students.id = departments.id;

SELECT * from students
LEFT JOIN departments ON students.id = departments.id;

SELECT * from students
LEFT JOIN departments ON students.id = departments.id;

SELECT * FROM students
RIGHT JOIN departments ON students.department_id = departments.id;

SELECT * FROM students
RIGHT JOIN departments ON students.department_id = departments.id;

SELECT * FROM students
FULL JOIN departments ON students.department_id = departments.id;


SELECT * FROM students
CROSS JOIN departments;
```