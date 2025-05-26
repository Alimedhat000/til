### 1. `lru_k_replacer.c` & `lru_k_replacer.h`

Implement a LRU_K policy  

What I Did :

- Added several helper functions In`LRUKNode` :
	- `RecordAccess(size_t timestamp)`:
		Pushes a new timestamp to the `history_` array, If the `history_` is full (size > k) then remove the oldest timestamp i.e.`pop_front`.
	
	- `IsHistoryFull() const`: 
		returns a `boolian` $size \space \ge \space k$
	
	- `GetLastAccess() const` :
		returns the newest timestamp
	
	- `GetKDistance(size_t current_timestamp)`:
		returns `infinity` if history is full else returns the difference between the newest timestamp and `current_timestamp` 
	
	- `is_evictable`: 
		This acts as the Pin discussed before making the current Node not evictable.
	
- In `LRUKReplacer`:
	- `Evict()` 
		this does two passes on the `node_store`:
		- First search frames with less than `K` accesses and evicts the newest one if found
		-  If no frames with less than k accesses, find largest k-distance and evicts it
	
	- `RecordAccess(frame_id, access_type)`
		if the frame does not exist create one first and add to `node_store`, then add new timestamp to it
	
	- `SetEvictable(frame_id, set_evictable)`
		sets the evictable level to the node with `frame_id` and increases the `evictable_count` or decrease it depending on `set_evictable` value.
	
	- `Remove(frame_id)`
		removes specified frame id , If Remove is called on a non-evictable frame, throw an exception or abort the process. also decrement `evictable_items` if removal is successful. 


### 2. `page_guard.cpp`

This was the most complicated bit for me but here's the summary of the functions

First of all, WHAT THE HECK IS `Write/ReadPageGuard` ?? 
Take a step back and relax, i once was in your position and truly understand your frustration.

Basically, The `page_guard.cpp` module implements two RAII-style guards: `ReadPageGuard` and `WritePageGuard`, which manage access to pages in the buffer pool. These guards ensure safe access patterns (read or write) by acquiring appropriate locks and managing the lifecycle of pinned pages.

#### WHO is RAII??

**RAII** stands for **Resource Acquisition Is Initialization**. It’s a programming idiom in C++ that ties resource management to object lifetime.

A **RAII-style guard** is a class whose constructor **acquires a resource**, and whose destructor **automatically releases it**. This ensures that resources like memory, file handles, mutex locks, etc., are **safely and automatically cleaned up** when the guard object goes out of scope—no manual cleanup is needed.

#### Back To Our Implementation  

##### **1. `ReadPageGuard` Class**

###### **1.1 Purpose**

The `ReadPageGuard` class provides read-only access to a page while ensuring that:

- The shared read lock is held during the guard’s lifetime, and the page remains pinned and is not evicted.

- Resources are automatically released when the guard is destroyed.

###### **1.2 Constructor**

```cpp
ReadPageGuard(page_id, frame, replacer, bpm_latch, disk_scheduler)
```

- Locks the page using a shared (`rwlatch_.lock_shared`) lock (Here used `lock_shared` because multiple queries can read the same frame at the same time), then marks the page/frame `valid` this ensures that it cannot be evicted.

###### **1.3 Move constructor**

The move constructors transfers ownership from one guard to another:

- Copies the internal state from the source guard.
    
- Moves pointers to shared resources (e.g., `frame_`, `replacer_`).
    
- Marks the source guard as **invalid**, preventing double-release or dangling resource access.

This allows guards to be passed or returned safely while preserving ownership semantics.
###### **1.4 Methods**

- `Flush()`: Safely flushes the page to disk via `DiskScheduler` using `std::promise`/`std::future` synchronization.
	Steps:
	1.  First check if the frame/page isn't valid assert.
	2. Acquire the `bpm_latch` to ensure thread safety from other DBMS classes.
	3. Create a promise to track the completion of the disk operation
	4. Schedule a write operation using `disk_scheduler`
	5. Wait for the promise to resolve.
	6. Then clear the dirty flag Yay !
    
- `Drop()`: Releases the read lock and decrements the pin count. If pin count drops to zero, the page becomes evictable.
	Steps:
	1. First check if the frame/page isn't valid assert.
	2. Now release the shared read lock we acquired in the constructor.
	3. Modify the pin count of the frame (need the `bpm_latch`)
	4. If the pin count drops to zero, mark the page as evictable via `replacer_->SetEvictable(...)`(LRU-K).
	5. Invalidate the guard by resetting all member fields to null or sentinel values.

---

#### **2. `WritePageGuard` Class**

###### **2.1 Purpose**

The `WritePageGuard` grants exclusive write access to a page:

- Acquires an **exclusive** lock using `rwlatch_.lock()`.
    
- Marks the page as dirty immediately.
    
- Handles eviction and cleanup similarly to `ReadPageGuard`.

###### **2.2 Constructor**

```cpp
WritePageGuard(page_id, frame, replacer, bpm_latch, disk_scheduler)
```

- Locks the page for **exclusive** access.
    
- Marks the page dirty to signal it needs flushing before eviction.

###### **2.3 Move Semantics**

Similar to `ReadPageGuard`, the move constructor:

- Transfers ownership of resources and state from the source guard.
    
- Marks the source as invalid to prevent resource duplication.
    
- Ensures that only one guard controls the page at a time.

###### **2.4 Methods**

- **`Flush()`**: Performs a thread-safe flush of dirty data to disk.
    Steps:
    1. Assert the guard is valid.    
    2. Lock `bpm_latch_` to prevent concurrent pin/eviction operations.
    3. Create a `std::promise<bool>` and obtain its `future`.
    4. Schedule a write via `disk_scheduler_`.
    5. Wait for the disk operation to complete.
    6. Reset the dirty flag.

- **`Drop()`**: Releases ownership and unpins the page.
    Steps:
    1. Return early if the guard is invalid.
    2. Unlock the exclusive write lock.
    3. Lock `bpm_latch_` and decrement `pin_count_`.
    4. If `pin_count_ == 0`, make the page evictable via the replacer.
    5. Reset all internal fields to mark the guard invalid.

---

### 2. `buffer_pool_manager.cpp` & `buffer_pool_manager.h`

ill copy this from chatgpt for now (sheet9 OS 😭)


Great — here's a detailed breakdown of **all the important functions and sections** from your `buffer_pool_manager.cpp` file, along with **clear summaries** for each of them, focused on how they contribute to the buffer pool system in BusTub.

---

## ✅ **Key Classes and Functions in `buffer_pool_manager.cpp`**

---

### ### 🔹 `FrameHeader::FrameHeader(frame_id_t frame_id)`

- **Purpose**: Constructor that initializes a frame’s metadata.
    
- **Key Actions**:
    
    - Stores frame ID.
        
    - Allocates a memory block (`data_`) the size of a page.
        
    - Calls `Reset()` to clear state.
        

---

### 🔹 `FrameHeader::GetData()` / `GetDataMut()`

- **Purpose**: Access page data.
    
- **Const version** returns a pointer to **immutable** data.
    
- **Mutable version** allows writing to the data.
    

---

### 🔹 `FrameHeader::Reset()`

- **Purpose**: Resets the frame’s state.
    
- **What It Does**:
    
    - Fills data with 0.
        
    - Resets pin count to 0.
        
    - Marks the page as **not dirty**.
        

---

### 🔹 `BufferPoolManager::BufferPoolManager(...)`

- **Purpose**: Constructor for buffer pool manager.
    
- **Key Actions**:
    
    - Initializes all internal components: frames, page table, free list, replacer, disk scheduler.
        
    - Allocates memory for all frames up front and pushes them into the free list.
        

---

### 🔹 `BufferPoolManager::~BufferPoolManager()`

- **Purpose**: Destructor.
    
- **What It Does**: Default cleanup (smart pointers handle memory).
    

---

### 🔹 `BufferPoolManager::Size()`

- **Purpose**: Returns the number of frames in the buffer pool.
    

---

### 🔹 `BufferPoolManager::NewPage()`

- **Purpose**: Allocates a new page.
    
- **Steps**:
    
    1. Acquires a free frame.
        
    2. Allocates a new `page_id`.
        
    3. Resets the frame.
        
    4. Adds to page table.
        
    5. Pins the frame and makes it non-evictable.
        

---

### 🔹 `BufferPoolManager::DeletePage(page_id_t page_id)`

- **Purpose**: Deletes a page from memory and disk.
    
- **Steps**:
    
    1. Checks if page exists in page table.
        
    2. If it's pinned, return `false`.
        
    3. If dirty, write it to disk before removal.
        
    4. Remove from page table and add frame back to free list.
        
    5. Remove from replacer and deallocate on disk.
        

---

### 🔹 `BufferPoolManager::CheckedWritePage(page_id_t page_id, AccessType access_type)`

- **Purpose**: Provides **exclusive write access** to a page.
    
- **Important Behavior**:
    
    - If page already in memory, increments pin count and prevents eviction.
        
    - If not:
        
        - Tries to find a free or evictable frame.
            
        - Loads the page into memory using disk scheduler.
            
        - Handles evicting dirty pages (if necessary).
            
        - Updates the page table, replacer, and frame metadata.
            

---

### (Not Fully Visible): `BufferPoolManager::FindAvailableFrame()`

- **Likely Purpose**: Helper function to either pop a frame from the free list or evict one using `LRUKReplacer`.
    

---

## 📦 Key Components Involved

|Component|Role|
|---|---|
|`FrameHeader`|Holds metadata and memory for one page frame.|
|`page_table_`|Maps `page_id` → `frame_id`|
|`free_frames_`|Holds IDs of available frames.|
|`replacer_`|Decides which pages to evict (uses LRU-K).|
|`disk_scheduler_`|Schedules async disk read/write operations.|
|`pin_count_`|Prevents eviction when non-zero (in use).|
|`is_dirty_`|Marks a page as modified (needs to be flushed to disk).|

---

Would you like a visual diagram or flowchart to illustrate how these functions interact during a `NewPage()` or `CheckedWritePage()` operation?