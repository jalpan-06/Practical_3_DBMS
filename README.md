#  College Database Management System

A relational database project built with **MySQL** to model and manage core academic information for a college. The database demonstrates relationships between departments, students, courses, enrollments, and faculty members using SQL constraints and relational database concepts.

---

##  Overview

The `college_demo` database consists of five main entities:

* **Department** – Stores information about academic departments.
* **Student** – Stores student details and their department associations.
* **Course** – Stores courses offered by each department.
* **Enrollment** – Records student course enrollments, semesters, and grades.
* **Faculty** – Stores faculty information and their associated departments.

The project demonstrates the use of **Primary Keys, Foreign Keys, Unique Constraints, NOT NULL Constraints, CHECK Constraints, and Composite Primary Keys**.

---

##  Database Schema

### Department

Stores information about college departments.

| Column      | Data Type   | Constraints      | Description                  |
| ----------- | ----------- | ---------------- | ---------------------------- |
| `dept_id`   | INT         | PRIMARY KEY      | Unique department identifier |
| `dept_name` | VARCHAR(50) | UNIQUE, NOT NULL | Name of the department       |

### Student

Stores student personal and academic information.

| Column      | Data Type   | Constraints | Description                   |
| ----------- | ----------- | ----------- | ----------------------------- |
| `roll_no`   | INT         | PRIMARY KEY | Unique student roll number    |
| `name`      | VARCHAR(50) | NOT NULL    | Student's name                |
| `email`     | VARCHAR(50) | UNIQUE      | Student email                 |
| `aadhar_no` | VARCHAR(12) | UNIQUE      | Student identification number |
| `dept_id`   | INT         | FOREIGN KEY | Associated department         |

### Course

Stores courses offered by departments.

| Column        | Data Type   | Constraints | Description                    |
| ------------- | ----------- | ----------- | ------------------------------ |
| `course_id`   | INT         | PRIMARY KEY | Unique course identifier       |
| `course_name` | VARCHAR(50) | NOT NULL    | Name of the course             |
| `dept_id`     | INT         | FOREIGN KEY | Department offering the course |

### Enrollment

Stores the courses taken by students.

| Column      | Data Type | Constraints              | Description            |
| ----------- | --------- | ------------------------ | ---------------------- |
| `roll_no`   | INT       | PRIMARY KEY, FOREIGN KEY | Student roll number    |
| `course_id` | INT       | PRIMARY KEY, FOREIGN KEY | Course identifier      |
| `semester`  | INT       | CHECK (1–8)              | Semester of enrollment |
| `grade`     | CHAR(2)   | —                        | Grade received         |

The table uses a **composite primary key**:

```text
(roll_no, course_id, semester)
```

This ensures that the same student cannot be enrolled in the same course more than once in the same semester.

### Faculty

Stores faculty members and their department associations.

| Column       | Data Type   | Constraints | Description               |
| ------------ | ----------- | ----------- | ------------------------- |
| `faculty_id` | INT         | PRIMARY KEY | Unique faculty identifier |
| `dept_id`    | INT         | FOREIGN KEY | Associated department     |
| `first_name` | VARCHAR(50) | —           | Faculty first name        |
| `last_name`  | VARCHAR(50) | —           | Faculty last name         |
| `salary`     | FLOAT       | —           | Faculty salary            |

---

## 🔗 Entity Relationships

```text
                    ┌───────────────┐
                    │   Department  │
                    └───────┬───────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              ▼             ▼             ▼
        ┌──────────┐   ┌──────────┐  ┌──────────┐
        │ Student  │   │  Course  │  │ Faculty  │
        └────┬─────┘   └────┬─────┘  └──────────┘
             │              │
             │              │
             └──────┬───────┘
                    ▼
             ┌──────────────┐
             │  Enrollment  │
             └──────────────┘
```

### Relationships

* A **Department** can have multiple students.
* A **Department** can offer multiple courses.
* A **Department** can have multiple faculty members.
* A **Student** can enroll in multiple courses.
* A **Course** can have multiple students.
* `Enrollment` creates a **many-to-many relationship** between `Student` and `Course`.
* Each enrollment belongs to a specific semester and has a grade.

---

##  SQL Implementation

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

---

##  Sample Data

### Departments

```sql
INSERT INTO department
VALUES
    (1, 'computer science'),
    (2, 'Mechanical');
```

### Students

```sql
INSERT INTO student
VALUES
    (29, 'Jalpan', 'jalpan@gmail.com', '9786534', 1),
    (12, 'shivtej', 'shivtej@gmail.com', '1324576', 2);
```

### Courses

```sql
INSERT INTO course
VALUES
    (501, 'DBMS', 1),
    (502, 'circuits', 2);
```

### Enrollment

```sql
INSERT INTO enrollment
VALUES
    (29, 501, 3, 'B'),
    (12, 502, 3, 'A');
```

---

##  Useful SQL Queries

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

### Display enrollment records

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

### Display courses with departments

```sql
SELECT
    c.course_id,
    c.course_name,
    d.dept_name
FROM course c
JOIN department d
    ON c.dept_id = d.dept_id;
```

### Display complete enrollment information

```sql
SELECT
    s.roll_no,
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

---

##  SQL Concepts Demonstrated

This project demonstrates:

* `CREATE DATABASE`
* `USE DATABASE`
* `CREATE TABLE`
* `INSERT INTO`
* Primary Keys
* Foreign Keys
* Composite Primary Keys
* `UNIQUE` constraints
* `NOT NULL` constraints
* `CHECK` constraints
* One-to-Many relationships
* Many-to-Many relationships
* `JOIN` operations
* Relational database design
* Referential integrity

---

##  Getting Started

### Prerequisites

Install any MySQL-compatible environment, such as:

* [MySQL](https://www.mysql.com/)
* MySQL Workbench
* XAMPP
* WAMP
* DBeaver
* phpMyAdmin

### Installation

1. Clone the repository:

```bash
git clone https://github.com/your-username/college-database.git
```

2. Navigate to the project directory:

```bash
cd college-database
```

3. Open the SQL file in MySQL Workbench or your preferred SQL editor.

4. Execute the script.

5. Verify the created tables:

```sql
SHOW TABLES;
```

---

##  Project Structure

```text
college-database/
│
├── college_demo.sql
└── README.md
```

---

##  Future Enhancements

The database can be expanded with:

* Student attendance management
* Examination and marks management
* Faculty-course assignments
* Class timetable management
* Fees and payment tracking
* Semester-wise results
* Student performance reports
* Course credits
* Stored procedures
* Views
* Triggers
* Indexing and query optimization

---

##  License

This project is created for **educational and learning purposes**. You are free to modify and use it for academic projects and SQL practice.

---

##  Author

**Jalpan Bhavesh Mandavia**

College Database Management System
*MySQL & Relational Database Practice Project*

