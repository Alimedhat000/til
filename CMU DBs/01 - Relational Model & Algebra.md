## **What is a Database?**

Organized collection of inter-related data that models some aspect of the real-world.

Databases are the core component of the most computer applications.

### **Flat File DBs**
A very basic way to implement a database is by storing data in flat files (e.g., CSV, JSON, YAML).

- **Structure**:
    - Each entity (e.g., `artists`, `albums`) gets its own file.
    - The application must parse the file every time it reads or updates data.

![[Pasted image 20250429142306.png]]

- **Pros for Small-Scale Use**:
    - Works fine when the dataset is small and operations are simple.
#### **Problems with This Approach**

1. **Data Integrity Issues**
    - **Consistency**: How do we ensure the artist name matches across all album entries?
    - **Validation**: What if someone overwrites an album’s year with an invalid string (e.g., `"nineteen ninety-five"`)?
    - **Complex Relationships**: How to handle albums with multiple artists?
    - **Referential Integrity**: What happens if an artist is deleted but their albums still exist? (Orphaned records)

2. **Performance Problems**    
    - Parsing files repeatedly is slow for large datasets.        
    - No indexing → full scans required for every query.

3. **Concurrency Challenges**
    - File locks or race conditions if multiple processes try to write at once.

4. **Scalability Limitations**    
    - Flat files don’t handle large volumes of data efficiently.

And here comes Database Management Systems

### **Database Management Systems**

DBSM is a software that allows apps to store and analyze information in a db
- **Examples**: MySQL, PostgreSQL, MongoDB, Redis.

- A DBMS operates based on a **data model**, which dictates how data is organized and interacted with.

**Core Functions**:
1. **Data Definition**: Define schemas and structures (e.g., tables, collections).
2. **Data Manipulation**: Insert, query, update, and delete data.
3. **Administration**: Manage security, backups, and user access.

4. **Optimization**: Ensure performance (indexing, query planning).    


### **Data Models**

**Definition**:  
A **data model** is a framework for describing:
- The **structure** of data (e.g., tables, documents).
- **Relationships** between data (e.g., foreign keys, graph edges).
- **Constraints** (e.g., "age must be > 0").

**Schema**:
- A **schema** is a _specific instance_ of a data model, defining how data is organized in a particular database.
- Example: A `users` table schema in SQL specifies columns (`id`, `name`, `email`) and their data types.

**Common Data Models**

| **Model**                            | **Description**                                                                 | **DBMS Examples**   |
| ------------------------------------ | ------------------------------------------------------------------------------- | ------------------- |
| **Relational**                       | Tables with rows/columns; SQL.                                                  | MySQL, PostgreSQL   |
| **Key/Value**                        | Simple pairs (key → value).                                                     | Redis               |
| **Document/JSON<br>/XML/Object**     | JSON-like documents.                                                            | MongoDB             |
| **Graph**                            | Nodes/edges for relationships.                                                  | Neo4j, ArangoDB     |
| **Wide-Column**                      | Flexible columns per row.                                                       | Cassandra, ScyllaDB |
| **Array(Vector,<br>Matrix, Tensor)** | Multi-dimensional arrays (tensors);<br>optimized for numerical/scientific data. | Rasdaman, SciDB,    |

### **Relational Model**

The **relational model** organizes data into _relations_ (tables) to abstract physical storage details

#### **Key Principles**
1. **Structure**:
    - Data is stored in _relations_ (tables) with rows (tuples) and columns (attributes).
    - Schema defines relations _independently_ of physical storage (e.g., files, pages).
2. **Integrity**:
    - Constraints (e.g., primary keys, foreign keys) ensure data validity.
    - Example: `album.artist_id` must reference a valid `artist.id`.
3. **Manipulation**:
    - SQL provides a declarative interface (e.g., `SELECT`, `INSERT`).
    - DBMS translates queries into efficient execution plans.
4. **Data Independence**:
    - **Logical Independence**: Apps are shielded from changes to the logical schema (e.g., adding a column).
    - **Physical Independence**: Apps are unaffected by storage changes (e.g., switching from HDD to SSD).
![[Pasted image 20250429160226.png|center|500]]

From down up:
1. **Physical Schema**:
	- How data is stored (files, pages)
2. **Logical Schema** :
	- Defines the _entire database structure_ (tables, relationships, constraints) using SQL-like definitions.
3. **External Schema** :
	-  User-facing _applications_ interact with customized views of the data (e.g., a "Customer Dashboard" view).

#### **What is a Relation?**

A **relation** is a mathematical set-based representation of a database table, characterized by:
- **Unordered** elements (no inherent row order).
- **Unique tuples** (no duplicate rows).
- **Attributes** (columns) with distinct names and domains (data types).

**Example**:

| `artist_id` (INT) | `name` (TEXT) | `genre` (TEXT) |
| ----------------- | ------------- | -------------- |
| 1                 | The Beatles   | Rock           |
| 2                 | Miles Davis   | Jazz           |

- **Relation Name**: `artists`    
- **Attributes**: `artist_id`, `name`, `genre`
- **Tuples**: Each row (e.g., `(1, "The Beatles", "Rock")`).
#### **Tuples and Domains**

- **Tuple**: A single row in a relation, representing an entity instance.
    - Example: `(2, "Miles Davis", "Jazz")` is a tuple in the `artists` relation.

- **Domain**: The set of allowable values for an attribute (e.g., `genre` could be restricted to `{"Rock", "Jazz", "Pop"}`).

#### **Primary Keys**

A **primary key (PK)** uniquely identifies a tuple in a relation.

**Why PKs Matter**:
- Enable efficient lookups (e.g., `SELECT * FROM artists WHERE artist_id = 2`).
- Enforce entity integrity (no duplicate artists).

Some DBMs automatically create as internal primary key of a table doesn't define one via ***Identity columns***:
- **IDENTITY** (SQL Standard)
- **SEQUENCE** (PostgreSQL)
- **AUTO_INCREMENT** (MySQL)

#### **Foreign Keys**

A **foreign key (FK)** links attributes across relations, enforcing referential integrity.

```SQL
CREATE TABLE albums (
  album_id INT PRIMARY KEY,
  title TEXT,
  artist_id INT REFERENCES artists(artist_id)  -- FK to artists.artist_id
);
```

#### **Constrains**

User-defined rules that ensure data validity.

```SQL
CREATE TABLE Artists (
	name VARCHAR NOT NULL,
	year INT,
	country CHAR(60),
	CHECK(year > 1900)
)
```

### Data Manipulation Languages (DML)

The API that a DBMS exposes to applications to store and retrieve information from a database. 
1. Procedural (Relational Algerba)
	- The query specifies **how** to retrieve the result by defining a step-by-step _procedure_ (sequence of operations).
	- Works with **sets** (unique rows) or **bags** (duplicate-allowed multisets).
	- Used internally by DBMS query optimizers to generate execution plans.
	
2. Non-Procedural (Relational Calculus)
	- The query specifies **what** data is needed (declarative), _not_ how to retrieve it.
	- The DBMS determines the optimal execution strategy.

### Relational Algebra
- Fundamental operations to retrieve and manipulate tuples in a relation.
- Its defined as 7 main operators where each one takes one or more relations as its inputs and outputs a new relation.
- We can chain operators together to create more complex operations.

#### 1. SELECT $\sigma$ 

Choose a subset of the tuples from a relation that satisfies a selection predicate.
- Predicate acts as a filter to retain only tuples that fulfill its requirements
- Can combine multiple predicates using conjunctions/ disjunctions.

**Syntax**: $\omega_{predicate}(R)$ 
**Example:** $\omega_{id = 10}(R)$ ,  $\omega_{id = 10\space \wedge \space age \lt 30}(R)$ 

In SQL its just a `WHERE` clause:
```sql
SELECT * FROM R
WHERE id = 10 AND age < 30;
```

#### 2. PROJECTION $\Pi$ 

Generate a relation with tuples that contains only the specified attributes.
- Rearrange attributes’ ordering.
- Remove unwanted attributes.
- Create derived attribute

**Syntax**: $\Pi_{A1,\space A2,\space\dots,\space An}(R)$ 
**Example:** $\Pi_{age-5}(R)$  

In SQL its just a `SELECT` operator:
```sql
SELECT age - 5 FROM R;
```

#### 3. Union $\cup$  

Generate a relation that contains all tuples that appear in either only one or both input relations.

**Syntax**: $R \cup S$   

```sql
(SELECT * FROM R) 
UNION
(SELECT * FROM S);
```


#### 4. Intersection $\cap$  

Generate a relation that contains only the tuples that appear in both of the input relations.

**Syntax**: $R \cap S$   

```sql
(SELECT * FROM R) 
INTERSECT
(SELECT * FROM S);
```

#### 5. Difference $-$  

Generate a relation that contains only the tuples that appear in the first and not the second of the input relations.

**Syntax**: $R - S$   

```sql
(SELECT * FROM R) 
EXCEPT
(SELECT * FROM S);
```

#### 6. PRODUCT $\times$  

Generate a relation that contains all possible combinations of tuples from the input relations.

**Syntax**: $R \times S$   

```sql
SELECT * FROM R CROSS JOIN S; 
```

#### 7. JOIN $\bowtie$  

Generate a relation that contains all tuples that are a combination of two tuples (one from each input relation) with a common value(s) for one or more attributes.

**Syntax**: $R \bowtie S$   

```sql
SELECT * FROM R JOIN S; 
```
