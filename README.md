# SQL Query 

This document contains SQL queries to solve a variety of tasks related to student, teacher, department, and course information management.

## Tasks and Queries

### 1. Find Students with Specific Blood Groups
**Description:** Retrieve the first name, last name, phone number, and address of students with the blood groups `O+` and `A-`.
```sql
SELECT first_name, last_name, phone_number, address
FROM students
WHERE blood_group IN ('O+', 'A-');
```

### 2. Find Department and Subject Details
**Description:** Retrieve the department name, department code, and subject title for the subject with the subject code `EE201`.
```sql
SELECT department_name, department_code, subject_title
FROM subjects
WHERE subject_code = 'EE201';
```

### 3. Count Students for Each Blood Group with at Least 2 Students
**Description:** Show the count of students for each blood group that has at least 2 students.
```sql
SELECT blood_group, COUNT(*) AS student_count
FROM students
GROUP BY blood_group
HAVING COUNT(*) >= 2;
```

### 4. Find Students Enrolled in Department `CS101`
**Description:** Retrieve the first name, last name, and subject title of students enrolled in the department `CS101`.
```sql
SELECT s.first_name, s.last_name, sub.subject_title
FROM students s
JOIN enrollment e ON s.student_id = e.student_id
JOIN subjects sub ON e.subject_code = sub.subject_code
WHERE sub.department_code = 'CS101';
```

### 5. Total Semester Fee for Computer Science Department
**Description:** Calculate the total semester fee collected for the Computer Science department.
```sql
SELECT SUM(semester_fee) AS total_fee
FROM students
WHERE department_code = 'CS';
```

### 6. Update Semester Fee by 10%
**Description:** Increase the semester fee by 10% for students in the `CS101` department.
```sql
UPDATE students
SET semester_fee = semester_fee * 1.1
WHERE department_code = 'CS101';
```

### 7. Students and Teachers in Course `CS101`
**Description:** Retrieve the first name, last name of students, and the first name, designation of their teachers enrolled in the course `CS101`.
```sql
SELECT s.first_name AS student_first_name, s.last_name AS student_last_name,
       t.first_name AS teacher_first_name, t.designation
FROM students s
JOIN enrollment e ON s.student_id = e.student_id
JOIN subjects sub ON e.subject_code = sub.subject_code
JOIN teachers t ON sub.teacher_id = t.teacher_id
WHERE sub.subject_code = 'CS101';
```

### 8. Students and Teachers from the Same City
**Description:** Show the first name of students and the last name of teachers where both are from the same city.
```sql
SELECT s.first_name AS student_first_name, t.last_name AS teacher_last_name
FROM students s
JOIN teachers t ON s.city = t.city;
```

### 9. Students Enrolled in "Algorithms"
**Description:** Retrieve the first name, last name, email, subject code, and subject title for students enrolled in the subject "Algorithms".
```sql
SELECT s.first_name, s.last_name, s.email, sub.subject_code, sub.subject_title
FROM students s
JOIN enrollment e ON s.student_id = e.student_id
JOIN subjects sub ON e.subject_code = sub.subject_code
WHERE sub.subject_title = 'Algorithms';
```

### 10. Promote Teachers
**Description:** Promote Lecturers to Sr. Lecturers and Sr. Lecturers to Associate Professors.
```sql
UPDATE teachers
SET designation = CASE
    WHEN designation = 'Lecturer' THEN 'Sr. Lecturer'
    WHEN designation = 'Sr. Lecturer' THEN 'Associate Professor'
    ELSE designation
END;
```

### 11. Find Teacher with Second Highest Salary
**Description:** Retrieve the first name and last name of the teacher with the second highest salary.
```sql
SELECT first_name, last_name
FROM teachers
WHERE salary = (SELECT MAX(salary)
                FROM teachers
                WHERE salary < (SELECT MAX(salary) FROM teachers));
```

### 12. Find Students Taught by Teacher with Second Highest Salary
**Description:** List the students taught by the teacher with the second highest salary. Show the teacher's first name, last name, and the students' details.
```sql
SELECT t.first_name AS teacher_first_name, t.last_name AS teacher_last_name,
       s.first_name AS student_first_name, s.last_name AS student_last_name,
       s.city, s.department_code
FROM teachers t
JOIN subjects sub ON t.teacher_id = sub.teacher_id
JOIN enrollment e ON sub.subject_code = e.subject_code
JOIN students s ON e.student_id = s.student_id
WHERE t.salary = (SELECT MAX(salary)
                  FROM teachers
                  WHERE salary < (SELECT MAX(salary) FROM teachers));
