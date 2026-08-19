CREATE DATABASE college_demo; USE college_demo;
CREATE TABLE department ( dept_id INT PRIMARY KEY, dept_name VARCHAR(50) UNIQUE NOT NULL );
CREATE TABLE student (
roll_no INT PRIMARY KEY,
name VARCHAR(50) NOT NULL,
email VARCHAR(50) UNIQUE,
aadhar_no VARCHAR(12) UNIQUE,
dept_id INT,
FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);

CREATE TABLE course (
course_id INT PRIMARY KEY,
course_name VARCHAR(50) NOT NULL,
dept_id INT,
FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);

CREATE TABLE enrollment (
 roll_no INT,
 course_id INT,
semester INT CHECK (semester BETWEEN 1 AND 8),
 grade CHAR(2),
PRIMARY KEY (roll_no, course_id, semester),
FOREIGN KEY (roll_no) REFERENCES student (roll_no),
FOREIGN KEY (course_id) REFERENCES course (course_id)
);

insert into department values (1, 'computer science'), (2, 'Mechanical');
insert into student values (29, 'Jalpan', 'jalpan@gmail.com', '9786534', 1);
nsert into student values (12, 'shivtej', 'shivtej@gmail.com', 1324576, 2);
insert into course values (501, 'DBMS', 1), (502, 'circuits', 2);
insert into enrollment values (101, 501, 3, 'B');
insert into enrollment values (101, 502, 3, 'A');

create table faculty (
faculty_id int primary key,
dept_id int,
first_name varchar(50),
last_name varchar(50),
salary float,
foreign key (dept_id) references department(dept_id)
);
