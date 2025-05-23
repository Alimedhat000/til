In our journey through DBMS internals, we've explored storage architectures and how pages are managed on disk and in memory using the buffer pool. Now, we reach a critical point: understanding how these pages interact with the execution engine—and the role key data structures play in this connection.

We'll focus on two foundational structures in DBMS: **Hash Tables** and **B+ Trees**. These are essential for indexing, joins, and temporary in-memory operations. In this article, we dive into **Hash Tables**, exploring their components, how they're implemented, and why they're critical to performance.

---

### Data Structures in DBMS

Data structures are the backbone of DBMS performance and internal operations:

1. **Meta-Data Structures** – Manage system state and memory, such as _page tables_ and _page directories_.
    
2. **Core Storage Structures** – Efficiently store and retrieve tuples (rows) from disk.
    
3. **Temporary Structures** – Built on the fly during query execution, e.g., hash tables for joins.
    
4. **Indices** – Speed up tuple lookups through auxiliary structures like B+ Trees and Hash Indexes.
    

---

### Hash Tables in DBMS

Hash tables implement an **unordered associative array**, mapping keys to values. In our DBMS context, we assume keys and values are of fixed size.

#### Performance
- **Space Complexity:**
    - Theoretically `O(n)`—in worst-case collisions, we may probe the entire table.
        
    - Practically, table size is 2–4× the number of keys to minimize collisions.
        
- **Time Complexity:**
    - **Average Case:** `O(1)`
        
    - **Worst Case:** `O(n)` (e.g., many collisions)
        

---

### Hash Functions

A **hash function** maps a key to an index in a table. The goal is a balance between speed and low collision rate.

- A function that's too simple (e.g., always returns the same value) causes collisions.
    
- A perfect function avoids collisions but may be too slow to compute.

In DBMS, we prioritize **fast, deterministic** functions over cryptographic ones. Popular choices include:

- `MurmurHash`
    
- `CityHash` / `FarmHash`
    
- Facebook’s `XXHash` and the current state-of-the-art, `XXHash3`

---
### Static Hashing Schemes

In a static hashing scheme, the size of the hash table is fixed. This means that once the hash table reaches its storage limit, the DBMS needs to rebuild a larger hash table from the beginning, which can be quite costly. Normally, the new hash table is created with a size that is **twice** as large as the original.

To minimize inefficient comparisons, it is important to avoid collisions between hashed keys. One common approach is to allocate double the number of slots in the hash table compared to the expected number of elements. This provides additional space to accommodate potential collisions and reduces the likelihood of wasted comparisons during key retrieval.

The following assumptions usually do not hold in reality:

1. The number of elements is known ahead of time and fixed.
2. Each key is unique.
3. There exists a perfect hash function. Therefore, we need to choose the hash function and hashing schema appropriately

##### Linear Probe Hashing 

This is the fastest and most basic hashing scheme, also most DBMSs implement it. It uses a circular buffer of array slots and stores both key and value in the slot.

![[Pasted image 20250506192900.png|center]]

**How does it deal with collisions?**

The hash function maps keys to slots. When a collision occurs, we linearly search the adjacent slots until an open one is found.

![[cb90bad8-7732-4b4e-aa99-8b543edd3831.gif | center]]

**Lookups**

For lookups, We can check the slot the key hashes to, and search linearly until we find the desired entry. If we reach an empty slot or we iterated over every slot in the hash table, then the key is not in the table.

**Deletions**

Deletions are more tricky. We have to be careful about just removing the entry from the slot, as this may prevent future lookups from finding entries that have been put below the now-empty slot.

![[0f8a1bc7-b51f-4722-879e-a718f1a10213.gif|center]]

There are two solutions to this problem:

1. The first option is to _shift the adjacent data after deleting an entry_ to fill the now empty slot. However, we must be careful to only move the entries which were originally shifted. **This is rarely implemented in practice.**
    
2. The other and most common approach is to use `“tombstones”`. Instead of deleting the entry, we replace it with a `“tombstone”` entry that tells future lookups to keep scanning. (***Soft Delete***)

![[2e5538aa-b551-448e-9a22-01d7be749c66.gif|center]]

#### Non-unique Keys

In the case where the same key may be associated with multiple different values or tuples, there are two approaches to take:

1. **Separate Linked List:** Instead of storing the values with the keys, we store a pointer to a separate storage area that contains a linked list of all the values.
    
2. **Redundant Keys:** The more common approach is to simply store the same key multiple times in the table. Everything with linear probing still works even if we do this.

![[Pasted image 20250506193718.png|center]]


### Cuckoo Hashing

The name is inspired by the cuckoo bird's behavior of displacing eggs in other birds’ nests—analogous to how keys may displace others during insertion.

Cuckoo hashing is a collision-resolution strategy that uses **multiple hash functions** and tables to ensure constant-time lookups and deletions.

Instead of one hash table and one hash function, Cuckoo hashing maintains **two or more hash tables**, each with a **different hash function**. These hash functions are typically variants of the same algorithm (like `XXHash` or `CityHash`) using different seeds to ensure different outputs for the same input.

**Insertion Process**

1.  **Hash the item** using two or more hash functions (commonly 2 or 3).    
2. **Check the candidate slots** in the respective hash tables.
    - If **any are free**, insert the item there.
        
    - If **none are free**, choose one slot (typically at random), **evict** its current occupant, and **rehash** the evicted item into its alternative slot.
        
    - Repeat this process as needed (this is the "cuckoo" behavior—evicting others from their nests).
        
3. **Cycle detection**:
    - If the algorithm enters a loop (e.g., evicting endlessly), it’s a sign of a cycle.
        
    - In such cases, we either:
        - Rebuild the table with **larger capacity**, or
        - Use **new hash seeds** to avoid repeating the cycle.

![[9t269z.gif|center]]

When inserting item `C`:

1. Both potential slots are full (e.g., with `A` and `B`).    
2. `C` kicks out `B`.
3. `B` is reinserted to its alternative slot, evicting `A`.
4. `A` is finally placed in its other hash location.


### Dynamic Hashing Scheme

The previous hash tables require the DBMS to know the number of elements it wants to store. Otherwise, it must rebuild the table if it needs to grow/shrink in size.

Dynamic hashing schemes are able to **resize** the hash table on demand without needing to rebuild the entire table. The schemes perform this resizing in different ways that can either maximize reads or writes.

##### Chained Hashing

This is the most common dynamic hashing scheme. The DBMS maintains a **linked list** of buckets for each slot in the hash table. Keys that hash to the same slot are simply inserted into the linked list for that slot.

![[Pasted image 20250506195335.png|center]]

A simple optimization we can do is the bucket pointers we can add filters that say if the key exists in the chain or not.
##### Extendible Hashing

An improved variant of chained hashing that splits buckets instead of letting chains grow forever. This approach allows multiple slot locations in the hash table to point to the same bucket chain.

The core idea behind **re-balancing** the hash table is to move bucket entries on split and increase the number of bits to examine to find entries in the hash table. This means that the DBMS only has to move data within the buckets of the split chain; all other buckets are left untouched.

- The DBMS maintains global and local depth bit counts that determine the number of bits needed to find buckets in the slot array.
    
- When a bucket is full, the DBMS splits the bucket and reshuffles its elements. If the local depth of the split bucket is less than the global depth, then the new bucket is just added to the existing slot array. Otherwise, the DBMS doubles the size of the slot array to accommodate the new bucket and increments the global depth counter.

##### Linear Hashing

Instead of immediately splitting a bucket when it overflows, this scheme maintains a split pointer that keeps track of the next bucket to split. No matter whether this pointer is pointing to a bucket that overflowed, the DBMS always splits.

The overflow criterion is left up to the implementation.

- When any bucket overflows, split the bucket at the pointer location by adding a new slot entry and creating a new hash function.
    
- If the hash function maps to a slot that has previously been pointed to by a pointer, apply the new hash function.
    
- When the pointer reaches the last slot, delete the original hash function and replace it with a new hash function.