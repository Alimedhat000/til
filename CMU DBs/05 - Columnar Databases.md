### Introduction

In the previous lesson, we focused on disk-oriented DBMS and how tuples are stored within pages using different approaches such as the Tuple Oriented Approach, Slotted Pages Approach, and the Log-Structured Approach. In this lesson, we will explore alternative methods for representing data to meet the needs of different workloads.

### Database Workloads

First of all, what do mean by database workload?

In the context of database systems, workload refers to the set of activities and tasks performed by the database system. It encompasses the queries, transactions, data updates, and other operations that are executed against the database. The workload can include a variety of factors such as the type and complexity of queries, the frequency and volume of data access, the number of concurrent users, and the overall system demand.

We categorize the workloads into 3 main categories:

##### 1. OLTP: On-Line Transaction Processing

An OLTP (Online Transaction Processing) workload refers to a type of database usage that involves quick and short operations. These operations typically consist of simple queries that work with individual pieces of data at a time.

In an OLTP workload, there are usually more updates or changes (writes) to the data than there are requests to retrieve the data (reads).

An example of an OLTP workload is an online storefront like Amazon. Users can perform actions such as adding items to their cart and making purchases.

OLTP workloads are commonly the **first type** of applications that developers build.

##### 2. OLAP: On-Line Analytical Processing

An OLAP (Online Analytical Processing) workload refers to a type of database usage that involves performing lengthy and complex queries. These queries typically involve reading and analyzing large amounts of data from the database.

Here the database system focuses on analyzing and deriving new insights from the existing data collected through OLTP (Online Transaction Processing) operations.

To illustrate this, consider an example where Amazon wants to determine the most purchased item in Saudi Arabia on a sunny day. This analysis requires processing a significant amount of data and deriving meaningful information from it.

OLAP workloads are typically executed on the data that has been collected and stored from OLTP applications.

##### 3. HTAP: Hybrid Transaction + Analytical Processing

HTAP (Hybrid Transactional/Analytical Processing) is a relatively recent type of workload. HTAP aims to combine the capabilities of both OLTP and OLAP workloads within the same database system.

![[Pasted image 20250502162102.png|center]]

##### Example

Consider this is the schema for the Wikipedia database:

![[Pasted image 20250502162603.png]]

And these are the example queries as OLTP and OLAP workload.

![[Pasted image 20250502162618.png]]

On the left, we have everyday queries used in regular projects (OLTP). They are fast and simple focusing on small amount of data that is related to a single entity in the database.

But on the right side, we have an example of complex queries used for analyzing big databases (OLAP workload). These queries provide insights by analyzing and summarizing data across multiple entities.

### Storage Models

A DBMS's *storage model* specifies how it physically organizes tuples on disk and in memory. This can have different performance characteristics based on the target workload (OLTP vs OLAP). Influences the design choices of the rest of the DBMS.

##### 1. **NSM**, N-ary Storage Model. (AKA **Row-based** Storage)

In the n-ary storage model, the DBMS stores all of the attributes for a single tuple contiguously on a single page. This approach is ideal for **OLTP** workloads where requests are insert-heavy and transactions tend to operate only as an individual entity. It is ideal because it takes only one fetch to be able to get all of the attributes for a single tuple.

A disk-oriented NSM system stores a tuple's fixed-length and variable length attribute contagiously in a single slotted page 

![[9socrn.gif | center]]


###### NSM: OLTP Example

![[Pasted image 20250502193240.png | center]]

Ignore how the index lookup works for now (we’ll cover that later). Conceptually, some abstract index data structure returns the **record ID** for the given `userName`.

From there:
1. The **page directory** tells us which page contains the tuple with that **ID**.
2. Read that particular page without loading unneeded pages to memory.
3. The **page header** tells us the slot location.
4. We then jump to that slot and read the **entire tuple** into memory.

The same goes for insertion.

![[Pasted image 20250502194327.png | center]]

###### NSM: OLAP Example

However, when executing an OLAP query, the inefficiency of loading useless data to memory arises.

![[Pasted image 20250502194541.png | center]]

For example, the condition `WHERE U.hostname LIKE '%.gov'` (without an index) requires scanning all hostnames—meaning we must read every page in the table.

While we can perform the aggregation during the scan to save time, it doesn’t help memory usage. Fields like `userID`, `userName`, and `userPass` are still loaded in memory even though they aren’t needed for the query.

| **NSM**           |                                                                                                                                                                                        |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Advantages**    | - Fast inserts, updates, and deletes.<br>- Can use index-oriented physical storage for clustering.<br>- Efficient for queries that access the full tuple (OLTP).                       |
| **Disadvantages** | - Inefficient for scans over large portions of the table when only a subset of attributes is needed.<br>- Terrible memory locality in access patterns.<br>- Not ideal for compression. |

##### 1. **DSM**, Decomposition Storage Model. (**Column-based** Storage)

In the decomposition storage model, the DBMS stores a single attribute (column) for all tuples contiguously in a page. Thus, it is also known as a “column store.” This model is ideal for **OLAP** workloads with many read-only queries that perform large scans over a subset of the table’s attributes.

###### Physical Organization

Store attributes and meta-data (e.g., nulls) in separate arrays of fixed length values, And Maintain separate pages per attribute with a dedicated header area for metadata about entire column.

Most systems identify unique physical tuples using offsets into these arrays. Need to handle variable-length values

![[Pasted image 20250502204334.png| center|550]]

###### DSM: OLAP Example

Now, when we run the same query, all we need to do in the `WHERE` clause is access a single page — the `hostname` one. We apply the condition, retrieve the matching offsets, then evaluate the aggregation by loading the `lastlogin` page and computing the aggregation over those offsets — and _voila_, we're done!

No extra data is loaded, and no memory is wasted!

![[9son6i.gif|center|550]]

But wait a minute, How are we *exactly* identifying the tuples?

###### Tuple Identification

There are two common approaches for identifying tuples in columnar storage:

1. **Fixed-Length Offsets**  
    Each value within a column is stored at a known, fixed-length offset. This allows for fast and predictable access without needing extra metadata.
    
2. **Embedded Tuple IDs**  
    Each value includes a tuple ID directly embedded alongside it, making the position of the value explicit.
    

In practice, **Fixed-Length Offsets** are far more common due to their simplicity and efficiency. On the other hand, **Embedded Tuple IDs** can be risky — they introduce overhead and complexity, and are generally considered deprecated.

![[Pasted image 20250502205626.png|center]]

And here comes another annoying question. What about *Variable-Length Data*?

Padding would be the first fix you would think of but it's wasteful  — Very very wasteful especially for large attributes. 

A Better approach is to use *Dictionary Compression* to convert repetitive variable-length data into fixed-length values (typically 32-bit integers). 


| **DSM**           |                                                                                                          |
| ----------------- | -------------------------------------------------------------------------------------------------------- |
| **Advantages**    | - Reduce the amount of wasted I/O per query.<br>- Faster query processing.<br>- Better data compression. |
| **Disadvantages** | - Slow for point queries, inserts, updates, and deletes<br>because of splitting/stitching/reorganization |

##### Pax Storage Model

Partition Attributes Across (PAX) is a hybrid storage model that vertically partitions attributes within a database page.

The goal is to get the benefit of processing faster on columnar storage while retaining the spatial locality benefits of row storage.  

###### Physical Organization

Horizontally partition data into row groups. Then vertically partition their attributes into column chunks. Each row group contains its own meta-data header about its contents, And Global meta-data directory contains offsets to the file's row groups, It's stored in the footer if the file is immutable.

![[Pasted image 20250502210704.png|center|500]]

### Compression

Even after all our optimizations, our queries still haven't reached their full _speed_ potential. The main bottleneck? **I/O speed**.

To mitigate this, the DBMS can _compress_ pages, effectively increasing the amount of data transferred per I/O operation. However, compression introduces a classic trade-off: **speed vs. compression ratio**. As compression ratios increase, so does the cost of decompression — impacting query latency.

##### So what are our goals when compressing data?

1. **Fixed-Length Values**  
    Compression should ideally produce fixed-length outputs for predictable access.  
    The only exception: variable-length data (like strings) may be stored in a separate overflow pool.
    
2. **Late Materialization**  
    Decompression should be delayed as much as possible during query execution.  
    This strategy — known as _late materialization_ — avoids unnecessary work for columns that may never be used.
    
3. **Lossless Compression**  
    The scheme must be _lossless_, meaning no data is lost or approximated during compression and decompression.

##### Block-Level Compression

The DBMS compresses data using a general-purpose algorithm (e.g., `gzip`, `LZO`, `LZ4`, `Snappy`,  `Oracle OZIP`, `Zstd`).

Although there are several compression algorithms that the DBMS could use, engineers often choose ones that often provide lower compression ratio in exchange for faster compress/decompress.

An example of using naive compression is **MySQL InnoDB**. The DBMS compresses disk pages, and pad them to a power of two KBs (1, 2, 4, 8) and stored them in the buffer pool.

However, every time the DBMS tries to read data, the compressed data in the buffer pool has to be decompressed.

Since accessing data requires decompression of compressed data, this limits the scope of the compression scheme.

If the goal is to compress the entire table into one giant block, using naive compression schemes would be impossible since the whole table needs to be compressed/decompressed for every access.

Therefore, **MySQL** breaks the table into smaller chunks since the compression scope is limited.

![[Pasted image 20250502212231.png | center]]

##### Columnar Compression

###### Run-Length Encoding (RLE)

RLE compresses **runs of the same value** in a single column into `triplets`:

- The value of the attribute    
- The start position of the run in the column segment
- The number of elements in the run

The DBMS should sort the columns intelligently beforehand to maximize compression opportunities. These clusters duplicate attributes and thereby increasing the compression ratio.

![[Pasted image 20250502212711.png|center]]

###### Bit-Packing Encoding 

When values for an attribute are always less than the value’s declared largest size, store them as a smaller data type.

![[Pasted image 20250502212844.png | center]]

###### Patching/Mostly Encoding

Bit-packing variant that uses a special marker to indicate when a value exceeds the largest size and then maintains a look-up table to store them.

![[Pasted image 20250502212956.png | center]]

###### Bitmap Encoding

The DBMS maintains a separate **bitmap** for each unique value of a particular attribute. Each bitmap is essentially a binary vector where the position corresponds to a specific tuple in the table. 

The `i-th` bit in the bitmap indicates whether the `i-th` tuple contains that value. To avoid allocating large contiguous memory blocks, these bitmaps are typically segmented into smaller chunks.

This method is effective only when the attribute has low cardinality, as the number of bitmaps — and therefore the total storage — grows linearly with the number of distinct values. If the cardinality is high, the resulting bitmaps can become larger than the original dataset, making this approach impractical.

![[Pasted image 20250502213217.png|center]]

###### Delta Encoding

Instead of storing exact values, record the difference between values that follow each other in the same column. The base value can be stored in-line or in a separate look-up table.

We can also use **Run-Length Encoding** on the stored deltas to get even better compression ratios.

![[Pasted image 20250502213317.png | center]]

###### Incremental Encoding 

This is a type of delta encoding avoids duplicating common prefixes/suffixes between consecutive tuples. This works best with sorted data.

![[Pasted image 20250502213453.png | center]]


###### Dictionary Encoding 

 The most common database compression scheme. The DBMS replaces frequent patterns in values with smaller codes. It then stores only these codes and a data structure (i.e., dictionary) that maps these codes to their original value.
 
A dictionary compression scheme needs to support fast encoding/decoding, as well as range queries.

![[Pasted image 20250502213632.png | center]]