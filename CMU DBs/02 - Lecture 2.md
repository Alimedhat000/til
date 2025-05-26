### Aggregates

Functions that return a single value from a bag of tuples:

→AVG(col)→ Return the average col value. 
→MIN(col)→ Return minimum col value. 
→MAX(col)→ Return maximum col value. 
→SUM(col)→ Return sum of values in col. 
→COUNT(col)→ Return # of values for col.

- When using any of these you cant specify other columns outside of an aggregate as there output will be undefined.
![[Pasted image 20250430142701.png]]

But we can use `GROUP BY` to split the tuples into subsets then calculate the aggregate against each subset.

![[Pasted image 20250430142821.png]]
![[Pasted image 20250430142828.png]]

So non-aggregated values in SELECT output clause must appear in GROUP BY clause.
#### `HAVING` 

Filters results based on aggregation computation. Like a WHERE clause for a GROUP BY

Here this wont work because `avg_gpa` isn't computed when the `AND` is executed.
![[Pasted image 20250430143250.png]]

This would work on some systems but will break on some others.

![[Pasted image 20250430143411.png]]

This will never break!
![[Pasted image 20250430143420.png]]


### Date & Time

syntax varies wildly

### Window functions

Performs a calculation across a set of tuples that are related to the current tuple, without collapsing them into a single output tuple, to support running totals, ranks, and moving averages. 

```SQL
SELECT FUNC-NAME(...) OVER (...)
```

```SQL
SELECT *, ROW_NUMBER() OVER () AS row_num  FROM enrolled
```

The OVER keyword specifies how to group together tuples when computing the window function. Use PARTITION BY to specify group

```SQL
SELECT cid, sid,    
	ROW_NUMBER() OVER (PARTITION BY cid)
	FROM enrolled
	ORDER BY cid
```

