**Objective:**  
To compress data by assigning variable-length binary codes to input characters, with shorter codes assigned to more frequent characters.

### **Step-by-Step Algorithm**

1. **Input:**  
    A set of characters and their frequencies.  
    Example: `{A:5, B:9, C:12, D:13, E:16, F:45}`
    
2. **Create Leaf Nodes:**  
    For each character, create a leaf node containing the character and its frequency.
    
3. **Build a Min-Heap (Priority Queue):**  
    Insert all leaf nodes into a min-heap ordered by frequency.
    
4. **Build the Huffman Tree:**  
    Repeat until the heap contains only one node:
    
    - Remove the two nodes with the smallest frequencies from the heap.
        
    - Create a new internal node with these two nodes as children.
        
    - The new node's frequency is the sum of the two nodes' frequencies.
        
    - Insert the new node back into the heap.
        
5. **Assign Codes:**  
    Traverse the Huffman Tree:
    
    - Assign '0' for the left edge and '1' for the right edge.
        
    - Concatenate bits along the path to get the code for each character.
        
6. **Output:**  
    A mapping from characters to their Huffman codes.
    

```pseudocode
function HuffmanCoding(charFreq):
    create priorityQueue
    for each character in charFreq:
        create node and insert into priorityQueue

    while priorityQueue.size > 1:
        node1 = priorityQueue.extractMin()
        node2 = priorityQueue.extractMin()
        merged = new Node(freq=node1.freq + node2.freq)
        merged.left = node1
        merged.right = node2
        priorityQueue.insert(merged)

    root = priorityQueue.extractMin()
    assignCodes(root, "")

function assignCodes(node, code):
    if node is a leaf:
        save code for node.character
    else:
        assignCodes(node.left, code + "0")
        assignCodes(node.right, code + "1")
```