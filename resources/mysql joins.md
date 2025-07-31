---
tags:
  - database
  - sql
  - red
cssclasses:
  - interface-red
---
# SQL Joins - Complete Guide with Examples

## Database Schema Overview

Based on the visible schema, we have the following tables:

```sql
-- Main Tables
instructors (id, name, department_id)
courses (id, course_name, instructor_id, department_id)
students (id, name, department_id)
assignments (id, course_id, assignment_name, due_date)
departments (id, department_name)
enrollments (id, student_id, course_id, enrollment_date)
```

## Types of SQL Joins

### 1. INNER JOIN

Returns only matching records from both tables.

**Syntax:**

```sql
SELECT columns
FROM table1
INNER JOIN table2 ON table1.column = table2.column;
```

**Example 1: Get courses with instructor names**

```sql
SELECT c.course_name, i.name AS instructor_name
FROM courses c
INNER JOIN instructors i ON c.instructor_id = i.id;
```

**Example 2: Get enrolled students with course names**

```sql
SELECT s.name AS student_name, c.course_name
FROM students s
INNER JOIN enrollments e ON s.id = e.student_id
INNER JOIN courses c ON e.course_id = c.id;
```

### 2. LEFT JOIN (LEFT OUTER JOIN)

Returns all records from the left table and matching records from the right table.

**Syntax:**

```sql
SELECT columns
FROM table1
LEFT JOIN table2 ON table1.column = table2.column;
```

**Example 1: Get all instructors and their courses (including instructors without courses)**

```sql
SELECT i.name AS instructor_name, c.course_name
FROM instructors i
LEFT JOIN courses c ON i.id = c.instructor_id;
```

**Example 2: Get all students and their enrollments (including students not enrolled)**

```sql
SELECT s.name AS student_name, c.course_name
FROM students s
LEFT JOIN enrollments e ON s.id = e.student_id
LEFT JOIN courses c ON e.course_id = c.id;
```

### 3. RIGHT JOIN (RIGHT OUTER JOIN)

Returns all records from the right table and matching records from the left table.

**Syntax:**

```sql
SELECT columns
FROM table1
RIGHT JOIN table2 ON table1.column = table2.column;
```

**Example: Get all courses and their instructors (including courses without assigned instructors)**

```sql
SELECT i.name AS instructor_name, c.course_name
FROM instructors i
RIGHT JOIN courses c ON i.id = c.instructor_id;
```

### 4. FULL OUTER JOIN

Returns all records when there's a match in either table.

**Syntax:**

```sql
SELECT columns
FROM table1
FULL OUTER JOIN table2 ON table1.column = table2.column;
```

**Example: Get all instructors and courses (whether matched or not)**

```sql
SELECT i.name AS instructor_name, c.course_name
FROM instructors i
FULL OUTER JOIN courses c ON i.id = c.instructor_id;
```

## Complex Join Examples

### Multiple Table Joins

**Example 1: Get student enrollments with complete details**

```sql
SELECT 
    s.name AS student_name,
    c.course_name,
    i.name AS instructor_name,
    d.department_name,
    e.enrollment_date
FROM students s
INNER JOIN enrollments e ON s.id = e.student_id
INNER JOIN courses c ON e.course_id = c.id
INNER JOIN instructors i ON c.instructor_id = i.id
INNER JOIN departments d ON c.department_id = d.id;
```

**Example 2: Get assignments with course and instructor details**

```sql
SELECT 
    a.assignment_name,
    a.due_date,
    c.course_name,
    i.name AS instructor_name
FROM assignments a
INNER JOIN courses c ON a.course_id = c.id
INNER JOIN instructors i ON c.instructor_id = i.id;
```

### Self Joins

**Example: Find instructors from the same department**

```sql
SELECT 
    i1.name AS instructor1,
    i2.name AS instructor2,
    d.department_name
FROM instructors i1
INNER JOIN instructors i2 ON i1.department_id = i2.department_id
INNER JOIN departments d ON i1.department_id = d.id
WHERE i1.id < i2.id;
```

## Problem Solutions from the Image

### 1. List all instructors (could be used in an admin panel)

```sql
SELECT id, name, department_id
FROM instructors;
```

### 2. Get a specific instructor by ID

```sql
SELECT id, name, department_id
FROM instructors
WHERE id = ?;
```

### 3. Get all courses taught by a specific instructor

```sql
SELECT c.id, c.course_name
FROM courses c
INNER JOIN instructors i ON c.instructor_id = i.id
WHERE i.id = ?;
```

### 4. Find which department an instructor belongs to

```sql
SELECT i.name AS instructor_name, d.department_name
FROM instructors i
INNER JOIN departments d ON i.department_id = d.id
WHERE i.id = ?;
```

### 5. List all students

```sql
SELECT id, name, department_id
FROM students;
```

### 6. Get a student by ID

```sql
SELECT id, name, department_id
FROM students
WHERE id = ?;
```

### 7. Find all courses a student is enrolled in

```sql
SELECT s.name AS student_name, c.course_name, e.enrollment_date
FROM students s
INNER JOIN enrollments e ON s.id = e.student_id
INNER JOIN courses c ON e.course_id = c.id
WHERE s.id = ?;
```

### 8. List all students not enrolled in any courses

```sql
SELECT s.id, s.name
FROM students s
LEFT JOIN enrollments e ON s.id = e.student_id
WHERE e.student_id IS NULL;
```

### 9. List all courses (show instructor name too)

```sql
SELECT c.id, c.course_name, i.name AS instructor_name
FROM courses c
LEFT JOIN instructors i ON c.instructor_id = i.id;
```

### 10. Get a specific course (ID) with department & instructor info

```sql
SELECT 
    c.id,
    c.course_name,
    i.name AS instructor_name,
    d.department_name
FROM courses c
LEFT JOIN instructors i ON c.instructor_id = i.id
LEFT JOIN departments d ON c.department_id = d.id
WHERE c.id = ?;
```

### 11. List all students in a particular course

```sql
SELECT s.id, s.name AS student_name, e.enrollment_date
FROM students s
INNER JOIN enrollments e ON s.id = e.student_id
WHERE e.course_id = ?;
```

### 12. Find how many students are enrolled in each course (aggregated)

```sql
SELECT 
    c.course_name,
    i.name AS instructor_name,
    COUNT(e.student_id) AS student_count
FROM courses c
LEFT JOIN instructors i ON c.instructor_id = i.id
LEFT JOIN enrollments e ON c.id = e.course_id
GROUP BY c.id, c.course_name, i.name
ORDER BY student_count DESC;
```

### 13. List all enrollments (student/course pairs)

```sql
SELECT 
    s.name AS student_name,
    c.course_name,
    e.enrollment_date
FROM enrollments e
INNER JOIN students s ON e.student_id = s.id
INNER JOIN courses c ON e.course_id = c.id;
```

### 14. Enroll a student in a course (INSERT)

```sql
INSERT INTO enrollments (student_id, course_id, enrollment_date)
VALUES (?, ?, CURRENT_DATE);
```

### 15. Unenroll a student from a course (DELETE)

```sql
DELETE FROM enrollments
WHERE student_id = ? AND course_id = ?;
```

### 16. List all assignments

```sql
SELECT 
    a.id,
    a.assignment_name,
    a.due_date,
    c.course_name
FROM assignments a
INNER JOIN courses c ON a.course_id = c.id;
```

### 17. Get all assignments for a particular course

```sql
SELECT id, assignment_name, due_date
FROM assignments
WHERE course_id = ?;
```

### 18. Get assignment details (with course/instructor info)

```sql
SELECT 
    a.id,
    a.assignment_name,
    a.due_date,
    c.course_name,
    i.name AS instructor_name
FROM assignments a
INNER JOIN courses c ON a.course_id = c.id
INNER JOIN instructors i ON c.instructor_id = i.id
WHERE a.id = ?;
```

### 19. Which instructor teaches the most courses? (count courses per instructor)

```sql
SELECT 
    i.name AS instructor_name,
    COUNT(c.id) AS course_count
FROM instructors i
LEFT JOIN courses c ON i.id = c.instructor_id
GROUP BY i.id, i.name
ORDER BY course_count DESC
LIMIT 1;
```

### 20. Which student is enrolled in the most courses? (count enrollments)

```sql
SELECT 
    s.name AS student_name,
    COUNT(e.course_id) AS enrollment_count
FROM students s
LEFT JOIN enrollments e ON s.id = e.student_id
GROUP BY s.id, s.name
ORDER BY enrollment_count DESC
LIMIT 1;
```

### 21. Courses with no students enrolled

```sql
SELECT c.id, c.course_name, i.name AS instructor_name
FROM courses c
LEFT JOIN instructors i ON c.instructor_id = i.id
LEFT JOIN enrollments e ON c.id = e.course_id
WHERE e.course_id IS NULL;
```

## Join Performance Tips

1. **Use indexes** on join columns for better performance
2. **Filter early** - use WHERE clauses to reduce dataset size
3. **Choose the right join type** based on your data requirements
4. **Avoid unnecessary columns** in SELECT to reduce memory usage
5. **Consider using EXISTS** instead of IN for subqueries

## Common Join Patterns

### Pattern 1: Master-Detail Relationship

```sql
-- Get orders with order items
SELECT o.order_id, oi.product_name, oi.quantity
FROM orders o
INNER JOIN order_items oi ON o.order_id = oi.order_id;
```

### Pattern 2: Many-to-Many Relationship

```sql
-- Students and courses (through enrollments)
SELECT s.name, c.course_name
FROM students s
INNER JOIN enrollments e ON s.id = e.student_id
INNER JOIN courses c ON e.course_id = c.id;
```

### Pattern 3: Hierarchical Data

```sql
-- Departments and sub-departments
SELECT 
    parent.department_name AS parent_dept,
    child.department_name AS child_dept
FROM departments parent
INNER JOIN departments child ON parent.id = child.parent_department_id;
```

## Quick Reference

|Join Type|Returns|Use Case|
|---|---|---|
|INNER JOIN|Only matching records|Most common, when you need data from both tables|
|LEFT JOIN|All from left + matching from right|When you need all records from the primary table|
|RIGHT JOIN|All from right + matching from left|Less common, opposite of LEFT JOIN|
|FULL OUTER JOIN|All records from both tables|When you need complete data picture|

Remember: Always consider your data relationships and what results you actually need when choosing join types!