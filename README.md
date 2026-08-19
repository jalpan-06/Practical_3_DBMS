# College Database Management System

A relational database project built using SQL to demonstrate the design and implementation of a college management database. The project includes departments, students, courses, enrollments, and faculty, with appropriate **primary keys, foreign keys, unique constraints, not-null constraints, and check constraints**.

## 📌 Overview

The `college_demo` database models common academic entities and their relationships:

* **Department** – Stores department information.
* **Student** – Stores student personal and academic information.
* **Course** – Stores courses offered by departments.
* **Enrollment** – Connects students with courses and stores semester and grade information.
* **Faculty** – Stores faculty details and their associated departments.

The project demonstrates fundamental **Relational Database Management System (RDBMS)** concepts and SQL constraints.

## 🗄️ Database Schema

### Department

| Column      | Data Type   | Constraint       | Description            |
| ----------- | ----------- | ---------------- | ---------------------- |
| `dept_id`   | INT         | Primary Key      | Unique department ID   |
| `dept_name` | VARCHAR(50) | UNIQUE, NOT NULL | Name of the department |

### Student

| Column      | Data Type   | Constraint  | Description                   |
| ----------- | ----------- | ----------- | ----------------------------- |
| `roll_no`   | INT         | Primary Key | Unique student roll number    |
| `name`      | VARCHAR(50) | NOT NULL    | Student's name                |
| `email`     | VARCHAR(50) | UNIQUE      | Student email address         |
| `aadhar_no` | VARCHAR(12) | UNIQUE      | Student identification number |
| `dept_id`   | INT         | Foreign Key | Student's department          |

### Course

| Column        | Data Type   | Constraint  | Description                    |
| ------------- | ----------- | ----------- | ------------------------------ |
| `course_id`   | INT         | Primary Key | Unique course ID               |
| `course_name` | VARCHAR(50) | NOT NULL    | Name of the course             |
| `dept_id`     | INT         | Foreign Key | Department offering the course |

### Enrollment

| Column      | Data Type | Constraint               | Description                    |
| ----------- | --------- | ------------------------ | ------------------------------ |
| `roll_no`   | INT       | Primary Key, Foreign Key | Enrolled student's roll number |
| `course_id` | INT       | Primary Key, Foreign Key | Enrolled course                |
| `semester`  | INT       | CHECK (1–8)              | Semester of enrollment         |
| `grade`     | CHAR(2)   | —                        | Grade received                 |

The enrollment table uses a **composite primary key** consisting of:

```text
(roll_no, course_id, semester)
```

### Faculty

| Column       | Data Type   | Constraint  | Description                 |
| ------------ | ----------- | ----------- | --------------------------- |
| `faculty_id` | INT         | Primary Key | Unique faculty ID           |
| `dept_id`    | INT         | Foreign Key | Faculty member's department |
| `first_name` | VARCHAR(50) | —           | Faculty member's first name |
| `last_name`  | VARCHAR(50) | —           | Faculty member's last name  |
| `salary`     | FLOAT       | —           | Faculty salary              |

## 🔗 Database Relationships

```text
Department
    │
    ├───────────────┐
    │               │
    ▼               ▼
 Student          Course
    │               │
    └───────┬───────┘
            ▼
       Enrollment

Department
    │
    ▼
 Faculty
```

### Relationships

* One **Department** can have many **Students**.
* One **Department** can offer many **Courses**.
* One **Student** can enroll in multiple **Courses**.
* One **Course** can have multiple **Students**.
* The `Enrollment` table implements the many-to-many relationship between students and courses.
* One **Department** can have multiple **Faculty** members.

## 💻 SQL Implementation

```sql
CREATE DATABASE college_demo;
USE college_demo;

CREATE TABLE department (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50) UNIQUE NOT NULL
);

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
    FOREIGN KEY (roll_no) REFERENCES student(roll_no),
    FOREIGN KEY (course_id) REFERENCES course(course_id)
);

CREATE TABLE faculty (
    faculty_id INT PRIMARY KEY,
    dept_id INT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    salary FLOAT,
    FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);
```

## 📥 Sample Data

### Departments

```sql
INSERT INTO department
VALUES
    (1, 'Computer Science'),
    (2, 'Mechanical');
```

### Students

```sql
INSERT INTO student
VALUES
    (29, 'Jalpan', 'jalpan@gmail.com', '9786534', 1),
    (12, 'Shivtej', 'shivtej@gmail.com', '1324576', 2);
```

### Courses

```sql
INSERT INTO course
VALUES
    (501, 'DBMS', 1),
    (502, 'Circuits', 2);
```

## ⚠️ Corrections to the Original SQL

The original script contains a few issues that should be corrected before execution.

### 1. Typographical Error

The following statement:

```sql
nsert into student values ...
```

should be:

```sql
INSERT INTO student VALUES
(12, 'Shivtej', 'shivtej@gmail.com', '1324576', 2);
```

### 2. Enrollment Roll Number

The original enrollment statements use `roll_no = 101`:

```sql
INSERT INTO enrollment VALUES (101, 501, 3, 'B');
INSERT INTO enrollment VALUES (101, 502, 3, 'A');
```

However, the students inserted have roll numbers `29` and `12`. Because `enrollment.roll_no` is a foreign key referencing `student.roll_no`, `101` does not exist in the `student` table.

A valid example would be:

```sql
INSERT INTO enrollment
VALUES
    (29, 501, 3, 'B');

INSERT INTO enrollment
VALUES
    (12, 502, 3, 'A');
```

## 🚀 Getting Started

### Prerequisites

You need an SQL-compatible database system. This project is primarily written in **MySQL syntax**.

Recommended tools:

* MySQL
* MySQL Workbench
* XAMPP
* WAMP
* phpMyAdmin
* DBeaver

### Installation

1. Clone the repository:

```bash
git clone https://github.com/your-username/college-database.git
```

2. Open MySQL Workbench or another SQL client.

3. Open the SQL script.

4. Execute the database and table creation statements.

5. Insert the sample data.

6. Verify the tables:

```sql
SHOW TABLES;
```

## 🔍 Useful Queries

### Display all students

```sql
SELECT * FROM student;
```

### Display all departments

```sql
SELECT * FROM department;
```

### Display all courses

```sql
SELECT * FROM course;
```

### Display student enrollments

```sql
SELECT * FROM enrollment;
```

### Display students with their departments

```sql
SELECT
    s.roll_no,
    s.name,
    d.dept_name
FROM student s
JOIN department d
    ON s.dept_id = d.dept_id;
```

### Display courses with their departments

```sql
SELECT
    c.course_id,
    c.course_name,
    d.dept_name
FROM course c
JOIN department d
    ON c.dept_id = d.dept_id;
```

### Display enrollment details

```sql
SELECT
    s.name,
    c.course_name,
    e.semester,
    e.grade
FROM enrollment e
JOIN student s
    ON e.roll_no = s.roll_no
JOIN course c
    ON e.course_id = c.course_id;
```

## 🧩 SQL Concepts Demonstrated

This project covers several important SQL concepts:

* `CREATE DATABASE`
* `USE`
* `CREATE TABLE`
* `INSERT INTO`
* Primary Keys
* Foreign Keys
* Composite Primary Keys
* `UNIQUE` constraints
* `NOT NULL` constraints
* `CHECK` constraints
* Table relationships
* One-to-many relationships
* Many-to-many relationships
* `JOIN` operations
* Database normalization concepts

## 📁 Project Structure

```text
college-database/
│
├── college_demo.sql
└── README.md
```

## 🔮 Future Enhancements

The database can be extended with additional functionality, such as:

* Attendance management
* Examination and marks tables
* Student addresses
* Faculty courses
* Course credits
* Class schedules
* Fees and payment management
* Semester-wise results
* Student performance reports
* Stored procedures
* Views
* Triggers
* Indexing and query optimization

## 📜 License

This project is intended for educational and learning purposes. You are free to modify and extend it for academic projects and SQL practice.

## 👨‍💻 Author

**Jalpan Bhavesh Mandavia**

College Database Management System
SQL Practice Project
