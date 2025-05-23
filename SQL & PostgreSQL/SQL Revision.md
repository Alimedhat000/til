## Querying tables

1. **Select All from a Table**
```sql
SELECT * FROM <table_name>;
```

2. **Select specific columns from a Table**
```sql
SELECT <column1>, <column2> FROM <table_name>;
```

---

## Queries with Constraints (Pt. 1 & 2)

3. **WHERE clause**
```sql
SELECT * FROM <table_name> WHERE <condition>;
```

4. **Comparison operators**
```sql
=, !=, <, <=, >, >=
```

5. **BETWEEN, IN, LIKE**
```sql
SELECT * FROM <table_name> WHERE <column> BETWEEN val1 AND val2;
SELECT * FROM <table_name> WHERE <column> IN (val1, val2, ...);
SELECT * FROM <table_name> WHERE <column> LIKE 'pattern%';
```

---

## Filtering and Sorting Query Results

6. **ORDER BY**
```sql
SELECT * FROM <table_name> ORDER BY <column> ASC|DESC;
```

7. **LIMIT** & **OFFSET**
```sql
SELECT * FROM <table_name> LIMIT <number> OFFSET <number> ;
```

---

## Multi-table Queries with JOINs

8. **INNER JOIN**
```sql
SELECT * FROM table1
JOIN table2 ON table1.id = table2.id;
```

9. **LEFT JOIN**
```sql
SELECT * FROM table1
LEFT JOIN table2 ON table1.id = table2.id;
```

10. **RIGHT JOIN**
```sql
SELECT * FROM table1
RIGHT JOIN table2 ON table1.id = table2.id;
```

---

## OUTER JOINs

11. **FULL OUTER JOIN**
```sql
SELECT * FROM table1
FULL OUTER JOIN table2 ON table1.id = table2.id;
```

---

## NULLs

12. **Checking for NULL**
```sql
SELECT * FROM <table_name> WHERE <column> IS NULL;
SELECT * FROM <table_name> WHERE <column> IS NOT NULL;
```

---

## Expressions in Queries

13. **Using expressions**
```sql
SELECT <column1> + <column2> AS total FROM <table_name>;
```

14. **Using CASE**
```sql
SELECT name,
CASE 
  WHEN score >= 90 THEN 'A'
  WHEN score >= 80 THEN 'B'
  ELSE 'C'
END AS grade
FROM students;
```

---

## Aggregates (Pt. 1 & 2)

15. **Aggregate functions**
```sql
SELECT COUNT(*), AVG(column), MAX(column), MIN(column), SUM(column) FROM <table_name>;
```

16. **GROUP BY**
```sql
SELECT column, COUNT(*) FROM <table_name> GROUP BY column;
```

17. **HAVING clause**
```sql
SELECT column, COUNT(*) FROM <table_name> GROUP BY column HAVING COUNT(*) > 1;
```

---

## Order of Execution

18. **SQL query execution order**
```text
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

---

## Inserting Rows

19. **INSERT INTO**
```sql
INSERT INTO <table_name> (<column1>, <column2>)
VALUES (value1, value2);
```

---

## Updating Rows

20. **UPDATE**
```sql
UPDATE <table_name>
SET <column1> = value1, <column2> = value2
WHERE <condition>;
```

---

## Deleting Rows

21. **DELETE**
```sql
DELETE FROM <table_name> WHERE <condition>;
```

---

## Creating Tables

22. **CREATE TABLE**
```sql
CREATE TABLE <table_name> (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  age INT
);
```

---

## Altering Tables

23. **ALTER TABLE**
```sql
ALTER TABLE <table_name> ADD <column_name> <datatype>;
ALTER TABLE <table_name> DROP COLUMN <column_name>;
ALTER TABLE <table_name> RENAME TO <new_name>;
```

---

## Dropping Tables

24. **DROP TABLE**
```sql
DROP TABLE <table_name>;
```

---
