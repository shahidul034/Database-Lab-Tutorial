<!-- markdownlint-disable MD013 -->

# Oracle Database Lab Tutorial

A hands-on introduction to Oracle SQL and PL/SQL. The ten labs progress from
database setup and schema design to queries, joins, stored programs, and
triggers.

> [!NOTE]
> The examples use SQL*Plus syntax and Oracle data types. Run the labs in order,
> because later exercises reuse the tables and sample data created earlier.

## Contents

| Lab | Topic                                                                                            |
| --: | ------------------------------------------------------------------------------------------------ |
|   1 | [Installation, user creation, and system privileges](#lab-1-installation-and-setup)              |
|   2 | [Data Definition Language (DDL)](#lab-2-data-definition-language-ddl)                            |
|   3 | [Data Manipulation Language (DML)](#lab-3-data-manipulation-language-dml)                        |
|   4 | [Aggregate functions, grouping, subqueries, and sets](#lab-4-aggregate-functions-and-subqueries) |
|   5 | [String operations](#lab-5-string-operations)                                                    |
|   6 | [Joins, views, and referential integrity](#lab-6-joins-views-and-referential-integrity)          |
|   7 | [PL/SQL declarations, `%ROWTYPE`, and cursors](#lab-7-plsql-basics-and-cursors)                  |
|   8 | [PL/SQL loops, arrays, and conditions](#lab-8-plsql-loops-arrays-and-conditions)                 |
|   9 | [Procedures and functions](#lab-9-procedures-and-functions)                                      |
|  10 | [Triggers](#lab-10-triggers)                                                                     |

## Lab 1: Installation and setup

### Install Oracle Database

Download and install [Oracle Database 21c Express Edition](https://www.oracle.com/database/technologies/xe-downloads.html)
or [Oracle Database 11g Express Edition](https://www.oracle.com/database/technologies/xe-prior-release-downloads.html).
Keep the administrative password you choose during installation; you will need
it to connect to the database.

![alt text](https://github.com/shahidul034/BookList_database-project/blob/master/DIAGRAM%20PIC/installation.png)

Open SQL*Plus, enter `CONNECT SYSTEM`, and provide the administrative password
when prompted.

![alt text](https://github.com/shahidul034/Database-Lab-Tutorial/blob/main/DIAGRAM%20PIC/sqlplus.png)

Use the `SYSTEM` account only for administration. Create a separate account for
the lab exercises.

To change the `SYSTEM` password, connect with administrative privileges:

```sql
CONNECT / AS SYSDBA
ALTER USER system IDENTIFIED BY new_password;
```

### Create a user and grant privileges

```sql
CREATE USER shakib034 IDENTIFIED BY a_strong_password;
```

Grant only the privileges required by these labs:

```sql
GRANT CREATE SESSION, CREATE TABLE, CREATE VIEW,
      CREATE PROCEDURE, CREATE TRIGGER
TO shakib034;

ALTER USER shakib034 QUOTA UNLIMITED ON users;
```

> [!CAUTION]
> `GRANT ALL PRIVILEGES` gives an account broad permissions and is unsuitable
> for normal application or production users.

To revoke a privilege or remove a lab account:

```sql
REVOKE CREATE TABLE FROM shakib034;
DROP USER shakib034 CASCADE;
```

![alt text](https://github.com/shahidul034/Database-Lab-Tutorial/blob/main/DIAGRAM%20PIC/sqlplus2.png)
You can also group privileges in a role:

```sql
CREATE ROLE cse2k15;
GRANT CREATE TABLE, CREATE SESSION TO cse2k15;
GRANT cse2k15 TO shakib034;
```

![alt text](https://github.com/shahidul034/Database-Lab-Tutorial/blob/main/DIAGRAM%20PIC/system%20privileges.png)

### Configure SQL*Plus output

```sql
SHOW PAGESIZE
SHOW LINESIZE

SET PAGESIZE 100
SET LINESIZE 200
```

### Database project demo

![Sample database schema](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/pic1.png)

![Sample entity-relationship diagram](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/P4.png)

### Run a SQL script

Save SQL statements in a file with a `.sql` extension, then run it from
SQL*Plus:

```sql
START C:\Users\andromeda\Desktop\file.sql
```

### Oracle data types

- `NUMBER(precision, scale)`: `precision` is the total number of significant
  digits; `scale` is the number of digits to the right of the decimal point.
- `CHAR(n)`: fixed-length character data.
- `VARCHAR2(n)`: variable-length character data.
- `DATE`: date and time to the nearest second. Display format depends on the
  session's `NLS_DATE_FORMAT`.

```sql
CREATE TABLE test (
    name  VARCHAR2(30),
    code  CHAR(3),
    roll  NUMBER(3),
    gpa   NUMBER(3, 2),
    s_dob DATE
);
```

See the [Oracle data type reference](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Data-Types.html)
for complete details.

### Inspect the current schema

```sql
SHOW USER
SELECT table_name FROM user_tables;
```

### Lab 1 exercise

1. Create a user named `lab_student` with a secure password.
2. Grant the user permission to create a session and tables.
3. Connect as `lab_student`, then display the current user and existing tables.

## Lab 2: Data Definition Language (DDL)

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/show_table_name.png)

The query from Lab 1 lists existing tables in the current schema. To start with
a clean schema, remove any old copies of the tutorial tables:

```sql
DROP TABLE department;
DROP TABLE course;
```

Create the `dept` table. Its primary key, `dept_id`, uniquely identifies each
row.

```sql
CREATE TABLE dept (
    dept_id       NUMBER(20),
    dept_name     VARCHAR2(30),
    faculty       VARCHAR2(30),
    no_of_student NUMBER(20),
    PRIMARY KEY (dept_id)
);
```

Inspect the table definition:

```sql
DESCRIBE dept
```

Create the `course`, `book`, and junction (`relation`) tables:

```sql
CREATE TABLE course (
    course_no    VARCHAR2(20),
    course_name  VARCHAR2(50),
    year_semester NUMBER(3),
    credit       NUMBER(20, 4),
    dept_id      NUMBER(20),
    PRIMARY KEY (course_no),
    FOREIGN KEY (dept_id) REFERENCES dept (dept_id)
);

CREATE TABLE book (
    book_no         NUMBER(20),
    book_name       VARCHAR2(50),
    author          VARCHAR2(50),
    book_edition    NUMBER(4),
    course_offering NUMBER(6),
    PRIMARY KEY (book_no)
);

CREATE TABLE relation (
    book_no   NUMBER(20),
    course_no VARCHAR2(20),
    PRIMARY KEY (book_no, course_no),
    FOREIGN KEY (book_no) REFERENCES book (book_no),
    FOREIGN KEY (course_no) REFERENCES course (course_no)
);
```

The foreign key on `course.dept_id` requires every referenced department to
exist in `dept`.

### Add a column

Syntax: `ALTER TABLE table_name ADD column_name column_definition;`

```sql
ALTER TABLE dept ADD location CHAR(20);
```

### Modify a column

Change `location` from `CHAR(20)` to `VARCHAR2(23)`:

```sql
ALTER TABLE dept MODIFY location VARCHAR2(23);
```

### Rename a column

```sql
ALTER TABLE dept RENAME COLUMN location TO location2;
```

### Drop a column

```sql
ALTER TABLE dept DROP COLUMN location2;
```

### Lab 2 exercise

Create a `student` table with `student_id`, `student_name`, `email`, and
`dept_id` columns. Make `student_id` the primary key, make `email` unique, and
reference `dept.dept_id` with a foreign key. Then add and remove a `phone`
column.

## Lab 3: Data Manipulation Language (DML)

### Insert data

```sql
INSERT INTO dept (dept_id, dept_name, faculty, no_of_student)
VALUES (7, 'CSE', 'EE', 120);
```

The column list determines which value is inserted into each column. Add the
remaining sample data:

```sql
INSERT INTO dept (dept_id, dept_name, faculty, no_of_student)
VALUES (3, 'EEE', 'EE', 120);
INSERT INTO dept (dept_id, dept_name, faculty, no_of_student)
VALUES (1, 'CE', 'CE', 120);
INSERT INTO dept (dept_id, dept_name, faculty, no_of_student)
VALUES (5, 'ME', 'ME', 120);
INSERT INTO dept (dept_id, dept_name, faculty, no_of_student)
VALUES (2, 'ECE', 'EE', 60);

INSERT INTO course (
    course_no, course_name, year_semester, credit, dept_id
) VALUES ('CSE1101', 'discrete math', 11, 3.00, 7);
INSERT INTO course (
    course_no, course_name, year_semester, credit, dept_id
) VALUES ('CSE3105', 'database systems', 31, 3.00, 7);
INSERT INTO course (
    course_no, course_name, year_semester, credit, dept_id
) VALUES ('EEE1101', 'Basic electrical engineering', 11, 3.00, 3);
INSERT INTO course (
    course_no, course_name, year_semester, credit, dept_id
) VALUES ('ME3101', 'solid mechanics', 31, 3.00, 5);

INSERT INTO book (
    book_no, book_name, author, book_edition, course_offering
) VALUES (12, 'discrete math', 'rosen', 4, 2);
INSERT INTO book (
    book_no, book_name, author, book_edition, course_offering
) VALUES (13, 'database systems', 'korth', 5, 1);
INSERT INTO book (
    book_no, book_name, author, book_edition, course_offering
) VALUES (14, 'data communication', 'william stallings', 6, 3);
INSERT INTO book (
    book_no, book_name, author, book_edition, course_offering
) VALUES (15, 'solid mechanics', 'john abraham', 3, 2);
INSERT INTO book (
    book_no, book_name, author, book_edition, course_offering
) VALUES (16, 'electrical engineering', 'boylestad', 8, 4);

INSERT INTO relation (book_no, course_no) VALUES (12, 'CSE1101');
INSERT INTO relation (book_no, course_no) VALUES (16, 'EEE1101');
INSERT INTO relation (book_no, course_no) VALUES (15, 'ME3101');
INSERT INTO relation (book_no, course_no) VALUES (13, 'CSE3105');

```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/dept_table.png)
![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/course_table.png)
![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/book_table.png)
![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/relation_table.png)
After inserting the sample data, try to insert a course that references a
department that does not exist:

```sql
INSERT INTO course (
    course_no, course_name, year_semester, credit, dept_id
) VALUES ('MME1101', 'Basic structure of materials', 11, 3.00, 9);
```

Oracle rejects this row because department `9` does not exist. This
demonstrates how a foreign key protects referential integrity.

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/error.png)

### Query data with `SELECT`

Find departments with 120 students:

```sql
SELECT * FROM dept WHERE no_of_student = 120;
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/select.png)

Find the department that offers the `database systems` course:

```sql
SELECT *
FROM dept
WHERE dept_id = (
    SELECT dept_id
    FROM course
    WHERE course_name = 'database systems'
);
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/subquery.png)

The inner query returns a department ID, and the outer query retrieves the
matching department. A query nested inside another query is called a subquery.

### Update data

```sql
UPDATE course
SET course_name = 'Digital Electronics'
WHERE course_no = 'EEE1101';
```

### Delete data

Add a temporary row, then delete it:

```sql
INSERT INTO dept (dept_id, dept_name, faculty, no_of_student)
VALUES (12, 'URP', 'CE', 60);
```

```sql
DELETE FROM dept WHERE dept_id = 12;
```

### Set operators

Oracle supports `UNION`, `UNION ALL`, `INTERSECT`, and `MINUS` (rather than
`EXCEPT`).

```sql
SELECT dept_name FROM dept WHERE dept_name LIKE 'E%'
UNION
SELECT dept_name FROM dept WHERE dept_name LIKE '%M%';
```

### `WITH` clause

This common table expression calculates the largest department size and returns
every department with that size:

```sql
WITH max_student (val) AS (
    SELECT MAX(no_of_student) FROM dept
)
SELECT dept.*
FROM dept
CROSS JOIN max_student
WHERE dept.no_of_student = max_student.val;
```

### Save query output

Change the path and query as needed.

#### CSV

```sql
SET COLSEP ","
SET HEADING OFF
SET PAGESIZE 0
SET FEEDBACK OFF
SPOOL C:\Users\andromeda\Desktop\file.csv
SELECT *
FROM dept;
SPOOL OFF
```

#### Plain text

```sql
SPOOL C:\Users\andromeda\Desktop\file.txt
SELECT *
FROM dept;
SPOOL OFF
```

### Lab 3 exercise

1. Insert two rows into the `student` table from Lab 2.
2. Update one student's email address.
3. Delete one student and query the table to verify the result.

## Lab 4: Aggregate functions and subqueries

### Aggregate functions

Count the rows in `dept`:

```sql
select count(*) from dept;
```

Use an alias to name an output column:

```sql
select count(dept_name) as number_of_dept from dept;
```

Count distinct department names:

```sql
select count(distinct dept_name) as number_of_dept from dept;
```

Calculate the average and total number of students:

```sql
select avg(no_of_student) from dept;
select sum(no_of_student) from dept;
```

Find the largest and smallest department:

```sql
select max(no_of_student) from dept;
select min(no_of_student) from dept;
```

### `GROUP BY` and `HAVING`

Find the average number of students for each faculty:

```sql
select faculty,avg(no_of_student) from dept group by faculty;
```

![alt text](https://github.com/shahidul034/BookList_database-project/blob/master/DIAGRAM%20PIC/group%20by.png)

Filter the grouped results to averages greater than 60:

```sql
SELECT faculty, AVG(no_of_student)
FROM dept
GROUP BY faculty
HAVING AVG(no_of_student) > 60;
```

![alt text](https://github.com/shahidul034/BookList_database-project/blob/master/DIAGRAM%20PIC/having.png)

### Nested subquery

Find the department in which the book by Rosen is taught:

```sql
SELECT dept_name
FROM dept
WHERE dept_id = (
    SELECT dept_id
    FROM course
    WHERE course_no = (
        SELECT course_no
        FROM relation
        WHERE book_no = (
            SELECT book_no FROM book WHERE author = 'rosen'
        )
    )
);
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/subquery2.png)

### Set membership and Boolean operators

Find rows whose faculty is `EE` and whose related course number contains `CSE`:

```sql
SELECT *
FROM dept
WHERE faculty = 'EE'
  AND dept_id IN (
      SELECT dept_id FROM course WHERE course_no LIKE '%CSE%'
  );
```

### `SOME`, `ALL`, and `EXISTS`

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/some.png)
![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/all.png)

```sql
SELECT *
FROM book
WHERE book_no > SOME (
    SELECT book_no FROM book WHERE book_no >= 14
);

SELECT *
FROM book
WHERE book_no > ALL (
    SELECT book_no FROM book WHERE book_no >= 14
);
```

`EXISTS` evaluates to true when its subquery returns at least one row:

```sql
SELECT *
FROM course c
WHERE c.year_semester >= 11
  AND EXISTS (
      SELECT 1
      FROM dept d
      WHERE d.dept_id = c.dept_id
        AND d.faculty = 'EE'
  );
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/exist.png)

### Lab 4 exercise

Write queries that:

1. Count the courses offered by each department.
2. Show only departments offering more than one course.
3. Find the department with the largest number of students using a subquery.

## Lab 5: String operations

The `LIKE` operator uses two wildcard characters:

- `%` matches any sequence of zero or more characters.
- `_` matches exactly one character.

The following examples use this table:

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/table_test.png)

`E%` matches any string that begins with `E`:

```sql
SELECT * FROM test WHERE name LIKE 'E%';
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/begin.png)

`%E` matches any string that ends with `E`:

```sql
SELECT * FROM test WHERE name LIKE '%E';
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/end.png)

`%E%E%` matches a string containing at least two `E` characters in that order:

```sql
SELECT * FROM test WHERE name LIKE '%E%E%';
```

`___` matches any string of exactly three characters:

```sql
SELECT * FROM test WHERE name LIKE '___';
```

The following condition matches strings from three to five characters long:

```sql
SELECT *
FROM test
WHERE name LIKE '___'
   OR name LIKE '____'
   OR name LIKE '_____';
```

### Lab 5 exercise

Using the `test` table, find names that:

1. Begin with `S`.
2. End with `a`.
3. Contain exactly five characters.

## Lab 6: Joins, views, and referential integrity

### Join operations

```sql
SELECT *
FROM dept
NATURAL JOIN course
WHERE dept_id = 7;
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/natural_join_with_conditon.png)

```sql
SELECT * FROM dept NATURAL JOIN course;
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/natural_join.png)

```sql
SELECT dept_name, course_name
FROM dept
JOIN course USING (dept_id);

SELECT dept_name, course_name
FROM dept
JOIN course ON dept.dept_id = course.dept_id;
```

![alt text](https://github.com/shahidul034/database2k19/blob/main/DIAGRAM%20PIC/join.png)

```sql
SELECT dept_name, course_name
FROM dept LEFT OUTER JOIN course USING (dept_id);

SELECT dept_name, course_name
FROM dept RIGHT OUTER JOIN course USING (dept_id);

SELECT dept_name, course_name
FROM dept FULL OUTER JOIN course USING (dept_id);

SELECT dept_name, course_name
FROM dept LEFT OUTER JOIN course ON dept.dept_id = course.dept_id;
```

### Views

A view stores a query definition, not a separate copy of its result. Create a
view containing only department IDs and names:

```sql
CREATE VIEW dept_details AS
SELECT dept_id, dept_name FROM dept;
```

Create a view of courses offered by the CSE department:

```sql
CREATE VIEW cse_dept_course AS
SELECT course_name
FROM course
WHERE dept_id = (
    SELECT dept_id FROM dept WHERE dept_name = 'CSE'
);
```

Views can also reference other views:

```sql
CREATE VIEW custom AS
SELECT * FROM dept_details WHERE dept_id >= 3;
```

### Cascading referential actions

`ON DELETE CASCADE` automatically deletes child rows when their parent row is
deleted:

```sql
CREATE TABLE dept2 (
    dept_id       NUMBER(20),
    dept_name     VARCHAR2(30),
    faculty       VARCHAR2(30),
    no_of_student NUMBER(20),
    PRIMARY KEY (dept_id)
);

CREATE TABLE course2 (
    course_no     VARCHAR2(20),
    course_name   VARCHAR2(50),
    year_semester NUMBER(3),
    credit        NUMBER(20, 4),
    dept_id       NUMBER(20),
    PRIMARY KEY (course_no),
    FOREIGN KEY (dept_id) REFERENCES dept2 (dept_id) ON DELETE CASCADE
);
insert into dept2(dept_id,dept_name,faculty,no_of_student)values(7,'CSE','EE',120);
insert into dept2(dept_id,dept_name,faculty,no_of_student)values(3,'EEE','EE',120);
insert into dept2(dept_id,dept_name,faculty,no_of_student)values(1,'CE','CE',120);
insert into dept2(dept_id,dept_name,faculty,no_of_student)values(5,'ME','ME',120);
insert into dept2(dept_id,dept_name,faculty,no_of_student)values(2,'ECE','EE',60);

insert into course2(course_no,course_name,year_semester,credit,dept_id)values('CSE1101','discrete math',11,3.00,7);
insert into course2(course_no,course_name,year_semester,credit,dept_id)values('CSE3105','database systems',31,3.00,7);
insert into course2(course_no,course_name,year_semester,credit,dept_id)values('EEE1101','Basic electrical engineering',11,3.00,3);
insert into course2(course_no,course_name,year_semester,credit,dept_id)values('ME3101','solid mechanics',31,3.00,5);
```

Deleting department `5` also deletes its related rows from `course2`:

```sql
delete from dept2 where dept_id=5;
```

### Constraints on a single table

```sql
CREATE TABLE my_table (
    id INTEGER PRIMARY KEY,
    name VARCHAR2(50) NOT NULL,
    email VARCHAR2(100) UNIQUE NOT NULL,
    age INTEGER CHECK (age >= 18)
);
CREATE TABLE my_table2 (
    id INTEGER PRIMARY KEY,
    name VARCHAR2(50) NOT NULL,
    email VARCHAR2(100) UNIQUE NOT NULL,
    age INTEGER CHECK (age >= 18 AND age <= 120),
    status VARCHAR2(10) CHECK (status IN ('active', 'inactive', 'pending')),
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    CONSTRAINT check_age_status CHECK (
        (status = 'active' AND age >= 18 AND age <= 65) OR
        (status = 'inactive' AND age >= 18 AND age <= 120) OR
        (status = 'pending' AND age >= 18 AND age <= 100)
    ),
    CONSTRAINT check_date_range CHECK (end_date > start_date)
);

```

Insert valid rows using explicit date conversion:

```sql
INSERT INTO my_table VALUES (1, 'ss', 'ss@gmail.com', 25);
INSERT INTO my_table2
VALUES (
    3,
    'ss',
    'ss@gmail.com',
    25,
    'active',
    DATE '2007-04-03',
    DATE '2009-04-04'
);
```

### Lab 6 exercise

1. Write a join that displays each course with its department name.
2. Create a view named `course_details` from that query.
3. Insert a temporary parent and child row, delete the parent, and observe the
   effect of `ON DELETE CASCADE`.

## Lab 7: PL/SQL basics and cursors

You can run these examples in SQL*Plus, Oracle SQL Developer, or
[Toad for Oracle](https://www.quest.com/products/toad-for-oracle/software-downloads.aspx).

### Declare variables and print values

```sql
SET SERVEROUTPUT ON

DECLARE
    v_dept_id       dept.dept_id%TYPE;
    v_dept_name     dept.dept_name%TYPE;
    v_no_of_student dept.no_of_student%TYPE;
BEGIN
    SELECT dept_id, dept_name, no_of_student
    INTO v_dept_id, v_dept_name, v_no_of_student
    FROM dept
    WHERE dept_id = 7;

    DBMS_OUTPUT.PUT_LINE(
        'Department ID: ' || v_dept_id ||
        '; name: ' || v_dept_name ||
        '; students: ' || v_no_of_student
    );
END;
/
```

### Initialize variables and insert a row

```sql
DECLARE
    v_dept_id       dept.dept_id%TYPE := 9;
    v_dept_name     dept.dept_name%TYPE := 'MME';
    v_faculty       dept.faculty%TYPE := 'ME';
    v_no_of_student dept.no_of_student%TYPE := 30;
BEGIN
    INSERT INTO dept (dept_id, dept_name, faculty, no_of_student)
    VALUES (v_dept_id, v_dept_name, v_faculty, v_no_of_student);
END;
/
```

### `%ROWTYPE`

```sql
DECLARE
    dept_row dept%ROWTYPE;
BEGIN
    SELECT *
    INTO dept_row
    FROM dept
    WHERE dept_id = 7;

    DBMS_OUTPUT.PUT_LINE(dept_row.dept_name);
END;
/
```

### Cursor and row count

```sql
DECLARE
    CURSOR dept_cursor IS SELECT * FROM dept;
    dept_row dept%ROWTYPE;
BEGIN
    OPEN dept_cursor;
    LOOP
        FETCH dept_cursor INTO dept_row;
        EXIT WHEN dept_cursor%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE(
            'Department ID: ' || dept_row.dept_id ||
            '; name: ' || dept_row.dept_name ||
            '; faculty: ' || dept_row.faculty ||
            '; students: ' || dept_row.no_of_student
        );
        DBMS_OUTPUT.PUT_LINE('Row count: ' || dept_cursor%ROWCOUNT);
    END LOOP;
    CLOSE dept_cursor;
END;
/
```

### Lab 7 exercise

Write an anonymous PL/SQL block that uses `%ROWTYPE` to retrieve department
`3`, prints its name and faculty, and uses a cursor to print every course
offered by that department.

## Lab 8: PL/SQL loops, arrays, and conditions

### VARRAY with `EXTEND`

```sql
TYPE NAMEARRAY IS VARRAY(5) OF book.book_name%type;
```

This declares a VARRAY type with a maximum of five elements. Each element uses
the same data type as `book.book_name`.

```sql
A_NAME NAMEARRAY:=NAMEARRAY();
```

The constructor initializes an empty collection. Call `EXTEND` before assigning
each new element:

```sql
set serveroutput on
declare
  counter number;
  book_name2 book.book_name%type;
  TYPE NAMEARRAY IS VARRAY(5) OF book.book_name%type;
  A_NAME NAMEARRAY:=NAMEARRAY();
begin
  counter:=1;
  for x in 12..16
  loop
    select book_name into book_name2 from book where book_no=x;
    A_NAME.EXTEND();
    A_NAME(counter):=book_name2;
    counter:=counter+1;
  end loop;
  counter:=1;
  WHILE counter<=A_NAME.COUNT
    LOOP
    DBMS_OUTPUT.PUT_LINE(A_NAME(counter));
    counter:=counter+1;
  END LOOP;
end;
/
```

### VARRAY without `EXTEND`

Initialize all five elements in the constructor so they can be overwritten
without first calling `EXTEND`:

```sql
DECLARE
   counter NUMBER := 1;
   book_name2 book.book_name%TYPE;
   TYPE NAMEARRAY IS VARRAY(5) OF book.book_name%TYPE;
   A_NAME NAMEARRAY:=NAMEARRAY('Book 1', 'Book 2', 'Book 3', 'Book 4', 'Book 5');
   -- VARRAY with a fixed size of 5 elements and initialized with book names
BEGIN
   counter := 1;
   FOR x IN 12..16
   LOOP
      SELECT book_name INTO book_name2 FROM book WHERE book_no=x;
      A_NAME(counter) := book_name2;
      counter := counter + 1;
   END LOOP;
   counter := 1;
   WHILE counter <= A_NAME.COUNT
   LOOP
      DBMS_OUTPUT.PUT_LINE(A_NAME(counter));
      counter := counter + 1;
   END LOOP;
END;
/
```

### `EXTEND` behavior

`EXTEND` increases a collection's current element count, but a VARRAY can never
exceed the maximum size declared in its type (`5` here). Assigning to an index
greater than `COUNT` raises `ORA-06533: Subscript beyond count`; extending past
the declared maximum raises `ORA-06532: Subscript outside of limit`.

### `IF` / `ELSIF` / `ELSE`

```sql
DECLARE
   counter NUMBER := 1;
   book_name2 book.book_name%TYPE;
   TYPE NAMEARRAY IS VARRAY(5) OF book.book_name%TYPE;
   A_NAME NAMEARRAY:=NAMEARRAY('Book 1', 'Book 2', 'Book 3', 'Book 4', 'Book 5');
   -- VARRAY with a fixed size of 5 elements and initialized with book names
BEGIN
   counter := 1;
   FOR x IN 12..16
   LOOP
      SELECT book_name INTO book_name2 FROM book WHERE book_no=x;
      if book_name2='discrete math'
        then
        dbms_output.put_line(book_name2||' is a '||'CSE course');
      elsif book_name2='electrical engineering'
        then
        dbms_output.put_line(book_name2||' is a '||'EEE course');
      else
        dbms_output.put_line(book_name2||' is a '||'other dept course');
        end if;
   END LOOP;
END;
/
```

### Lab 8 exercise

Create a VARRAY containing three department names. Use a loop and an
`IF`/`ELSIF`/`ELSE` statement to label each department as `Engineering`,
`Science`, or `Other`, then print the result.

## Lab 9: Procedures and functions

### Procedure

This procedure demonstrates `IN`, `OUT`, and `IN OUT` parameters:

```sql
CREATE OR REPLACE PROCEDURE proc2(
    p_book_no     IN NUMBER,
    p_course_name OUT VARCHAR2,
    p_counter     IN OUT NUMBER
)
IS
BEGIN
    SELECT course_name
    INTO p_course_name
    FROM course
    WHERE course_no IN (
        SELECT course_no FROM relation WHERE book_no = p_book_no
    );

    p_counter := p_counter + 1;
    DBMS_OUTPUT.PUT_LINE(
        'From procedure: ' || p_course_name ||
        '; book number: ' || p_book_no ||
        '; counter: ' || p_counter
    );
END;
/
```

Call the procedure from an anonymous block:

```sql
SET SERVEROUTPUT ON

DECLARE
    v_book_no     book.book_no%TYPE := 12;
    v_course_name course.course_name%TYPE;
    v_counter     NUMBER := 0;
BEGIN
    proc2(v_book_no, v_course_name, v_counter);
END;
/
```

### Function

```sql
CREATE OR REPLACE FUNCTION get_dept_name(
    p_dept_id IN dept.dept_id%TYPE
) RETURN dept.dept_name%TYPE
IS
    v_name dept.dept_name%TYPE;
BEGIN
    SELECT dept_name
    INTO v_name
    FROM dept
    WHERE dept_id = p_dept_id;

    RETURN v_name;
END;
/
```

```sql
SET SERVEROUTPUT ON

DECLARE
    v_name dept.dept_name%TYPE;
BEGIN
    v_name := get_dept_name(5);
    DBMS_OUTPUT.PUT_LINE(v_name);
END;
/
```

A function returns a value through its `RETURN` clause. A procedure has no
return value, but it can send values to its caller through `OUT` and `IN OUT`
parameters.

### Drop a procedure or function

```sql
drop procedure proc2;
drop function get_dept_name;
```

### Lab 9 exercise

1. Create a procedure that accepts a department ID and prints its name.
2. Create a function that accepts a department ID and returns its student
   count.
3. Call both programs from an anonymous PL/SQL block.

## Lab 10: Triggers

A trigger runs automatically in response to a database event. A row-level DML
trigger follows this general form:

```sql
CREATE OR REPLACE TRIGGER trigger_name
BEFORE | AFTER INSERT OR UPDATE OR DELETE
ON table_name
FOR EACH ROW
BEGIN
    -- Trigger statements
END;
/
```

Inside a row-level trigger, `:OLD` contains values before an update or deletion,
and `:NEW` contains values after an insertion or update.

### Log deleted relationships

First create an audit table, then capture deleted junction rows:

```sql
CREATE TABLE relation_audit (
    book_no    NUMBER(20),
    course_no  VARCHAR2(20),
    deleted_at TIMESTAMP,
    deleted_by VARCHAR2(128)
);

CREATE OR REPLACE TRIGGER relation_delete_audit
AFTER DELETE ON relation
FOR EACH ROW
BEGIN
    INSERT INTO relation_audit (
        book_no, course_no, deleted_at, deleted_by
    )
    VALUES (
        :OLD.book_no, :OLD.course_no, SYSTIMESTAMP, USER
    );
END;
/
```

```sql
DELETE FROM relation WHERE book_no = 16;
```

### Synchronize a related value after an update

This trigger updates related book names when a course name changes:

```sql
CREATE OR REPLACE TRIGGER trigger2
AFTER UPDATE OF course_name ON course
FOR EACH ROW
BEGIN
    UPDATE book
    SET book_name = :NEW.course_name
    WHERE book_no IN (
        SELECT book_no
        FROM relation
        WHERE course_no = :OLD.course_no
    );
END;
/
```

```sql
UPDATE course
SET course_name = 'discrete math 2'
WHERE course_no = 'CSE1101';
```

### Update a counter after an insert

This trigger increments `course_offering` when a relationship is inserted:

```sql
CREATE OR REPLACE TRIGGER trigger_new
AFTER INSERT ON relation
FOR EACH ROW
BEGIN
    UPDATE book
    SET course_offering = course_offering + 1
    WHERE book_no = :NEW.book_no;
END;
/
```

Use these commands to inspect compilation errors, list triggers, or remove a
trigger:

```sql
SHOW ERRORS
SELECT * FROM user_triggers;
DROP TRIGGER trigger_new;
```

### Lab 10 exercise

Create an audit table and an `AFTER UPDATE` trigger that records a course's old
name, new name, update time, and the user who made the change. Update one course
and query the audit table to verify the trigger.

## Reference

Silberschatz, A., Korth, H. F., & Sudarshan, S. (2023). _Database System
Concepts_ (7th ed.).
