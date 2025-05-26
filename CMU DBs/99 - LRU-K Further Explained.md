
### How is LRU Not That Efficient

**Least Recently Used (LRU)** is a cache eviction policy that removes the least recently accessed item when the cache reaches its maximum capacity. The assumption is, if something is used recently, it is more probable to be used in the near future than something that was touched long ago.

However, LRU has notable drawbacks:

- **Burst Workloads with Unique Accesses**:  
    If the system experiences a burst of accesses to many unique items (e.g., a scan-heavy query), LRU will evict previously useful (or “hot”) items to make room. Even though those new items might only be accessed once, they remain in the cache longer than necessary because they were recently accessed.
    
- **Full Table Scans**:  
    Consider a query that performs a full table scan due to missing indexes. It brings in all pages of that table into memory. Even if the query runs only once, these pages — which are not necessarily important for future queries — occupy cache space and may evict more critical pages. LRU keeps them around just because they were accessed recently.
    

> **The core limitation**: LRU tracks **recency** but not **frequency** — meaning it doesn’t distinguish between a page accessed once and one accessed repeatedly.

### So Frequency Cache Would Fix It ? ...Right ?

![[Pasted image 20250504134349.png|center|300]]

**Least Frequently Used (LFU)** is another eviction policy that removes the item accessed the fewest number of times. It introduces a usage count for each item:

- **Frequency Tracking**: Each time an object is accessed, its counter is incremented.
    
- **Eviction**: When the cache is full, the object with the lowest count is removed.
    

While LFU addresses the frequency issue, it also has its downsides:

1. **Cold Start Problem**:  
    New items begin with a frequency count of zero. If eviction happens quickly, they might be removed before proving their worth — even if they were about to become hot.
    
2. **Stale Hot Items**:  
    LFU may keep items that were once frequently accessed but are no longer relevant. Since it ignores recency, such items might waste space despite no longer being useful.
    

> **The core limitation**: LFU tracks **frequency** but ignores **recency**.

![[Pasted image 20250504134209.png|center|300]]



### And Here Comes Our Hero: **LRU-K**  

**LRU-K** is a cache replacement algorithm that extends **Least Recently Used (LRU)** by tracking the **K most recent accesses** to each page (or item), not just the last one.

The idea is to use the _K-th most recent access time_ (rather than the most recent) to determine which item to evict. This gives a better picture of how often and how consistently an item is accessed.

##### Algorithm

1. **Track Access History**:
    - For each page/item, LRU-K keeps a list of the **K most recent timestamps** when it was accessed. This means we don’t just remember the last time it was used, but a history of uses.
    
2. **Eviction Policy**:
    - When the cache is full, LRU-K selects for eviction the item whose **K-th most recent access** is the **oldest**. **Items with fewer than K accesses are _not eligible for eviction_** under the main LRU-K policy. 
    
	-  But if the cache is full and we have many items with `< k` accesses, they can be evicted using simpler policy like FIFO or basic LRU.
        
3. **Queue-Based Implementation (Typical Design)**:
	- **History Queue**:
	    - Holds items that have been accessed fewer than **K times**. These items are still “cold” and not yet proven to be useful.
        
	- **Cache Queue**:
	    - Holds items that have been accessed at least **K times**. These are promoted from the history queue after reaching the K-access threshold.
##### Example (for LRU-2)

Assume K = 2 (this is common in practice).

| Page | Access Timestamps | K-th Access         |
| ---- | ----------------- | ------------------- |
| A    | 5, 20             | 5                   |
| B    | 10, 30            | 10                  |
| C    | 25                | N/A (only 1 access) |

To evict a page, LRU-2 would choose the one with the **oldest 2nd access** — here, Page A (5).  
Page C isn't even in the candidate list yet since it has only been accessed once.

