### Problem 1 

Given the example XML data below, explain how you would provide a list of every unique payment date and a count of how many loans were processed on that date. You can choose any programming language for the solution or simply describe your approach using pseudo-code.


```xml
<LOANS>
    <LOAN LoanNumber="PP58771" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP62633" NextPrincipalPaymentDate="8/14/2021"></LOAN>
    <LOAN LoanNumber="PP92431" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP60078" NextPrincipalPaymentDate="8/14/2021"></LOAN>
    <LOAN LoanNumber="PP76424" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP84213" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP88699" NextPrincipalPaymentDate="8/12/2021"></LOAN>
    <LOAN LoanNumber="PP74327" NextPrincipalPaymentDate="8/12/2021"></LOAN>
    <LOAN LoanNumber="PP74328" NextPrincipalPaymentDate="8/12/2021"></LOAN>
    <LOAN LoanNumber="PP95239" NextPrincipalPaymentDate="8/13/2021"></LOAN>
</LOANS>
```

### Soultion 1

```python

from xml.etree import ElementTree as ET

xml_data = """
<LOANS>
    <LOAN LoanNumber="PP58771" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP62633" NextPrincipalPaymentDate="8/14/2021"></LOAN>
    <LOAN LoanNumber="PP92431" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP60078" NextPrincipalPaymentDate="8/14/2021"></LOAN>
    <LOAN LoanNumber="PP76424" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP84213" NextPrincipalPaymentDate="8/13/2021"></LOAN>
    <LOAN LoanNumber="PP88699" NextPrincipalPaymentDate="8/12/2021"></LOAN>
    <LOAN LoanNumber="PP74327" NextPrincipalPaymentDate="8/12/2021"></LOAN>
    <LOAN LoanNumber="PP74328" NextPrincipalPaymentDate="8/12/2021"></LOAN>
    <LOAN LoanNumber="PP95239" NextPrincipalPaymentDate="8/13/2021"></LOAN>
</LOANS>
"""

root = ET.fromstring(xml_data)

date_counts = {}

for loan in root:
    date = loan.get('NextPrincipalPaymentDate')
    if date in date_counts:
        date_counts[date] += 1
    else:
        date_counts[date] = 1

for date, count in sorted(date_counts.items()):
    print(f"Date: {date}, Count: {count}")

```

### Problem 2
Given the tables below, write an SQL query to retrieve the full name and birth date of alumni who scored above 16 on their calculus exam.

**Tables:**

1. **dbo.alumni**

| student_id | first_name | last_name | birth_date | Faculty         |
|------------|------------|-----------|------------|-----------------|
| 347        | Daniela    | Lopez     | 1991-04-26 | Medical School  |
| 348        | Robert     | Fischer   | 1991-03-09 | Mathematics     |

2. **dbo.evaluation**

| student_id | class_id | exam_date  | Grade |
|------------|----------|------------|-------|
| 347        | 74       | 2015-06-19 | 16    |
| 347        | 87       | 2015-06-06 | 20    |
| 348        | 74       | 2015-06-19 | 13    |
| 348        | 46       | 2015-06-06 | 18    |

3. **dbo.curricula**

| class_id | class_name | professor_id | Semester   |
|----------|------------|--------------|------------|
| 74       | Algebra    | 435          | 2015_Fall  |
| 87       | Calculus   | 532          | 2015_Fall  |
| 46       | Statistics | 625          | 2016_Spring|

### Solution 2

To solve this problem, we can join the `dbo.alumni`, `dbo.evaluation`, and `dbo.curricula` tables, filtering for calculus exams where the grade is above 16. Here's how the SQL query might look:

```sql
SELECT
    a.first_name + ' ' + a.last_name AS full_name,
    a.birth_date
FROM dbo.alumni a
JOIN dbo.evaluation e ON a.student_id = e.student_id
JOIN dbo.curricula c ON e.class_id = c.class_id
WHERE c.class_name = 'Calculus'
AND e.Grade > 16;
```


### Problem 3

Given the tables below, there's a need to fix a SQL query that retrieves the first name, last name, class name, semester offered, and exam date for Daniela Lopez for every class in the curricula. If she did not take an exam, the date should display “Not Taken”, and her first and last names should still be included in the result (avoid NULLs).

**Tables:**

1. **dbo.alumni**

| student_id | first_name | last_name | birth_date | Faculty        |
|------------|------------|-----------|------------|----------------|
| 347        | Daniela    | Lopez     | 1991-04-26 | Medical School |
| 348        | Robert     | Fischer   | 1991-03-09 | Mathematics    |

2. **dbo.evaluation**

| student_id | class_id | exam_date  | Grade |
|------------|----------|------------|-------|
| 347        | 74       | 2015-06-19 | 16    |
| 347        | 87       | 2015-06-06 | 20    |
| 348        | 74       | 2015-06-19 | 13    |
| 348        | 46       | 2015-06-06 | 18    |

3. **dbo.curricula**

| class_id | class_name | professor_id | Semester    |
|----------|------------|--------------|-------------|
| 74       | Algebra    | 435          | 2015_Fall   |
| 87       | Calculus   | 532          | 2015_Fall   |
| 46       | Statistics | 625          | 2016_Spring |

**Given SQL:**

```sql
SELECT
T.first_name,
T.last_name,
C.class_name,
C.semester,
T.exam_date
FROM
curricula c
LEFT JOIN (
SELECT
e.exam_date,
e.class_id
FROM
alumni a
JOIN evaluation AS e ON a.student_id=e.student_id
WHERE a.student_id = 347) T
ON T.class_id = c.class_id
```

### Solution 3


```sql
SELECT
    a.first_name,
    a.last_name,
    c.class_name,
    c.Semester,
    COALESCE(T.exam_date, 'Not Taken') AS exam_date
FROM dbo.curricula c
LEFT JOIN (
    SELECT
        e.exam_date,
        e.class_id,
        a.first_name,
        a.last_name
    FROM dbo.alumni a
    LEFT JOIN dbo.evaluation e ON a.student_id = e.student_id AND a.student_id = 347
) T ON c.class_id = T.class_id
CROSS JOIN (SELECT DISTINCT first_name, last_name FROM dbo.alumni WHERE student_id = 347) a
```
