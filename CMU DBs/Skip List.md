The easiest way to implement a dynamic, order-preserving index is with a **sorted linked list**. However, all operations in a basic linked list require **linear search**, which can be inefficient.

To reduce this overhead, we can introduce a **secondary level**—a linked list that _skips_ every other key. This idea can be extended by adding **multiple levels** of skip links, each skipping more elements than the one below. 

This forms the basis of a **skip list**, allowing for much faster search, insertion, and deletion operations while preserving order. It's mostly used for in-memory data structures. 

Approximate `O(log(n))` search times

![[Pasted image 20250524000117.png|center]] 

### Inserts 

To insert a new key into a skip list, we first determine how many levels the new node will span. This is typically done by flipping a coin (or using `randint`) and counting how many times the result is greater than 0.5—this count determines the height of the new node.

Insertion is performed **bottom-up**. At each level, we locate the correct position for the new key, then adjust the **left, right, and up pointers** to maintain structural integrity. This is crucial for thread safety: if a concurrent reader accesses a node during insertion and finds that it points to a higher level but not yet to a lower one, it could lead to inconsistent reads.

If the skip list is **singly linked** (i.e., forward-only pointers), each level's insertion can be performed using an **atomic pointer swap** (e.g., atomically updating K4's `next` pointer to point to K5). In this case, no locking (latch) is required, enabling efficient concurrent insertions.

### Search 

Go to the top-level linked list and traverse until the value is about to be greater than the target. Then go down to the next level and traverse the same way until it reaches the bottom list and then the target key.

![[Pasted image 20250524003555.png | center]]
