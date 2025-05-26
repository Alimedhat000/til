 
A **B+ Tree** does not indicate whether a child exists beneath an inner node, so to confirm that a key **does not exist**, the search must always continue down to the **leaf level**. This incurs **one buffer pool page miss per tree level**, even for failed lookups.

In contrast, a **trie** is an order-preserving data structure that stores keys as sequences of **digits** (typically one byte each). For example, strings can use characters as digits, while other data types may use bits. 

These digits form a **tree structure** representing the **prefixes** of all inserted keys. The time complexity of operations in a trie depends on the **length of the key**, not the total number of keys.

![[Pasted image 20250524231101.png|center|500]]


The **span** of each trie level is the number of bits that each digit represent. If the digit and its prefix exist in the corpus, then a pointer to the next level node is stored at that digit, otherwise, a null is stored.

This determines the **Fan-out** of each node and the physical **height** of the tree.
