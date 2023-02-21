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
## Create Table
```
drop table relation;
drop table course;
drop table dept;
drop table book;
Create table dept(
dept_id number(20),
dept_name varchar(30),
faculty varchar(30),
no_of_student number(20),
primary key(dept_id)
);
Create table course(
course_no varchar(20),
course_name varchar(50),
year_semister number(3),
credit number(20,4),
dept_id number(20),
primary key(course_no),
foreign key(dept_id) references dept(dept_id)
);
create table book(
book_no number(20),
book_name varchar(50),
author varchar(50),
book_edition number(4),
course_offering number(6),
primary key(book_no) 
);
create table relation(
book_no number(20),
course_no varchar(20),
primary key(book_no,course_no),
foreign key (book_no) references book(book_no),
foreign key (course_no) references course(course_no) 
);

```
## Insert the data in the table
```
insert into dept(dept_id,dept_name,faculty,no_of_student)values(7,'CSE','EE',120);
insert into dept(dept_id,dept_name,faculty,no_of_student)values(3,'EEE','EE',120);
insert into dept(dept_id,dept_name,faculty,no_of_student)values(1,'CE','CE',120);
insert into dept(dept_id,dept_name,faculty,no_of_student)values(5,'ME','ME',120);
insert into dept(dept_id,dept_name,faculty,no_of_student)values(2,'ECE','EE',60);

insert into course(course_no,course_name,year_semister,credit,dept_id)values('CSE1101','discreate math',11,3.00,7);
insert into course(course_no,course_name,year_semister,credit,dept_id)values('CSE3105','database systems',31,3.00,7);
insert into course(course_no,course_name,year_semister,credit,dept_id)values('EEE1101','Basic electrical engineering',11,3.00,3);
insert into course(course_no,course_name,year_semister,credit,dept_id)values('ME3101','solid mechanics',31,3.00,5);



insert into book(book_no,book_name,author,book_edition,course_offering)values(12,'discreate math','rosen',4,2);
insert into book(book_no,book_name,author,book_edition,course_offering)values(13,'database systems','korth',5,1);
insert into book(book_no,book_name,author,book_edition,course_offering)values(14,'data_communication','willim stallings',6,3);
insert into book(book_no,book_name,author,book_edition,course_offering)values(15,'solid mechanics','john abraham',3,2);
insert into book(book_no,book_name,author,book_edition,course_offering)values(16,'electrical engineering','boylsted',8,4);

insert into relation(book_no,course_no)values(12,'CSE1101');
insert into relation(book_no,course_no)values(16,'EEE1101');
insert into relation(book_no,course_no)values(15,'ME3101');
insert into relation(book_no,course_no)values(13,'CSE3105');

```


## SQL Query:
### ------DDL-------
```
--drop table amni;
alter table dept add location char(20);
alter table dept modify location varchar(23);
alter table dept rename column location to location2;
select * from dept; 
select* from tab;
```
### ------DML--------
```
insert into dept(dept_id,dept_name,faculty,no_of_student,location2)values(12,'URP','CE',60,'khulna');
update dept set location2='ctg' where dept_id=12;
delete from  dept where dept_id=12;-- delete the row
alter table dept drop column location2; --DDL--
select * from dept where dept_name like '%E%';
select * from dept order by dept_name;
```
### ---aggregate function---
```
select count(*) from dept; 
select count(dept_name) as number_of_dept from dept;
select count(distinct dept_name) as number_of_dept from dept;
select dept_name,avg(no_of_student) from dept group by dept_name;
select dept_name,avg(no_of_student) from dept group by dept_name having avg(no_of_student)>100;
select sum(no_of_student) from dept;
```
 ### ---- JOIN------
 ```
select * from dept where dept_name in(select dept_name from dept where dept_id=7);
select dept_name,course_name from dept natural join course where dept_id=7;
select dept_name,course_name from dept join course using(dept_id);
select dept_name,course_name from dept left outer join course using(dept_id);
select dept_name,course_name from dept right outer join course using(dept_id);
select dept_name,course_name from dept full outer join course using(dept_id);
```
## PL/SQL:
```
set serveroutput on
declare
new_course_name course.course_name%type;
DEPT_name dept.DEPT_NAME%type;
book_num book.book_name%type;
value dept.dept_name%type;
counter2 number;
TYPE NAMEARRAY IS VARRAY(5) OF course.course_name%type;  --array declare
A_NAME NAMEARRAY:=NAMEARRAY();
counter number;
begin
dbms_output.put_line('DEPT_name: ');
select dept_name into DEPT_name from dept where dept_name='CSE';
dbms_output.put_line(DEPT_name);
counter:=12;
counter2:=1;
dbms_output.put_line('book_name: ');
for counter in 12..16  --- for loop
loop
select book_name into book_num from book where book_no=counter;
A_NAME.EXTEND();
A_NAME(counter2):=book_num;
counter2:=counter2+1;
if book_num='discreate math'  ---if condition
then
dbms_output.put_line(book_num||' is a '||'CSE course');
elsif book_num='electrical engineering'  --else if condition
then
dbms_output.put_line(book_num||' is a '||'EEE course');
else 
dbms_output.put_line(book_num||' is a '||'other dept course');
end if;
dbms_output.put_line(book_num);
end loop;
counter2:=1;
dbms_output.put_line('loop: ');
 WHILE counter2<=A_NAME.COUNT  --while loop
LOOP 
DBMS_OUTPUT.PUT_LINE(A_NAME(counter2)); -- array print
 counter2:=counter2+1;
   END LOOP;
proc1('CSE1101');   
proc2(15,new_course_name); 
proc3(7);
value:=fun(7);
DBMS_OUTPUT.PUT_LINE('function value: ' || value);

 
end;
/
```
## Procedure:
```
set serveroutput on
create or replace procedure proc2(var1 In number,var2 out varchar) AS
t_show char(30);
begin
t_show:='From procedure: ';
select  course_name into var2 from course where course_no in(select course_no from relation  where book_no=var1);  
dbms_output.put_line(t_show || var2 ||' library code is '|| var1);
end;
/
```
## Function:
```
set serveroutput on
create or replace function fun(var1 in varchar) return varchar AS
value dept.dept_name%type;
begin
  select dept_name into value from dept where dept_id=var1; 
   return value;
end;
/
```
## Trigger:
```
SET SERVEROUTPUT ON
CREATE OR REPLACE TRIGGER trigger_new
after insert ON relation 
REFERENCING OLD AS o NEW AS n
FOR EACH ROW
Declare
BEGIN
update book set course_offering=course_offering+1 where book_no=:n.book_no;
END;
/
```
## Cursor:
```
set serveroutput on
declare
d_name dept.dept_name%Type;
cursor cur_dept is
select dept_name into d_name from dept where no_of_student=120;
begin
DBMS_OUTPUT.PUT_LINE('dept Name where no of student 120: '); 
if not cur_dept%isopen
then
open cur_dept;
end if;
loop
fetch cur_dept into d_name;
exit when cur_dept%notfound;
DBMS_OUTPUT.PUT_LINE('dept Name=' || cur_dept%rowcount||':'||d_name);
end loop;
close cur_dept;
end;
/
```
## Command for FILE:
```
SQL*Plus: Release 11.2.0.2.0 Production on Thu May 3 11:12:12 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
SQL> connect system
Enter password:
Connected.
SQL> conn sys/sys as sysdba;
Connected.
SQL> grant execute on UTL_FILE to public;
Grant succeeded.
SQL> create or replace directory CSE as 'C:\Users\ASUS\Desktop\TOAD';
Directory created.
SQL> grant read,write on directory CSE to public;
Grant succeeded.
SQL>

```
## File_read(course):
```
set serveroutput on
declare 
f utl_file.file_type;
line varchar(1000);
course_no course.course_no%type;
course_name  course.course_name%type;
year_semister  course.year_semister%type;
credit  course.credit%type;
dept_id  course.dept_id%type;
begin
  f:=utl_file.fopen('CSE','COURSE.CSV','R');                                                                                                                                                                                                                                                                                                     
  if utl_file.is_open(f) then
  utl_file.get_line(f,line,1000);
  loop begin
  utl_file.get_line(f,line,1000);
  if line is null then exit;
  end if;
  course_no:=regexp_substr(line,'[^,]+',1,1);
  course_name:=regexp_substr(line,'[^,]+',1,2);
  year_semister:=regexp_substr(line,'[^,]+',1,3);
  credit:=regexp_substr(line,'[^,]+',1,4);
  dept_id:=regexp_substr(line,'[^,]+',1,5);
  insert into course values(course_no,course_name,year_semister,credit,dept_id);
  commit;
  exception
  when no_data_found then exit;
  end;
  end loop;
  end if;
  utl_file.fclose(f); 
  end;
  /
```
## File_Write(course):
```
set serveroutput on
declare 
f utl_file.file_type;
cursor c is select * from DEPT;
begin
f:=utl_file.fopen('CSE','NEW_DEPT.csv','W');
utl_file.put(f,'dept_id' ||','||'dept_name' ||','||'faculty'||','||'no_of_student');
utl_file.new_line(f);
    for c_record in c
    loop
    utl_file.put(f,c_record.dept_id ||','||c_record.dept_name ||','||c_record.faculty||','||c_record.no_of_student);
    utl_file.new_line(f);
    end loop;
 utl_file.fclose(f);
 end;
 /

```
