# Database Normalization — College Demo

This document explains the **normalization applied** to the College Database schema and identifies which normal forms are satisfied, which are not specifically demonstrated, and how the schema can be improved.

---

##  Overview

Database normalization is the process of organizing data into well-structured relational tables to:

* Reduce data redundancy
* Prevent update, insertion, and deletion anomalies
* Improve data integrity
* Establish clear relationships between entities
* Make the database easier to maintain

The College Database primarily demonstrates:

```text
1NF → 2NF → 3NF
```

The design is also **generally compatible with BCNF**, while 4NF and 5NF are not specifically demonstrated.

---

#  Normalization Summary

| Normal Form | Status              | Applied In                                   |
| ----------- | ------------------- | -------------------------------------------- |
| **1NF**     |  Applied            | All tables                                   |
| **2NF**     |  Applied            | Especially `enrollment`                      |
| **3NF**     |  Applied            | `student`, `course`, `faculty`, `department` |
| **BCNF**    |  Mostly satisfied   | Most relations                               |
| **4NF**     |  Not demonstrated   | No multivalued dependencies                  |
| **5NF**     |  Not demonstrated   | No complex join dependencies                 |

---

#  First Normal Form — 1NF

## Status:  Applied

A relation is in **First Normal Form (1NF)** when:

1. Each column contains atomic values.
2. There are no repeating groups.
3. Each row is uniquely identifiable.

The database follows these principles.

For example, the `student` table contains individual attributes:

```sql
student
---------------------------------------------
roll_no | name | email | aadhar_no | dept_id
```

Each attribute contains a single logical value.

###  Non-normalized example

A poorly designed student table might contain:

```text
roll_no | name | course1 | course2 | course3
```

This creates repeating groups.

###  Normalized approach

The project separates courses and enrollments:

```text
student
   ↓
enrollment
   ↓
course
```

This keeps the values atomic and avoids repeating course columns.

### Conclusion

**1NF is satisfied.**

---

#  Second Normal Form — 2NF

## Status:  Applied

A table is in **Second Normal Form (2NF)** when:

1. It is already in 1NF.
2. Every non-key attribute depends on the **whole primary key**.

The `enrollment` table is the most important example.

```sql
CREATE TABLE enrollment (
    roll_no INT,
    course_id INT,
    semester INT,
    grade CHAR(2),

    PRIMARY KEY (roll_no, course_id, semester)
);
```

The composite primary key is:

```text
(roll_no, course_id, semester)
```

The student's grade is associated with the complete enrollment:

```text
(roll_no, course_id, semester)
                ↓
              grade
```

`grade` does not depend only on `roll_no`, because a student can take multiple courses.

It also does not depend only on `course_id`, because multiple students can take the same course.

Therefore, there is no problematic partial dependency.

###  Example of a partial dependency

A poorly designed table could look like:

```text
roll_no | course_id | course_name
```

with:

```text
course_id → course_name
```

If `(roll_no, course_id)` were the primary key, `course_name` would depend only on part of the key (`course_id`).

That violates 2NF.

###  Solution

The project separates course information:

```text
course
-------------------------
course_id
course_name
dept_id
```

from enrollment information:

```text
enrollment
-------------------------
roll_no
course_id
semester
grade
```

### Conclusion

**2NF is satisfied.**

---

#  Third Normal Form — 3NF

## Status:  Applied

A table is in **Third Normal Form (3NF)** when:

1. It is already in 2NF.
2. Non-key attributes do not depend on other non-key attributes.

In other words, there should be no problematic **transitive dependency**.

---

## Example: Department

Instead of storing department information repeatedly:

```text
student
------------------------------------------------
roll_no | name | dept_id | dept_name
```

the database separates it into:

```text
department
---------------------
dept_id
dept_name
```

and:

```text
student
---------------------
roll_no
name
dept_id
```

The relationship is:

```text
roll_no → dept_id
dept_id → dept_name
```

`dept_name` is therefore not unnecessarily repeated in every student record.

---

## Example: Course

The course table contains:

```sql
CREATE TABLE course (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(50) NOT NULL,
    dept_id INT,
    FOREIGN KEY (dept_id)
        REFERENCES department(dept_id)
);
```

The department information is referenced through `dept_id` rather than duplicated.

---

## Example: Faculty

Faculty also references the department:

```text
faculty
------------------------------
faculty_id
first_name
last_name
salary
dept_id
```

The department name is not stored repeatedly for every faculty member.

### Conclusion

**3NF is satisfied.**

---

#  Boyce-Codd Normal Form — BCNF

## Status:  Mostly Satisfied

**Boyce-Codd Normal Form (BCNF)** is stricter than 3NF.

The basic rule is:

> Every determinant must be a candidate key.

For example:

```text
department
dept_id → dept_name
```

`dept_id` is the primary key, so this dependency satisfies BCNF.

Similarly:

```text
student
roll_no → name, email, aadhar_no, dept_id
```

`roll_no` is the primary key.

The schema therefore does not contain an obvious BCNF violation under its stated business rules.

However, BCNF should be evaluated against the **actual functional dependencies and business rules**, not just the SQL constraints.

### Conclusion

**The current design is generally BCNF-compatible, but BCNF is not the primary focus of this project.**

---

#  Fourth Normal Form — 4NF

## Status:  Not Specifically Demonstrated

Fourth Normal Form addresses **multivalued dependencies**.

For example, suppose a student can independently have multiple:

* Phone numbers
* Hobbies

A badly designed table could be:

```text
student
--------------------------------
roll_no | phone | hobby
```

This could create unnecessary combinations:

```text
29 | 9876543210 | Cricket
29 | 9876543210 | Football
29 | 9999999999 | Cricket
29 | 9999999999 | Football
```

A 4NF-oriented design would separate these independent relationships:

```text
student_phone
----------------
roll_no
phone
```

and:

```text
student_hobby
----------------
roll_no
hobby
```

The current College Database does not contain such independent multivalued dependencies.

### Conclusion

**4NF is not meaningfully demonstrated by the current schema.**

---

#  Fifth Normal Form — 5NF

## Status:  Not Specifically Demonstrated

Fifth Normal Form deals with **join dependencies** and complex cases where information can be decomposed into several relations without introducing incorrect combinations.

The current College Database does not contain sufficiently complex relationships to demonstrate 5NF meaningfully.

Therefore, there is no need to artificially modify the schema just to claim 5NF.

### Conclusion

**5NF is not demonstrated by the current schema.**

---

#  Normalization Before vs After

##  Poorly Normalized Design

A single table might contain:

```text
student
-----------------------------------------------------------------
roll_no | student_name | dept_name | course_name | faculty_name
```

This causes:

* Department data repetition
* Course data repetition
* Faculty data repetition
* Update anomalies
* Insertion anomalies
* Deletion anomalies

---

##  Normalized Design

The project separates entities:

```text
department
    │
    ├── student
    │
    ├── course
    │
    └── faculty

student
    │
    └── enrollment
            │
            └── course
```

This reduces unnecessary duplication and makes relationships explicit.

---

# 🛠️ Recommended Normalization Improvements

Although the schema already demonstrates 1NF–3NF, several improvements can make the design stronger.

## 1. Separate Student Name

Current:

```sql
name VARCHAR(50)
```

Recommended:

```sql
first_name VARCHAR(50),
last_name VARCHAR(50)
```

This provides more granular data.

---

## 2. Avoid Financial `FLOAT`

The `faculty` table currently uses:

```sql
salary FLOAT
```

For monetary values, use:

```sql
salary DECIMAL(10,2)
```

This avoids floating-point precision issues.

---

## 3. Make Mandatory Relationships `NOT NULL`

If every student must belong to a department:

```sql
dept_id INT NOT NULL
```

Likewise for courses and faculty where appropriate.

This strengthens data integrity.

---

## 4. Consider Academic Year in Enrollment

The current primary key is:

```text
(roll_no, course_id, semester)
```

If the college stores records across multiple academic years, consider:

```text
(roll_no, course_id, semester, academic_year)
```

For example:

```text
29 | 501 | 3 | 2026 | B
29 | 501 | 3 | 2027 | A
```

Without an academic year or another attempt identifier, these could conflict.

The correct choice depends on the actual business rules.

---

#  Anomalies Prevented by Normalization

Normalization helps prevent three major types of anomalies.

### Update Anomaly

Without normalization, changing a department name could require updating many student records.

With the current design:

```text
department
```

stores the department name once.

---

### Insertion Anomaly

A new department can be inserted without requiring a student record.

```sql
INSERT INTO department
VALUES (3, 'Civil Engineering');
```

---

### Deletion Anomaly

Deleting a student does not automatically remove the department information because department data exists independently.

---

#  Functional Dependency Overview

The major dependencies can be represented as:

```text
department
dept_id → dept_name
```

```text
student
roll_no → name, email, aadhar_no, dept_id
```

```text
course
course_id → course_name, dept_id
```

```text
faculty
faculty_id → dept_id, first_name, last_name, salary
```

```text
enrollment
(roll_no, course_id, semester) → grade
```

These dependencies explain why the tables can be separated into their current relational structure.

---

#  Final Assessment

The College Database is primarily a **1NF, 2NF, and 3NF normalized relational database**.

### Final Normalization Status

```text
1NF   
2NF   
3NF   
BCNF   Mostly satisfied
4NF    Not demonstrated
5NF    Not demonstrated
```

The most important normalization decision in the project is the separation of the **many-to-many Student–Course relationship** into the `enrollment` table.

```text
Student
   │
   │ 1
   │
   │
   │ N
Enrollment
   │
   │ N
   │
   │
   │ 1
Course
```

This eliminates repeating course information from the student table and provides a clean relational structure.

---

##  Conclusion

The schema successfully demonstrates the practical application of database normalization up to **Third Normal Form (3NF)**.

Rather than artificially applying higher normal forms, the design focuses on appropriate entity separation, functional dependencies, primary keys, foreign keys, and elimination of unnecessary redundancy.

> **Normalization is not about creating as many tables as possible. It is about creating the right tables based on the data dependencies and business rules.**

---

##  Concepts Demonstrated

* First Normal Form (1NF)
* Second Normal Form (2NF)
* Third Normal Form (3NF)
* Boyce-Codd Normal Form (BCNF)
* Functional Dependencies
* Partial Dependencies
* Transitive Dependencies
* Multivalued Dependencies
* Data Redundancy
* Update Anomalies
* Insertion Anomalies
* Deletion Anomalies
* Composite Primary Keys
* Referential Integrity
* Relational Decomposition
