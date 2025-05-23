In this lesson, we will explore B+ Trees, a fundamental data structure extensively utilized in DBMS, especially for table indexes. Building upon our previous discussion of Hash Tables, we will delve into the design considerations, implementations, and optimizations of B+ Trees. By understanding the role and functioning of B+ Trees in DBMS, we can gain insights into their significance in facilitating efficient data storage and retrieval.

##### Indexes vs Filters
An **index** data structure of a subset of a table's attributes that are organized and/or sorted to the location of specific tuples using those attributes. :LiArrowBigRight: Example: B+Tree 

A **filter** is a data structure that answers set membership queries; it tells you whether a record (likely) exists for a key but **not** where it's located :LiArrowBigRight: Example: Bloom Filter

### B+Tree

A B+Tree is a self-balancing, ordered $M$-way tree for searches, sequential access, insertions, and deletions in $O(log_m n)$ where $M$ is the tree fanout (The maximum number of children a node can have) with the following properties:
- It is perfectly balanced (i.e., every leaf node is at the same depth in the tree)

- Every node other than the root is at least half-full $\frac{M}{2} - 1 \leq keys \leq M-1$

- Every inner node with ***k*** keys has ***k + 1***  non-null children. 

Some real-world implementation relax these properties, but we will ignore that for now.

![[Pasted image 20250508205459.png | center]]

Take a look at this example. At first glance, it may seem a bit complicated, but it really follows a few simple principles:

- **Sorted Keys at Each Level**: All index keys are sorted at each level—typically in ascending order.
    
- **Sibling Links**: Nodes at the same level are connected via pointers, making it a hybrid between a tree and a linked list.
    
- **Structure of Inner Nodes**: Internal (root/inner) nodes store keys and child pointers in the format:  
    `pointer | key | pointer | key | ... | pointer`
    
- **Structure of Leaf Nodes**: Leaf nodes contain only key-value pairs and also maintain links to their neighboring leaves.

- **Null Keys**: Store either at the beginning or the end of the leaf node

![[Pasted image 20250508210824.png|center]]

The leaf nodes would typically be something like this 
- Meta Data like current level, number of free slots, `prev` and `next` leaf node pointers.

- Key-Value pair arrays.

##### What the heck is these values?

There are two approaches for leaf node values:

- **Record IDs:** leaf nodes that record ids refer to a pointer to the location of the tuple. (Most Common)
    
- **Tuple data:** leaf nodes that have tuple data store the actual contents of the tuple in each node.

##### B-Tree vs B+Tree 

 **1. Data Storage**

- **B-Tree:** 
	- Stores **both keys and values (data records) in all nodes**—internal (non-leaf) and leaf nodes.
	
	- More **space-efficient** in terms of key storage since each key appears only once in the tree. However, internal nodes may consume more space because they store actual data.
    
- **B+Tree:**
	- **Only leaf nodes store values** while **internal nodes store only keys** (as guides for search) and **pointers** to child nodes.
    

**2. Search & Queries**

- **B-Tree:** 
	- Can find a key **early** in an internal node but **random searches may be slower** due to data being scattered across all levels.
    
- **B+Tree:** 
	- **All searches must traverse to a leaf node**, ensuring a **consistent search time**. Better for **range queries** since leaf nodes are **linked sequentially**, allowing efficient scans.
    

**3. Best For**

- **B-Tree:** Good for **in-memory databases** or cases where early termination in searches is beneficial.
    
- **B+Tree:** **Dominates in disk-based storage systems**

### Insertion

To insert a new entry into a B+Tree, one must traverse down the tree and use the inner nodes to figure out which leaf node to insert the key into.

1. Find correct leaf L.
    
2. Add new entry into L in sorted order:
    
    - If L has enough space, the operation is done.
        
    - Otherwise, split L into two nodes L and L2. Redistribute entries evenly and copy up the middle key. Insert index entry pointing to L2 into the parent of L.
        
3. To split an inner node, redistribute entries evenly, but push up the middle key.

![[9tbi2x 1.gif|center|600]]

In this Example:
1. **Locate the Leaf Node:**
    - The key `6` is less than `12`, so it belongs in the left leaf node `[5 | 9 | 10]`.
        
2. **Check for Overflow:**
    - The leaf node is **full** (already contains `5, 9, 10`). So a **split** is required.
        
3. **Split the Leaf Node:**
    - New left node: `[5 | 6]`
    - New right node: `[9 | 10]`
    - The **middle key (`9`)** is promoted to the parent.
        
4. **Update the Parent Node:**
    - Original parent: `[ <12 ]`
    - After promotion: `[ <9 | <12 ]`
    - The tree now has **two pointers** from the parent:
        - One to `[5 | 6]` (keys < `9`)
        - One to `[9 | 10]` (keys < `12`)

**Say we have this scenario:**

Inserting key `17` into a B+Tree where:

1. The target leaf node is **full**, requiring a split.
    
2. The parent node is **also full**, forcing another split.
    
3. The parent is the **root**, triggering a **tree height increase**.

Now we have to 
1. Insert `17` into the correct leaf
2. Split the leaf node.
3. Try to update the Parent, but it's full.
4. After splitting the parent we find that it was the root.
5. Create a new root.
6. Increase Tree Height.

![[9tbjib.gif|center|600]]


### Deletion 

Whereas in inserts we occasionally had to split leaves when the tree got too full, if a deletion causes a tree to be less than half-full, we must merge to re-balance the tree.

1. Find correct leaf L.
2. Remove the entry:
    - If L is at least half full, the operation is done.
    - Otherwise, you can try to redistribute, borrowing from sibling.
    - If redistribution fails, merge L and sibling.
        
3. If merge occurred, you must delete entry in parent pointing to L.
4. Also have to do this recursively up the tree until every thing is balanced.


### Composite Index

A composite index (or compound index) is a database index on **multiple columns** (attributes) that are combined into a single key.

- **Example:** `CREATE INDEX idx_name ON table (a, b DESC, c NULLS FIRST);`
    
- The **column order matters** (leftmost columns have highest priority for searches).

In B+Tree keys are ordered by the **leftmost column first**, then the next column, and so on.
    
- Example: For `(a, b, c)`, sorting is by `a` → `b` → `c`.


Composite indexes support queries with:

- **Prefix Matching:** Queries filtering on **leftmost columns** can use the index efficiently.
    
```sql

SELECT * FROM table WHERE a = 1;             -- Uses index
SELECT * FROM table WHERE a = 1 AND b = 2;   -- Uses index
SELECT * FROM table WHERE b = 2;             -- **Does not use index** (missing `a`)
    ```
- **Partial Key Lookup:**
    - If only the first key (`a`) is specified, the index is scanned for all matching `a` values (treating `b, c` as wildcards).
    - If a middle key is missing (e.g., `a` and `c` but not `b`), the index is **partially used** (only up to `a`).


###### 1. Prefix Matching

![[9tbw14.gif|center|600]]

###### 2. Partial Key Lookup

![[9tbwcr.gif|center|600]]

###### Missing Leftmost Key (`b` without `a`)

This is a bit more complicated and inefficient because we literally have to scan all branches  as we don't know which leaf node to go to cause we don't know the first key in the index.

**Oracle** call these *skip scans*.

![[Pasted image 20250508231421.png|center|600]]

##### Performance Considerations
- **Equality vs. Range Queries:**
	- **Best for equality filters (`=`)** on leftmost columns.
	- Range queries (`>`, `<`, `BETWEEN`) on later columns **may not use the index fully**.

- **OR Conditions:**
	- `OR` breaks index usage (e.g., `WHERE a = 1 OR b = 2` → **full scan**).

- **Column Order Matters !!**
	- Place **high-selectivity columns** (most distinct values) first.

- **Avoid Over-indexing:** Too many indexes slow down writes.

### Duplicate Keys

There are two approaches to duplicate keys in a B+Tree.

1. **Append Record ID:**
	-  Append record IDs as part of the key. Since each tuple’s record ID is unique, this will ensure that all the keys are identifiable.

Here Although it may seem that there's multiple `6` keys in the end but actually each of them is composed of `<key,recordID>` which is unique every time.

>Recall `RecordID` is where the tuple is stored in the disk i.e. which page and which slot.  

![[9tbz0d.gif|center|600]]

2. **Overflow Leaf Nodes:**
	- Allow leaf nodes to spill into overflow nodes that contain the duplicate keys. Although no redundant information is stored, this approach is more complex to maintain and modify.

	- This Could lead to a big skew, where we have to scan sequentially again, Also it breaks the siblings somehow. 

![[9tbzbu.gif|center|600]]


### B+Tree Design Choices

##### Node Size

Depending on the storage medium, we may prefer larger or smaller node sizes. For example, nodes stored on hard drives are usually on the order of (HDD=>1MB, SSD=>10KB) in size to reduce the number of seeks needed to find data and amortize the expensive disk read over a large chunk of data.

While in-memory databases may use page sizes as small as 512 B in order to fit the entire page into the CPU cache as well as to decrease data fragmentation.

This choice can also be dependent on the type of workload, as point queries would prefer as small a page as possible to reduce the amount of unnecessary extra info loaded, while a large sequential scan might prefer large pages to reduce the number of fetches it needs to do.

##### Merge Threshold

While B+Trees have a rule about merging underflowed nodes after a delete, sometimes it may be beneficial to temporarily violate the rule and delay a merge operation to reduce the amount of reorganization. It may also be better to let smaller nodes exist and then periodically rebuild entire tree

For instance, eager merging could lead to thrashing, where a lot of successive delete and insert operations lead to constant splits and merges.
It also allows for batched merging where multiple merge operations happen all at once, reducing the amount of time that expensive write latches have to be taken on the tree.

This is why PostgreSQL calls their B+Tree a ***non balanced*** B+Tree `nbtree`.

##### Variable-Length Keys

Currently, we have only discussed B+Trees with fixed-length keys. However, we may also want to support variable-length keys, such as the case where a small subset of large keys leads to a lot of wasted space. There are several approaches to this:

**Pointers**

Instead of storing the keys directly, we could just store a pointer to the key. Due to the inefficiency of having to chase a pointer for each key, the only place that uses this method in production is embedded devices, where its tiny registers and cache may benefit from such space savings. This is also called `T-Trees` (in-memory DBMSs)

**Variable Length Nodes**

We could also still store the keys like normal and allow for variable-length nodes. This is infeasible and largely not used due to the significant memory management overhead of dealing with variable-length nodes.

**Padding**

Instead of varying the key size, we could set each key’s size to the size of the maximum key and pad out all the shorter keys. In most cases this is a massive waste of memory, so you don’t see this used by anyone either.

**Key Map/Indirection**

The method that nearly everyone uses is replacing the keys with an index to the key-value pair in a separate dictionary. This offers significant space savings and potentially shortcuts point queries (since the key-value pair the index points to is the exact same as the one pointed to by leaf nodes). Due to the small size of the dictionary index value, there is enough space to place a prefix of each key alongside the index, potentially allowing some index searching and leaf scanning to not even have to chase the pointer (if the prefix is at all different from the search key).

### Intra-Node Search

Once a node is reached, we must search within it—either to locate the next child in an internal node or to find a key in a leaf node. While straightforward, several strategies exist, each with tradeoffs:

**Linear**

The simplest approach: scan every key in the node until the target is found.
- **Pros**: Easy to implement; fast insertions and deletions (no need to maintain order).

- **Cons**: Inefficient for large nodes (`O(n)` search time).

- **Optimization**: Can be accelerated with **SIMD** (Single Instruction, Multiple Data), which performs the same operation across multiple data points simultaneously—common in GPUs and vector processors.

> *SIMD is Single instruction multiple data, Which as the name suggests executes one instruction on multiple data elements at the same time, Its mainly used in vector processors, GPUs, and multimedia apps 
> 
> The classic sequential processing is called SISD Single Instruction Single Data*

![[Pasted image 20250509155519.png|center]]


**Binary**

Maintains keys in sorted order, allowing binary search.

- **Pros**: Efficient lookups (`O(log n)` time).
    
- **Cons**: Insertions and deletions are more complex due to sorting overhead.


**Interpolation**

Uses node metadata (e.g., min, max, average) to estimate the key’s position.

- **Pros**: Potentially faster than binary search when keys are uniformly distributed.
    
- **Cons**: Limited to certain key types (e.g., integers); rarely used in practice outside of academic databases

### Optimization

##### Prefix Compression

Sorted keys in the same leaf node are likely to have the same prefix. Instead of storing the entire key each time, extract common prefix and store only unique suffix for each key

![[Pasted image 20250509155936.png|center]]

##### Deduplication 

Non-unique indexes can end up storing multiple copies of the same key in leaf nodes. The leaf node can store the key once and then maintain a "posting list" of tuples with that key (similar to what we discussed for hash tables).

![[Pasted image 20250509160014.png | center]]

##### Suffix Truncation

The keys in the inner nodes are only used to **direct traffic**. So we don't need the entire key we can only store a minimum prefix that is needed to correctly route probes into the index

![[9teerj.gif|center]]

##### Pointer Swizzling 

Because each node of a B+Tree is stored in a page from the buffer pool, each time we load a new page we need to fetch it from the buffer pool, requiring latching and lookups. To skip this step entirely, we could store the actual raw pointers in place of the page IDs (known as ”swizzling”), preventing a buffer pool fetch entirely. Rather than manually fetching the entire tree and placing the pointers manually, we can simply store the resulting pointer from a page lookup when traversing the index normally. Note that we must track which pointers are swizzled and deswizzle them back to page ids when the page they point to is unpinned and victimized.


##### Bulk Inserts

When a B+Tree is initially built, having to insert each key the usual way would lead to constant split operations thus a lot of overhead.

So since we already give leaves sibling pointers, initial insertion of data is much more efficient if we construct a sorted linked list of leaf nodes and then easily build the index from the bottom up using the first key from each leaf node. Note that depending on our context we may wish to pack the leaves as tightly as possible to save space or leave space in each leaf to allow for more inserts before a split is necessary.


