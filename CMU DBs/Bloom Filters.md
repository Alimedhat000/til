
### Introduction 

**Filters**—specialized data structures that answer **set membership queries**. Filters quickly determine whether a key is _likely_ in a set. A well-known example of this is the **Bloom Filter**.

### Bloom Filters

A probabilistic data structure that answers set membership queries with high efficiency. It may produce **false positives** (indicating a key is present when it is not) but **never false negatives** (missing a key that is actually in the set). 

- `Insert(x)`: Use `k` hash functions to set bits in the filter to 1. 
- `LookUp(x)`: Check whether the bits are 1 for each hash function.

At its core, a **Bloom Filter** is just a bitmap. To **insert** an element, we compute two hash functions of the element, take each hash modulo the number of bits, and set the bits at those positions to 1 (if a bit is already set, it remains unchanged).

To **lookup** an element, we compute the same hashes, check the corresponding bits in the bitmap, and if **all** are set to 1, the element is _likely_ in the set; if **any** bit is 0, the element is definitely not in the set.

![[9v08gf.gif|center|600]]

- Larger bitmap size $m$ → lower false positive rate, but more memory used.
    
- More hash functions $k$ → more accuracy, but slower insertions and lookups.

##### Bitmap Size Formula 

The optimal size of the bitmap (in bits) is given by:

$$
m = -\frac{n.ln(p)}{(ln2)^2}
$$

Where: 
- $m$ =number of bits in bitmap.
- $n$ = number of elements expected to be inserted
- $p$ = desired false positive rate (e.g., 0.01 for 1%)

##### Number of Hash Functions

Once you have $m$, the optimal number of hash functions $k$ is:

$$
k  = \frac{m}{n}\space ln2
$$

##### Other Filters Variants
 
 - **Counting Bloom Filter** : 
	 - A variant of the standard Bloom Filter that supports **deletions**. Instead of a simple bitmap, it uses an array of counters (small integers). 
	 - This allows dynamic updates, but uses more memory than a standard Bloom Filter.
		 - When inserting, the counters at the hash positions are incremented.
			 
		 - When deleting, the counters are decremented.
			 
		 - An element is considered present if all corresponding counters are greater than zero. 
	
 - **Cuckoo Filter** :
	 - An alternative to Bloom Filters that also supports **insertions and deletions** efficiently.
	 
	 - Based on **Cuckoo Hashing**, it stores **fingerprints** of elements in a hash table with multiple possible positions.
	 
	 - If all candidate positions are full, it "kicks out" existing fingerprints to make room (like a cuckoo bird displacing eggs).
	 
	- It generally offers **lower false positive rates** and better space efficiency than Bloom Filters for some workloads.
	 
 
 - **Succinct Range Filter (SuRF)**:
	 - A filter designed specifically for **range queries** (e.g., “Is there any key between X and Y?”), which traditional Bloom Filters cannot efficiently handle.
	 
	 - Uses a **compressed trie structure** to compactly represent keys.
	 
	 - Supports approximate membership testing over key ranges with tunable false positive rates. 
	 
	 - Ideal for applications like key-value stores where range queries are common. 