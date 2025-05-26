### Introduction:

##### Devices volatility

Volatile devices mean that if you pull the power from the machine, then the data is lost. Volatile storage supports fast `random access` with `byte-addressable` locations. This means that the program can jump to any byte address and get the data that is there.

On the other hand, Non-Volatile devices don't require continuous power in order to retain the bits it's storing.

Also, these devices are `block/page addressable`. This means that in order to read a value at a particular offset, the program first has to load the whole `block/page` into memory that holds the value the program wants to read.

##### Sequential vs Random access

Sequential access is when data is read or written in a sequentially ordered manner.
Random access is when data is read or written in a unordered manner.

What we need to remember here is Random access on non-volatile storage is almost always much slower than sequential access.

##### Storage Hierarchy 

![[Pasted image 20250430180903.png|center]]

##### Databases Storage Types

DBMS can be categorized based on the storage technology they employ into:

1. **Disk-oriented Databases**: In these databases, the primary storage location of data is on *non-volatile* disk(s). 
2. **In-Memory Databases**: Store data primarily in main memory (RAM) instead of disk.

My reference [CMU 15-455/645](https://15445.courses.cs.cmu.edu/fall2024/) focuses on **Disk-Oriented** DBMS architecture


---
### Disk-Oriented DBMS Overview
 
 In Disk-Oriented DBMS the database is all on disk but it does not operate on disk.
 Most DBMS store their data on disk so we can have the advantage of large disk sizes, and data safety. but when we want to access the data to manipulate it, we move the data from disk to memory, so we can get the advantage of fast access time.
##### Buffer Pool and Execution Engine

So in DBMS, we have what is called a `Buffer Pool`, the buffer pool manager is responsible for managing the supply of DBMS with the page requested and making sure that this page will be in memory. _( we will visit the buffer pool in later lectures)_

The DBMS also has an `execution engine` that will execute queries.

So here is a sample query execution scenario:

1. The `execution engine` will ask the `buffer pool` for a specific page.

2. The `buffer pool` will take care of bringing that page into memory and giving the execution engine a `pointer` to that page in memory.

3. The `execution engine` will interpret the layout of the page.

![[Pasted image 20250430182046.png|center]]

	Wait a minute, So it's all Virtual Memory ??
	Guess i have to study OS after all :'(

![[Pasted image 20250430182650.png|center|400]]


#### DBMS vs OS

A high-level design goal of the DBMS is to support databases that exceed the amount of memory available.

Since reading/writing to disk is expensive, disk use must be carefully managed. We do not want large *stalls* from fetching something from disk to slow down everything else.

> *Stall in operating systems refers to the delay in processor execution that is caused by waiting for data to become available in memory.*

This high-level design goal is like **virtual memory**, where the OS provides the user with the illusion of having a very big main memory. This is done by treating a part of the disk as the memory (RAM).

**Then, Why not use the OS?**

Because OS has no clue about the logical dependence between pages, So it may replace a page what was important during execution, leading to slowness.


### File Storage

In its most basic form, a DBMS stores a database as files on disk. Some may use a file hierarchy, others may use a single file (e.g., SQLite), and typically the files are in a proprietary format.

The operating system does not know anything about the content of these files. Only the DBMS knows how to decipher its contents since it is encoded in a way specific to the DBMS.

The DBMS’s storage manager is responsible for managing a database’s files:

- It represents the files as a collection of pages.
- Tracks data read/written to pages.
- Tracks the available space in pages.
- Some do their own scheduling for reads and writes to improve the spatial and temporal locality of pages.

### Database Pages

The DBMS organizes the database across one or more files in fixed-size blocks of data called `pages`.

- It can contain tuples, meta-data, indexes, log records.
- Most systems do not mix page types.
- Some system will require that pages are self-contained, meaning that all the information needed to read each page is on the page itself.

##### Page Identifiers

Each page is given a unique `ID`. If the database is a single file, then the page id can just be the file offset.

##### Page Size

Most DBMSs use fixed-size pages to avoid the engineering overhead needed to support variable-sized pages.

- For example, with variable-size pages, deleting a page could create a hole (Fragmentation) in files that the DBMS cannot easily fill with new pages.

There are three concepts/types of pages in DBMS:

1. **Hardware page,** this is the page that is specified by the Hard Disk (usually 4 KB).
2. **OS page**, this is the page that the OS can work with, and it is usually the same size as the Hardware page (4 KB).
3. **Database page** (1-16 KB).

![[Pasted image 20250430205024.png|center]]
##### Atomic Writes

The Storage device ensures `atomic writes` of the **hardware page** size. This means if out database page size is equal to the hardware page we will gain `atomicity` over the writes so if we attempt to write 4KB to the disk, either all or none is written. 

But, if the database page size exceeds the hardware page size the DBMS will have to take extra measures to ensure that the data gets written safely since the program can get half way through writing a database page to disk when the system crashes.

Now we need to know how theses pages are structured and organized.

### Page Storage Architecture

_Finding_ pages in a single database file is straightforward.

The formula to locate a specific page is simply the page number multiplied by the page size.

![[Pasted image 20250430205402.png|center]]

But What if we have multiple files?

![[Pasted image 20250430205521.png|center]]

DBMSs have different strategies for managing pages in files on disk. 

1. Heap File Organization :
	where records or tuples are stored randomly within a file. 
2. Sequential/Sorted File Organization (ISAM)
3. Tree File Organization
4. Hashing File Organization.

Each method has its own benefits and considerations. But we will go further explaining the Heap File Organization.

##### Heap File Organization

A heap file is unordered set (Hash table) of an files where records or tuples are stored in a random order, eliminating the need for sorting. In this method, metadata is used to track the existence of pages and the available free space on each page. 

There are two common ways to represent a heap file:

1. **Page Directory representation**: In this method the DBMS creates special pages that track the locations of data pages along with the amount of free space on each page called page directory.

![[Pasted image 20250430210230.png | center]]

2. **Linked List representation**: In this approach, the pages within the file are linked together using pointers. Each page contains records/tuples, and there is a pointer from one page to the next, forming a linked list structure. if the DBMS is looking for a specific page, it has to do a sequential scan of the data page list until it finds the page it is looking for.

### Page Layout

Each page is divided into two main sections, the first one is the **Page Header**, and the second is **Page Data**.

![[Pasted image 20250501001400.png|center]]

##### Page Header

On every page of a database file, there is a **page header** that stores metadata related to the page. The page header provides essential information for interpreting and understanding the data stored within the page. The specific metadata stored in the page header can vary between different DBMSs.

Examples of metadata that can be stored in the page header include the page size, DBMS version, compression information, checksums for data integrity, and other relevant details.
This varies for each DBMS.

Some DBMSs prefer to make their pages self-contained, meaning that the metadata within the page header contains all the necessary information for the DBMS to identify the page and its contents. However, the exact implementation and content of the page header depend on the specific design and requirements of the DBMS being used.

##### Page Data

When it comes to storing data in pages, there are various approaches to consider. There are three different methods for storing tuples or records within a page:

1. **Tuple-oriented Storage**
2. **Slotted Pages Approach**
3. **Log-structured Storage**

##### Tuple-Oriented Approach

In this approach, the page header contains information about the number of tuples stored on the page. To add a new tuple, we append its data to the existing page data at the first free space it finds, and update the number of tuples.

If we want to delete some record we find, delete it and update the number of tuples in the page header. This results in the following structure for our pages:

![[34b6e66a-ef82-44fc-90db-c2ba416b67e8.webp|center]]

This has several drawbacks:

- Firstly, It assumes that all tuples have the same length. However, records often have variable lengths, especially when dealing with attributes like strings that can vary in size. This limitation prevents us from efficiently locating the appropriate position for inserting a new record in the page. 

- Secondly, deletions in this approach lead to page fragmentation.

##### Slotted Pages

This is the most common approach used in DBMSs today. Our page is divided into **slots,** **a header,** and **data**

In the header, we keep track of the number of used slots, the offset of the starting location of the last used slot, and a slot array, which keeps track of the location of the start of each tuple in the data section. Our page will be like this:

![[Pasted image 20250501144521.png|center]]

To add a tuple, the **slot array** will grow from the beginning to the end, and the data of the tuples will grow from the end to the beginning, So the page is considered full when the slot array and the tuple data meet.

Deletion here could use two strategies:
1. Lazy Deletion (Slot Marking): Mark the slot *deleted*, This is as easy, and fast as flipping a flag, **but** it leads to internal fragmentation.

2. Compaction: shift subsequent records in the cell region to fill the hole, and update all affected slot pointers. the biggest drawback is the High CPU and I/O overhead.

Some problems associated with the Slotted-Page Design are:
- **Page Fragmentation:** Pages are not fully utilized

- **Useless Disk I/O:** DBMS must fetch entire page to update one tuple.

- **Random Disk I/O:** Worse case scenario when updating multiple tuples is that each tuple is on a separate page.

### Log-Structured Storage

The idea is instead of storing tuples, the DBMS only stores log records, Where each log entry represents a tuple PUT/DELETE operation.

![[Pasted image 20250501184056.png | center]]

The DBMS applies changes to an in-memory data structure called *MemTable* and then write out the changes sequentially to disk's *SSTable*.
 
In the following GIF, **MemTable** is depicted as a generic data structure. Different DBMS implementations may use a Linked List, Skip List, B+ Tree, or even a Trie for this purpose.

Here Updates aren't that complicated as we just overwrite the value of the entry in-place in the MemTable

When a `Delete(key)` is issued, the MemTable doesn’t remove the key immediately. Instead, it inserts a special **tombstone marker** for that key. This tombstone indicates that the key is deleted, and it will later be respected during compaction.

Once the MemTable becomes full (or reaches a size threshold), it's frozen and *flushed* to disk as an SSTable(Sorted String Table). The flushed MemTable becomes **immutable** and a new one is created to handle new writes.

If you run a `GET(key)` before a flush: 
1. The DB checks the MemTable first
2. If the key is found there it's returned immediately
3. if not it checks SSTables.

![[9skcux.gif|center|600]]

##### Level DB
You may notice that there are levels for SSTables on disk — but what are those?

SSTables on disk are organized into **multiple levels** (e.g., L0, L1, L2, etc.) to manage space efficiently and optimize read performance. This design supports **sequential writes** (which are fast) while keeping read performance manageable through a process called **compaction**.

When a MemTable is flushed to disk, it becomes a new SSTable in **Level 0 (L0)**. SSTables in L0 may have **overlapping key ranges**, because each flush is written independently without coordination. This means when a read happens, **all L0 SSTables must be checked**, making L0 reads slower if it grows too large.

To prevent this, background processes continuously compact SSTables from L0 into the next level.

As you go deeper (L2, L3...), SSTables grow in size and cover larger key ranges, but remain sorted and non-overlapping within each level.

##### Summary Table

But now our reads might get **very slow**, especially in the worst-case scenario — we might need to scan through the entire **MemTable**, all **L0 SSTables**, and multiple deeper levels on disk to locate a single key.

To speed up these reads, DBMSs use metadata structures like the **Summary Table** (and related structures such as indexes and filters) to help **skip unnecessary SSTables** during lookups.

The **Summary Table** contains metadata such as:

1. **Min/Max key per SSTable**
    - Allows the system to quickly **eliminate** SSTables that can’t possibly contain the key.
	
    - Example: If you're looking for key `42` and an SSTable has keys from `100–200`, it’s skipped entirely.
    
2. **Key filters per SSTable or level** (e.g., **Bloom Filters**)
	
    - These are probabilistic structures that can **tell with high certainty** whether a key is **definitely not** in an SSTable.



### Tuple Layout  

A tuple is essentially a sequence of bytes, and it is the DBMS’s job to interpret those bytes into attribute types and values.

Each DBMS stores tuples in its own way, and it all depends on the implementation details of the specific DBMS. but we can separate the tuple structure into two main sections:

![[Pasted image 20250501195433.png|center]]


1. **Tuple Header** contains metadata about the tuple.
	- Visibility information for concurrency control protocol ( will be explained later)
	
	- Bit Map for NULL values.
	
2. **Tuple Data**, the actual data of the tuple
    - Attributes attribute values of the tuple (typically stored in the order that you specify them when you create the table).
    
    - Most DBMSs do not allow a tuple to exceed the size of a page.

**Record ID (Unique Identifier)**

Each tuple in the database is assigned a unique identifier
- The most common way to construct a unique ID for the tuple is **_page_id+(offset or slot number)**_. Can also contain file location info.
    
- This is not the primary key of the tuple. So the application cannot rely on these IDs to mean anything.
    
- In different DBMSs, it has different names.

##### Word-Aligned Tuples 

Modern CPUs are optimized for reading memory in **aligned chunks**, often referred to as **machine words** (e.g., 4 or 8 bytes depending on architecture). To take advantage of this, most DBMSs ensure that **attribute values are word-aligned** in memory or on disk. This improves access speed and avoids costly misaligned memory reads.

To maintain alignment, **padding bytes** may be inserted between attributes. This typically occurs when The **preceding attribute size** doesn't align naturally with the word size of the next attribute.

![[Pasted image 20250501200109.png|center]]

Another approach is reordering we where switch the order of attributes in the tuples' physical layout to make sure they are aligned. May still have to use padding to fill remaining space.

![[Pasted image 20250501200230.png|center]]

---