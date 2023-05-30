# randowm_sql_questions

Teacher Table:

Columns: teacher_id, teacher_name, experience, qualification, class_id
Rows: Each row represents a teacher with their corresponding details.
Class Table:

Columns: class_id, class_name
Rows: Each row represents a class with its unique identifier and name.
Student Table:

Columns: student_id, student_name, gender, class_id, roll_number
Rows: Each row represents a student with their unique identifier, name, gender, assigned class, and roll number.
Marks Table:

Columns: mark_id, student_id, subject, marks, marks_obtained, total_marks, exam_type
Rows: Each row represents a record of marks for a student in a specific subject.

Questions:

1.	In the school, one teacher might be teaching more than one class. Write a query to identity how many classes each teacher is taking.

SELECT t.teacher_id, t.teacher_name, COUNT(c.class_id) AS class_count
FROM Teacher t
JOIN Class c ON t.teacher_id = c.teacher_id
GROUP BY t.teacher_id, t.teacher_name;

2.It is interesting for teachers to come across students with n times similar to theirs. John is one of the teachers who finds this fascinating and wants to find out how many students in each class have names similar t ohis. Write a query to help him find this data.

SELECT c.class_id, COUNT(s.student_id) AS similar_names_count
FROM Class c
JOIN Student s ON c.class_id = s.class_id
JOIN Teacher t ON c.teacher_id = t.teacher_id
WHERE t.teacher_name = 'John'
  AND s.student_name LIKE CONCAT('%', t.teacher_name, '%')
GROUP BY c.class_id;

3.
Every class teacher assigns a unique roll number to
their class students based on the alphabetical order o f  t h e i r  n a m e s . C a n y o u help by writing a querythat will assign a roll number to each student ina class?

SELECT class_id, student_id, student_name,
  ROW_NUMBER() OVER (PARTITION BY class_id ORDER BY student_name) AS roll_number
FROM Student
ORDER BY class_id, roll_number;

4. The principal of the school wants to understand the diversity of students in his school. One of the important aspects is gender diversity. Provide a query that computes the male-to-female gender ratio in each class.

SELECT class_id,
  COUNT(CASE WHEN gender = 'Male' THEN 1 END) AS male_count,
  COUNT(CASE WHEN gender = 'Female' THEN 1 END) AS female_count,
  COUNT(*) AS total_students,
  ROUND(CAST(COUNT(CASE WHEN gender = 'Male' THEN 1 END) AS FLOAT) / COUNT(CASE WHEN gender = 'Female' THEN 1 END), 2) AS gender_ratio
FROM Student
GROUP BY class_id;

5. Every school has teachers with different years of experience working in that school. The principal wants to know the average experience of teachers at his school.

SELECT AVG(years_of_experience) AS average_experience
FROM Teachers;

6. At the end of every year, class teachers must provide students with their marks sheet for the whole year. The marks sheet of a student consists of exam (Quarterly, Half-Yearly, etc.) wise marks obtained out of the total marks. Help them by writing a query that gives the student-wise marks sheet.

SELECT s.student_name, m.exam_type, m.marks_obtained, m.total_marks
FROM Students s
JOIN Marks m ON s.student_id = m.student_id;

7. Every teacher has a certain group of favorite students and keeps track of their performance in exams. A teacher approached you to find out the percentages attained by students with ids 1,4,9,16,25 in the "Quarterly" exam. Write aquery to obtain this data for each student.

SELECT student_id, (marks_obtained / total_marks) * 100 AS percentage
FROM Marks
WHERE student_id IN (1, 4, 9, 16, 25) AND exam_type = 'Quarterly';

8. Class teachers assign ranks t o their students based on their marks obtained in each exam. Write a query to assign ranks (continuous) to students ineach class for "Half- Yearly" exams.

WITH ranked_students AS (
  SELECT student_id, marks_obtained,
    ROW_NUMBER() OVER (PARTITION BY class_id ORDER BY marks_obtained DESC) AS rank
  FROM Marks
  WHERE exam_type = 'Half-Yearly'
)
SELECT class_id, student_id, marks_obtained, rank
FROM ranked_students;
![image](https://github.com/BASH-EPIC/randowm_sql_questions/assets/81670865/a7262ee2-e417-4475-8570-f8140f972980)
