
In the last two lessons, we were concerned with how the DBMS looks on disk. In this lesson, we are going to see how DBMS manages its memory and moves data back and forth from the disk and memory. Since, for the most part, data cannot be directly operated on the disk, any database must be able to efficiently move data represented as files on its disk into memory so that it can be used.

![[Pasted image 20250503161905.png | center]]

An obstacle that DBMSs face is the problem of minimizing the slowdown of moving data around. Ideally, it should “appear” as if the data is all in the memory already. The execution engine shouldn’t have to worry about how data is fetched into memory, This Problem can be approached by considering.
##### Spatial Control & Temporal Control

**Spatial Control** refers to **where** to write pages on disk (physically). The goal of spatial control is to keep pages that are used together often as `physically close together` as possible on disk.

**Temporal Control** refers to **when** to read pages into memory and **when** to write them to disk. Temporal control aims to minimize the `number of stalls` from having to read data from disk.


It should be noted that DBMS needs memory for things other than tuples and indexes, These other *memory pools* may not always be backed by disk (`Query Caching`, `Maintenance Buffers`, `Log Buffers`,  `Dictionary Caches` ).

### Buffer Pool Manager 

The buffer pool serves as a cache in memory for pages retrieved from disk. It is a memory area structured as an array consisting of fixed-size pages (if you have variable-size pages you would have separate buffer pools for each), with each entry in the array referred to as a **_frame_**.

##### Page Requesting

When the DBMS requests a page an exact copy is placed into one of the frames of the buffer pool. Then, the database system can search the buffer pool first when a page is requested. If the page is not found, then the system fetches a copy of the page from the disk. 

![[93643344-2cd1-4e39-82e1-15e910573835.gif | center]]

Dirty Pages are written to the buffer first and **not** written to disk immediately (Write-Back Cache).

>***Dirty pages**
>
>Dirty pages are the pages in the memory buffer that have modified data, yet the data is not moved from memory to disk.*

##### Meta-Data & Page Table 

To ensure efficient and correct usage, the buffer pool requires some metadata:

The page table is an in-memory _hash table_ that keeps track of the pages currently in memory. It establishes a mapping between **page IDs** and their corresponding **frame locations** in the buffer pool. Since the order of pages in the pool doesn't necessarily match the disk's order, this indirection layer helps identify page locations within the pool.

> *If you are focusing and not scrolling throw TikTok like i do, You will probably ask this question*
>
 >*What the difference between **Page Table** and  [[03 - Storage in DBMS#Heap File Organization|Page Directory]]*
 >
 >***Page Table:** is the mapping from page ids to a copy of the page in buffer pool frames. This is an **in-memory** data structure that does not need to be stored on disk.*
 >
 >***Page Directory:** is the mapping from page ids to page locations in the database files. All changes to the page directory must be recorded **on disk** to allow the DBMS to find on restart.*

The page table itself maintains extra metadata for each page:

- **The Dirty Flag:** is set by a thread whenever it modifies a page. This flag notifies the storage manager that the page needs to be written back to disk.
    
- **The Pin/Reference Counter:** keeps track of the number of threads currently accessing a particular page(either reading or modifying it). Before accessing the page, a thread must increment the counter. If the counter is greater than zero, the storage manager cannot evict that page from memory. 

- **Access Tracking Information** : who and when was the page accessed. 

The pin here means someone is accessing the page (holding it's pointer) and we cant evict it and add another page instead.

The lock/latch means no one can access this page while the system is inserting/reading/updating it.

![[Pasted image 20250503164840.png | center]]



Before diving further into the buffer pool details and mechanisms, We need to make a distinction between `locks` and `latches` when discussing how the DBMS protects its internal elements.

##### Locks & Latches

**Locks:** A **lock** is a **high-level concurrency control mechanism** used to protect the **logical contents of the database**, such as tables, rows (tuples), or even entire databases, from conflicting operations by concurrent transactions.

- Locks are typically **held for the entire duration of a transaction** and are only released once the transaction commits or rolls back.
    
- Since locks operate at the transaction level, the system must be able to **rollback** any changes made by a transaction if it aborts.

**Latches:** A **latch** is a **low-level mutual exclusion primitive** used internally by the DBMS to protect its own **in-memory data structures**, such as buffer pool frames, B-tree nodes, or hash tables, from race conditions in a multi-threaded environment.

- Ensure thread-safe access to critical sections inside the DBMS kernel — this is not related to transaction logic or user-visible data, but rather to maintain structural consistency within the DBMS.
    
- Latches are **short-lived** and held only for the **duration of a critical operation**, often just a few instructions or a single function call.
	
- Latches **do not support rollback**. 
	
- Latches can be implemented using **OS-level mutexes or spinlocks**, but many high-performance DBMS systems **implement their own latch mechanisms**

**The Analogy**

In operating systems, we use **locks* to protect shared memory. In DBMS internals, we call those same primitives **latches**. But in databases, **locks** refer to **transaction-level mechanisms**, not just memory synchronization.


##### Why Not Use The OS?

You may wonder why we need to use a buffer pool manager instead of simply relying on the Operating System’s page cache. 
There are several problems with using something like memory-mapped files (mmap):
1. Transaction Safety: The OS can flush dirty pages at any time. 
2. I/O Stalls: The DBMS doesn’t know which pages are in memory. The OS might stall a thread on a page fault. 
3. Error Handling: It is difficult to validate pages. Any page access can cause a SIGBUS signal that the DBMS then must handle. 
4. Performance Issues: Internal OS data structure contention. TLB shootdowns.

There is a paper on [why should not use mmap in your DBMS](https://db.cs.cmu.edu/mmap-cidr2022/).  

>*I didn't read this but it seams useful :)*

There are some solutions to some of these problems: 
- `madvise`: Tell the OS how you expect to read certain pages. 
- `mlock`: Tell the OS that memory ranges cannot be paged out. 
- `msync`: Tell the OS to flush memory ranges out to disk. 

But using these syscalls to get the OS to behave correctly is just as onerous as managing memory yourself.

TLDR; The OS is **not** your friend, DBMS almost always wants to control things for itself and can do a better job than the OS.

### Buffer Replacement Policies

When The DBMS needs to free up a frame to make room for a new page, it must decide which page to evict from the buffer pool. A replacement policy is an algorithm that the DBMS implements that makes a decision on which pages to evict from the buffer pool when it needs space.

When designing a replacement policy, the following factors should be considered:

1. **Speed**: The replacement policy should be efficient and not significantly slow down the system when applied.
    
2. **Correctness**: The chosen replacement policy should produce accurate and reliable results, ensuring that the evicted page does not impact the correctness of the system.
    
3. **Accuracy**: The replacement policy should strive to be as accurate as possible in selecting the most suitable page for eviction based on various criteria, such as the recency of use or frequency of access.
    
4. **Low Overhead**: The replacement policy should not require excessive storage of metadata or introduce high overhead for the system. It should strike a balance between maintaining necessary information for eviction decisions and minimizing resource consumption.

##### Least-Recently Used (LRU)

The Least Recently Used (LRU) replacement policy operates by maintaining a timestamp for each page in the buffer pool, indicating when it was last accessed. When the DBMS needs to evict a page from the buffer pool, it selects the page with the oldest timestamp.

To efficiently implement the LRU policy, the timestamps can be stored in a separate data structure, such as a queue. This allows for easy sorting of the timestamps and simplifies the process of identifying the page with the oldest timestamp. By using a separate data structure for managing the timestamps, the overhead of sorting during eviction can be reduced, leading to improved efficiency in the replacement process.

##### Clock

The CLOCK policy is an approximation of LRU that does not require maintaining a separate timestamp for each page. Instead, each page is assigned a reference bit. When a page is accessed, its reference bit is set to 1.

To visualize this, organize the pages in a circular buffer with a “clock hand”. Upon sweeping check if a page’s bit is set to 1.

If yes, set it to zero, if no, then evict it. In this way, the clock hand remembers the position between evictions.

##### Alternatives 

There are several problems with `LRU` and `CLOCK` replacement policies.

LRU and CLOCK replacement policies can be susceptible to an issue called _Sequential Flooding_. This occurs when a sequential scan reads every page in the buffer pool, causing the timestamps or reference bits of all pages to be updated. This pollutes the buffer pool with pages that are read once and then never again.

In OLAP workloads, the *most recently used* page is often the best page to evict

LRU + CLOCK only tracks when a page was last accessed, but not how often a page is accessed.

To address the limitations of LRU and CLOCK policies, there are three proposed solutions:

###### 1. LRU-K: 

This solution involves tracking the history of the last K references to each page, effectively maintaining a longer timestamp history. By considering the interval between subsequent accesses, the DBMS can predict the next time a page will be accessed. This approach improves the accuracy of determining the importance of pages and helps make better eviction decisions.

Use single LRU linked list but with two entry points (*old* & *young*). New pages are always inserted to the head of the old list. If the pages in the old list is accessed again, then insert into the head of the young list.

![[Pasted image 20250503213035.png|center|550]]


###### 2. Localization per Query:

Instead of applying a global replacement policy, the DBMS can optimize the buffer pool on a per-transaction or per-query basis. By considering the specific needs of each transaction or query, the buffer pool can be tailored to minimize interference and pollution from unrelated queries.

This approach improves the efficiency of the buffer pool by prioritizing pages based on the active workload.

Example: PostgreSQL assigns a limited number of buffer of buffer pool pages to a query and uses it as a circular ring buffer.

###### 3. Priority Hints:

Transactions or queries can provide hints to the buffer pool about the importance or relevance of specific pages based on their context during query execution. These priority hints assist the buffer pool in making informed decisions when selecting pages for eviction. By leveraging the knowledge provided by transactions, the buffer pool can prioritize the retention of critical pages and optimize performance accordingly.


These three solutions aim to enhance the effectiveness, efficiency, and adaptability of page replacement policies in managing the buffer pool, ultimately improving the overall performance of the DBMS.


##### Dirty Pages

There are two approaches to handling pages with dirty bits in the buffer pool:

1. **Fast Eviction**: The fastest option is to drop any page in the buffer pool that is not marked as dirty. This approach prioritizes fast eviction of clean pages to make room for new pages.
	
2. **Write Back**: The slower method involves writing back dirty pages to disk to ensure that any modifications are persisted. 

Trade-off between fast evictions versus dirty writing pages that will not be read again in the future.

##### Background writing

To mitigate the problem of unnecessary page writes, the concept of background writing can be employed. In background writing, the DBMS periodically scans the page table and writes dirty pages to disk. This approach helps avoid the need to write out pages unnecessarily, improving efficiency and reducing overhead in buffer pool management.


### Disk I/O & OS Cache

Modern operating systems and hardware aim to **maximize disk throughput** by **reordering and batching I/O requests**. However, they lack **semantic awareness** of which I/O operations are critical and which are not.

In contrast, the **DBMS maintains its own internal I/O queues**, tracking page read/write requests across the system with **detailed context**. This allows it to assign **priorities** to I/O operations based on several factors, including:

- **Access pattern**: Sequential vs. Random I/O
    
- **Task criticality**: Critical-path operations vs. Background tasks
    
- **Data type**: Table data, Indexes, Transaction logs, or Temporary (ephemeral) data
    
- **Transaction context**: Whether the data is needed for an active, high-priority transaction
    
- **Service-level objectives (SLAs)**: User-defined performance or latency requirements
    

Because the **OS is unaware of these DBMS-specific priorities**, it can inadvertently interfere with optimal I/O handling—for example, by delaying a critical read in favor of less urgent batched writes. This is why many DBMSs implement **I/O scheduling and caching policies at the user level**, bypassing or working around the default OS strategies when necessary.


##### OS Page Cache 

Most disk operations go through the OS API. Unless explicitly told otherwise, the OS maintains its own filesystem cache. Most DBMS use direct I/O [(O_DIRECT)](https://linux.die.net/man/2/open) to bypass the OS’s cache in order to avoid redundant copies of pages and manage different eviction policies. not Loss of control over file I/O.

![[Pasted image 20250504001523.png | center]]

######  `fsync()` Problems in DBMS

**What happens when a DBMS calls `fwrite()`?**
- `fwrite()` writes data to the **OS page cache**, _not directly to disk_.
    
- This means the write is _buffered_ and may not persist immediately.

**What happens when a DBMS calls `fsync()`?**
- `fsync()` forces the OS to **flush dirty pages** from the page cache to **stable storage (disk)**.
    
- It is used to ensure that data has been _durably written_.

**The `fsync()` failure issue (EIO - I/O error):**
- If `fsync()` fails (e.g., due to a disk I/O error):    
    - The OS may **still mark the pages as clean**, even though they were _not actually flushed_ to disk.
        
    - If the DBMS calls `fsync()` again afterward, the OS may **report success**, even though no new flush happened.
        
    - As a result, the **DBMS thinks the data is safely written**, but it’s **actually lost**.

**🚨 Why this is dangerous:**

- The DBMS relies on `fsync()` to guarantee durability. But if the OS misleads the DBMS after a failure, this **violates the durability guarantee** of transactions.

##### Buffer Pool Optimization

###### 1. Multiple Buffer Pools:

One of the primary optimizations is to implement multiple buffer pools. By maintaining separate buffer pools for different purposes (such as per-database buffer pool or per-page type buffer pool), the DBMS can employ specific policies customized for the data stored in each pool. This approach effectively reduces latch contention and enhances data locality, resulting in improved performance.

Two methods can be used to map desired pages to a buffer pool:

**1. Object IDs**

In the object IDs approach, record IDs are extended to include an object identifier. This object identifier enables the maintenance of a mapping between objects and specific buffer pools.

![[Pasted image 20250504002245.png|center]]


**2. Hashing**

The hashing approach involves the DBMS hashing the page ID to determine which buffer pool to access. This hashing mechanism assists in selecting the appropriate buffer pool for retrieving the desired page (this is the MySQL approach).

![[Pasted image 20250504002348.png|center]]

One basic idea in this context is to ensure that a physical page exists in only one buffer pool. It is undesirable to have multiple threads mapping the same page to different buffer pools, as it would result in having duplicate copies of the page in memory. To avoid this situation, the goal is to maintain a consistent mapping of pages to buffer pools, ensuring that each physical page resides in a single buffer pool.

###### Pre-fetching

Another optimization technique involves pre-fetching pages based on the query plan. As the first set of pages is being processed, the DBMS can proactively fetch the second set of pages into the buffer pool. This strategy is particularly effective when accessing a large number of pages sequentially. By anticipating the data needs of the query and pre-loading the relevant pages, the DBMS minimizes the wait time for subsequent page accesses, leading to improved query performance.

![[9ss8ws.gif | center]]

###### Scan Sharing (Synchronized Scans)

Scan Sharing, also known as Synchronized Scans, is a technique that enables the reuse of data retrieved from one query for other queries that may attempt to read the same data simultaneously. This approach allows multiple queries to access and utilize the same set of data, avoiding the need to retrieve it separately for each query.

Scan Sharing, is different from Result Caching, It operates at a lower level within the DBMS, specifically within the buffer pool. It involves reusing pages that have been fetched by one query and making them available for reuse by another query. Instead of caching the output of a query, Scan Sharing focuses on reusing the underlying data pages that have already been loaded into memory.

![[9ss9b9.gif | center]]

Here, notice that:

- **Query Q1** begins scanning a table by loading `page0` from disk into the buffer pool.

- As Q1 continues scanning sequentially (page by page), the pages it fetches are stored in the buffer pool.

- If another query (say, Q2) starts scanning the same table while Q1 is still in progress, it can **join Q1's scan mid-flight** instead of starting over from `page0`.

- This technique minimizes **redundant I/O**, reduces disk bandwidth usage, and improves **cache efficiency** by aligning the scans of concurrent queries.

This approach is especially effective in **data warehouses** or **OLAP systems** where long-running scans over large tables are common and can overlap across queries.

###### Buffer Pool Bypass

By employing this technique, the DBMS bypasses the buffer pool and stores the fetched pages directly in the memory or cache. This approach eliminates the overhead associated with the buffer pool manager. However, it comes with the trade-off that the pages are specific to the running queries and are cleared from memory once the queries complete execution.

This method works well when the operator needs to read a **large sequence** of pages that are contiguous on disk. It can also be effectively applied when handling **temporary data**, such as sorting or join operations.

