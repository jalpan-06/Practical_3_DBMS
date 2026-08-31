#  College Database — SQL Normalization Demo

A relational database project demonstrating **SQL table design, primary keys, foreign keys, unique constraints, check constraints, and database normalization** using a simple college management system.

The database contains departments, students, courses, enrollments, and faculty.

---

##  Project Overview

This project creates a normalized college database using MySQL.

The main objectives are to:

* Demonstrate relational database design
* Apply **1NF, 2NF, and 3NF**
* Understand primary and foreign keys
* Enforce entity and referential integrity
* Avoid unnecessary data duplication
* Establish relationships between departments, students, courses, enrollments, and faculty

---

##  Database Structure

The database is named:

```sql
college_demo
```

### Tables

| Table        | Purpose                                      |
| ------------ | -------------------------------------------- |
| `department` | Stores department information                |
| `student`    | Stores student details                       |
| `course`     | Stores courses offered by departments        |
| `enrollment` | Connects students with courses and semesters |
| `faculty`    | Stores faculty information                   |

### Relationships

```text
Department
   │
   ├────────── Student
   │              │
   │              │
   │          Enrollment
   │              │
   │              │
   └────────── Course
   │
   └────────── Faculty
```

---

#  Keys and Constraints

The database uses several important relational constraints.

### Primary Keys

Each table has an appropriate primary key:

* `department.dept_id`
* `student.roll_no`
* `course.course_id`
* `faculty.faculty_id`
* `enrollment(roll_no, course_id, semester)` — composite primary key

### Foreign Keys

Foreign keys establish relationships between tables:

```text
student.dept_id       → department.dept_id
course.dept_id        → department.dept_id
faculty.dept_id       → department.dept_id
enrollment.roll_no    → student.roll_no
enrollment.course_id  → course.course_id
```

### Other Constraints

The schema also uses:

* `NOT NULL`
* `UNIQUE`
* `CHECK`
* `PRIMARY KEY`
* `FOREIGN KEY`

---

#  Normalization Analysis

##  First Normal Form — 1NF

### Status:  Applied

A table satisfies **1NF** when:

1. Each column contains atomic values.
2. There are no repeating groups.
3. Each row can be uniquely identified.

Your database follows these principles.

For example:

```sql
CREATE TABLE student (
    roll_no INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(50) UNIQUE,
    aadhar_no VARCHAR(12) UNIQUE,
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);
```

Each attribute contains a single value.

For example:

```text
roll_no | name   | email
--------|--------|-------------------
29      | Jalpan | jalpan@gmail.com
```

There are no columns such as:

```text
course1
course2
course3
```

which would violate the idea of atomic attributes.

### Improvement

The `name` column could be further divided:

```sql
first_name VARCHAR(50),
last_name VARCHAR(50)
```

This is not strictly required for 1NF, but it provides better data structure and makes searching and sorting by first/last name easier.

---

#  Second Normal Form — 2NF

### Status:  Applied

A table is in **2NF** when:

1. It is already in 1NF.
2. Every non-key attribute depends on the **entire primary key**, not just part of it.

The most important example is the `enrollment` table.

```sql
CREATE TABLE enrollment (
    roll_no INT,
    course_id INT,
    semester INT CHECK (semester BETWEEN 1 AND 8),
    grade CHAR(2),
    PRIMARY KEY (roll_no, course_id, semester),
    FOREIGN KEY (roll_no) REFERENCES student(roll_no),
    FOREIGN KEY (course_id) REFERENCES course(course_id)
);
```

The primary key is:

```text
(roll_no, course_id, semester)
```

The `grade` depends on the student's enrollment in a particular course during a particular semester.

Conceptually:

```text
(roll_no, course_id, semester)
                ↓
              grade
```

Therefore, there is no obvious partial dependency such as:

```text
roll_no → grade
```

because a student can take multiple courses and semesters.

### Why this is good

Instead of storing enrollment information inside the student table:

```text
student
--------------------------------
roll_no | course1 | course2 | ...
```

you created a separate associative table:

```text
enrollment
-------------------------------------
roll_no | course_id | semester | grade
```

This is a strong relational design choice.

---

#  Third Normal Form — 3NF

### Status:  Applied

A table is in **3NF** when:

1. It is in 2NF.
2. There are no inappropriate transitive dependencies between non-key attributes.

Your department information is separated into its own table:

```sql
CREATE TABLE department (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50) UNIQUE NOT NULL
);
```

Instead of storing:

```text
student
----------------------------------------
roll_no | name | dept_id | dept_name
```

you store:

```text
student
-------------------------
roll_no | name | dept_id
```

and:

```text
department
-----------------------
dept_id | dept_name
```

This avoids repeating department names for every student.

The dependency becomes:

```text
roll_no → dept_id
dept_id → dept_name
```

Rather than storing `dept_name` directly inside `student`.

The same principle is applied to `course` and `faculty`.

---

#  Boyce-Codd Normal Form — BCNF

### Status:  Mostly Applied

BCNF is stricter than 3NF.

A relation is in BCNF if:

> Every determinant is a candidate key.

Your tables generally follow this principle.

For example:

```text
department
dept_id → dept_name
```

`dept_id` is the primary key.

Also:

```text
student
roll_no → name, email, aadhar_no, dept_id
```

`roll_no` is the primary key.

Additionally, `email` and `aadhar_no` are declared `UNIQUE`, meaning they behave as candidate keys in the design.

So the current schema is **close to BCNF**.

However, declaring a column `UNIQUE` does not necessarily mean the business rule guarantees it will always be a candidate key. That depends on the real-world requirements of the system.

---

#  Fourth Normal Form — 4NF

### Status:  Not Specifically Demonstrated

4NF deals with **multivalued dependencies**.

For example, suppose a student independently has:

```text
multiple hobbies
multiple phone numbers
```

A poorly designed table might contain:

```text
student
--------------------------------
roll_no | hobby | phone_number
```

This could create unnecessary combinations:

```text
29 | Cricket | 9876543210
29 | Cricket | 9999999999
29 | Football | 9876543210
29 | Football | 9999999999
```

A normalized design would separate these independent multivalued relationships:

```text
student_hobby
student_phone
```

Your current schema does not contain such independent multivalued attributes, so **4NF is not really being tested by this project**.

---

#  Fifth Normal Form — 5NF

### Status:  Not Specifically Demonstrated

5NF deals with **join dependencies** and situations where a table can be decomposed into multiple smaller tables without losing information.

Your college database does not contain a complex enough relationship to meaningfully demonstrate 5NF.

Therefore, it is better to describe this project as primarily demonstrating:

```text
1NF → 2NF → 3NF
```

with a design that is generally compatible with **BCNF**.

---

#  Normalization Summary

| Normal Form | Status             | Explanation                                             |
| ----------- | ------------------ | ------------------------------------------------------- |
| **1NF**     |  Applied           | Atomic values and unique rows                           |
| **2NF**     |  Applied           | No problematic partial dependencies                     |
| **3NF**     |  Applied           | Department data separated from students/courses/faculty |
| **BCNF**    |  Mostly applied    | Determinants are generally candidate keys               |
| **4NF**     |  Not demonstrated  | No meaningful multivalued dependencies                  |
| **5NF**     |  Not demonstrated  | No complex join dependencies                            |

---

#  Recommended Improvements

Although the database is reasonably normalized, several improvements can make it more professional.

## 1. Split `name` into first and last name

Current:

```sql
name VARCHAR(50)
```

Better:

```sql
first_name VARCHAR(50) NOT NULL,
last_name VARCHAR(50) NOT NULL
```

This provides more flexibility for:

* Sorting
* Searching
* Formal reports
* Personalized greetings
* Data analysis

---

## 2. Use `DECIMAL` instead of `FLOAT` for salary

Current:

```sql
salary FLOAT
```

For financial values, `FLOAT` can introduce floating-point precision issues.

Better:

```sql
salary DECIMAL(10,2)
```

Example:

```text
75000.50
```

This is more appropriate for salary data.

---

## 3. Make important foreign keys `NOT NULL`

Currently:

```sql
dept_id INT
```

If every student must belong to a department, use:

```sql
dept_id INT NOT NULL
```

Similarly, if every course and faculty member must belong to a department:

```sql
dept_id INT NOT NULL
```

This prevents orphaned or incomplete records.

---

## 4. Improve the Aadhar number datatype

You currently have:

```sql
aadhar_no VARCHAR(12)
```

Using `VARCHAR` is actually preferable to an integer because identification numbers should generally be treated as **identifiers, not numbers**.

However, you should enforce the expected format.

For example, in MySQL:

```sql
aadhar_no VARCHAR(12) UNIQUE
```

could be supplemented with an appropriate validation rule.

Avoid storing it as:

```sql
aadhar_no INT
```

because leading zeros could be lost.

---

## 5. Improve naming consistency

Use consistent naming conventions throughout the database.

For example:

```text
department
student
course
enrollment
faculty
```

This is already reasonably consistent.

For columns, maintain a consistent style:

```text
dept_id
course_id
faculty_id
roll_no
```

Avoid mixing styles such as:

```text
deptId
course_ID
FacultyID
```

---

#  Improved Schema

A more production-ready version could look like this:

```sql
CREATE DATABASE college_demo;
USE college_demo;

CREATE TABLE department (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE student (
    roll_no INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50),
    email VARCHAR(100) NOT NULL UNIQUE,
    aadhar_no VARCHAR(12) NOT NULL UNIQUE,
    dept_id INT NOT NULL,

    FOREIGN KEY (dept_id)
        REFERENCES department(dept_id)
);

CREATE TABLE course (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(50) NOT NULL,
    dept_id INT NOT NULL,

    FOREIGN KEY (dept_id)
        REFERENCES department(dept_id)
);

CREATE TABLE enrollment (
    roll_no INT,
    course_id INT,
    semester INT NOT NULL,
    grade CHAR(2),

    PRIMARY KEY (roll_no, course_id, semester),

    FOREIGN KEY (roll_no)
        REFERENCES student(roll_no),

    FOREIGN KEY (course_id)
        REFERENCES course(course_id),

    CHECK (semester BETWEEN 1 AND 8)
);

CREATE TABLE faculty (
    faculty_id INT PRIMARY KEY,
    dept_id INT NOT NULL,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50),
    salary DECIMAL(10,2),

    FOREIGN KEY (dept_id)
        REFERENCES department(dept_id)
);
```

---

#  Entity Relationship Overview

The relationships can be represented as:

```text
                    ┌─────────────────┐
                    │   DEPARTMENT    │
                    ├─────────────────┤
                    │ PK dept_id      │
                    │    dept_name    │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              │              │              │
              ▼              ▼              ▼
       ┌────────────┐ ┌────────────┐ ┌────────────┐
       │  STUDENT   │ │   COURSE   │ │  FACULTY   │
       ├────────────┤ ├────────────┤ ├────────────┤
       │ PK roll_no │ │PK course_id│ │PK faculty_id│
       │ first_name │ │ course_name│ │ first_name │
       │ last_name  │ │ FK dept_id │ │ last_name  │
       │ email      │ └─────┬──────┘ │ salary     │
       │ aadhar_no  │       │        │ FK dept_id │
       │ FK dept_id │       │        └────────────┘
       └──────┬─────┘       │
              │              │
              └──────┬───────┘
                     ▼
             ┌────────────────┐
             │   ENROLLMENT   │
             ├────────────────┤
             │ PK roll_no     │
             │ PK course_id   │
             │ PK semester    │
             │ grade          │
             └────────────────┘
```

---

#  Key Normalization Concepts Demonstrated

This project demonstrates several important database-design concepts.

### Atomic Data

Each field stores one logical value.

### Entity Separation

Departments, students, courses, and faculty are maintained separately.

### Relationship Modeling

The `enrollment` table models the many-to-many relationship between students and courses.

```text
Student ───< Enrollment >─── Course
```

### Composite Primary Key

The enrollment table uses:

```sql
PRIMARY KEY (roll_no, course_id, semester)
```

This prevents the same student from being enrolled in the same course multiple times in the same semester.

### Referential Integrity

Foreign keys prevent invalid references.

For example, an enrollment cannot reference a student that doesn't exist.

---

#  Important Data-Modeling Consideration

There is one business rule that deserves further consideration:

```sql
PRIMARY KEY (roll_no, course_id, semester)
```

This assumes a student can have only **one enrollment record for a particular course in a particular semester**.

If the college allows re-examination, retakes, multiple attempts, or multiple sections of the same course, the enrollment design may need additional attributes such as:

```text
attempt_no
section_id
academic_year
```

For example:

```text
roll_no | course_id | semester | academic_year | attempt_no | grade
```

The correct design depends on the college's actual business rules.

---

#  Possible Future Enhancements

This project can be expanded with:

* `classroom` table
* `attendance` table
* `exam` table
* `result` table
* `course_faculty` table
* `academic_year`
* `semester`
* `student_phone` table
* `student_address` table
* `course_prerequisite` table
* Indexes for frequently searched columns
* `ON DELETE` and `ON UPDATE` rules
* Views
* Stored procedures
* Triggers
* SQL queries for reports and analytics

---

#  Normalization Level of This Project

The current project should be described professionally as:

> **A normalized relational database primarily demonstrating First Normal Form (1NF), Second Normal Form (2NF), and Third Normal Form (3NF), with a generally BCNF-compatible design. 4NF and 5NF are not specifically demonstrated because the schema does not contain meaningful multivalued or complex join dependencies.**

This is more accurate than claiming that the database is "fully normalized to 5NF."

---

#  Technologies

* **Database:** MySQL
* **Language:** SQL
* **Concepts:** Relational Database Design, Normalization, Primary Keys, Foreign Keys, Constraints, Entity Relationships

---

#  Suggested GitHub Repository Structure

```text
college-database/
│
├── README.md
│
├── sql/
│   ├── 01_create_database.sql
│   ├── 02_create_tables.sql
│   ├── 03_insert_data.sql
│   └── 04_queries.sql
│
├── diagrams/
│   └── er-diagram.png
│
└── docs/
    └── normalization.md
```

---

#  Author

**Jalpan**

College Database — SQL Normalization Practice Project

---

##  Conclusion

The database is a good starting point for learning relational database normalization.

The strongest parts of the current design are:

* Separate department entity
* Separate student and course entities
* Proper many-to-many `enrollment` table
* Composite primary key in enrollment
* Foreign-key relationships
* Unique constraints on email and Aadhar number
* Check constraint on semester

The main improvements I'd recommend are:

1. Split `name` into `first_name` and `last_name`
2. Change `salary FLOAT` to `DECIMAL(10,2)`
3. Make required foreign keys `NOT NULL`
4. Consider adding `academic_year` to enrollment
5. Add appropriate indexes as the database grows
6. Define explicit `ON DELETE` / `ON UPDATE` behavior
7. Add additional tables only when the business requirements justify them

**Overall:** the schema demonstrates **1NF → 2NF → 3NF well**, is **generally BCNF-compatible**, and does not need to artificially introduce 4NF/5NF structures just to claim higher normalization.

## created an index
    
create index idx_student_dept on student(dept_id);
create index idx_student_dept → Creates a new index named idx_student_dept.
on student(dept_id) → The index is built on the dept_id column of the student table.

select * from student where dept_id = 1;
select * from student → Retrieves all columns from the student table.
where dept_id = 1 → Filters rows where the department ID equals 1.
