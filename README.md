## Installation process:
1) Download the ([Oracle 11g Express edition](https://www.oracle.com/database/technologies/xe-prior-release-downloads.html)) and install the software.
2) Remember the password during the installation because this password is used for connecting the database account.

![alt text](https://github.com/shahidul034/BookList_database-project/blob/master/DIAGRAM%20PIC/installation.png)

3) Open the SQL Plus. Write 'connect system' and use the password that you set in the installation process. Follow the below figure.

![alt text](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/sqlplus.png)

4) You can create a new user because we use the system(administrator) as a user. Then, we will give the new user all privileges to perform all SQL tasks. Follow the figure.
```
create user shakib034 identified by shakib034;
grant all privileges to shakib034;
```
![alt text](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/sqlplus2.png)
# database-project
![alt text](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/pic1.png)
![alt text](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/p3.png)
![alt text](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/P4.png)
![alt text](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/p2.png)
## First, we check the existing table in our database.
```
select table_name from user_tables;
```
![alt text](https://github.com/shahidul034/database-project/blob/master/DIAGRAM%20PIC/show_table_name.png)
It shows that we have already two tables in my database. We need to drop those database for fresh start. You can use this command for dropping any database.
```
drop table department;
drop table course;
```
## First, we create the table "dept". Here, we can see primary key is dept_id. Because, it can uniquely identify the each in the table.
```
Create table dept(
dept_id number(20),
dept_name varchar(30),
faculty varchar(30),
no_of_student number(20),
primary key(dept_id)
);
```
