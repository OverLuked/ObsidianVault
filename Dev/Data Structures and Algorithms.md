## Data Structures & Algorithms
*Foundation for writing efficient code and a cornerstone of technical interviews*

### Big-O Notation
*Describes how runtime/memory grows as input size grows*

##### Common Complexities (best to worst)

| Notation    | Name          | Example                              |
| ------------ | --------------- | --------------------------------------- |
| O(1)         | Constant        | Array index access                     |
| O(log n)     | Logarithmic     | Binary search                          |
| O(n)         | Linear          | Single loop through an array           |
| O(n log n)   | Linearithmic    | Merge sort, quick sort (average case)  |
| O(n^2)       | Quadratic       | Nested loops, bubble sort              |
| O(2^n)       | Exponential     | Naive recursive Fibonacci              |
| O(n!)        | Factorial       | Generating all permutations            |

##### Rules of Thumb
- Drop constants -> O(2n) becomes O(n)
- Drop non-dominant terms -> O(n^2 + n) becomes O(n^2)
- Different inputs get different variables -> O(a + b), not O(2n)

### Arrays
*Fixed or dynamic contiguous block of memory*
- Access: O(1)
- Search: O(n)
- Insert/Delete at the end: O(1) amortized
- Insert/Delete at the start/middle: O(n) - requires shifting elements

### Linked Lists
*Nodes connected via pointers, not contiguous memory*
##### Singly Linked List
- Each node points to the next node
##### Doubly Linked List
- Each node points to both the next and previous node
##### Complexity
- Access: O(n)
- Insert/Delete at a known node: O(1)
- Search: O(n)
##### vs Arrays

| Aspect              | Array         | Linked List                    |
| --------------------- | --------------- | --------------------------------- |
| Access                | O(1)            | O(n)                              |
| Insert/Delete at start | O(n)           | O(1)                              |
| Memory                | Contiguous      | Scattered, extra pointer overhead |
| Cache locality        | Good            | Poor                              |

### Stacks & Queues
##### Stack
*LIFO - Last In, First Out*
- Operations: push, pop, peek - all O(1)
- Use cases: undo/redo, the call stack, expression evaluation, backtracking
##### Queue
*FIFO - First In, First Out*
- Operations: enqueue, dequeue - O(1)
- Use cases: task scheduling, breadth-first search, print queues
##### Deque
- Double-ended queue - insertion/removal from both ends in O(1)

### Hash Tables
*Key-value storage using a hash function to map keys to indices*
- Average case: O(1) for insert, delete, lookup
- Worst case: O(n) when many collisions occur
##### Collision Handling
- Chaining -> each bucket holds a linked list of entries
- Open Addressing -> probe for the next open slot

### Trees
##### Binary Tree
- Each node has at most 2 children
##### Binary Search Tree (BST)
- Left subtree < node < right subtree
- Search/Insert/Delete: O(log n) average, O(n) worst case (unbalanced)
##### Balanced Trees
- AVL Tree, Red-Black Tree - self-balancing, guarantee O(log n)
##### Tree Traversal
- In-order (Left, Root, Right) -> visits a BST in sorted order
- Pre-order (Root, Left, Right)
- Post-order (Left, Right, Root)
- Level-order (Breadth-First)

### Heaps
*Complete binary tree that maintains a heap property*
- Min-Heap -> parent <= children
- Max-Heap -> parent >= children
- Insert/Extract: O(log n)
- Use cases: priority queues, scheduling, Top-K problems

### Graphs
*Nodes (vertices) connected by edges*
##### Representations
- Adjacency Matrix -> O(V^2) space, O(1) edge lookup
- Adjacency List -> O(V + E) space, better for sparse graphs
##### Traversal
- BFS (Breadth-First Search) -> uses a queue, finds shortest path in unweighted graphs
- DFS (Depth-First Search) -> uses a stack/recursion, good for exploring all paths
##### Common Algorithms
- Dijkstra's -> shortest path with non-negative weights
- A* -> shortest path guided by a heuristic
- Topological Sort -> valid ordering of a DAG (Directed Acyclic Graph)

### Sorting Algorithms

| Algorithm       | Best         | Average       | Worst         | Space      | Stable |
| ----------------- | -------------- | --------------- | --------------- | ------------ | -------- |
| Bubble Sort        | O(n)           | O(n^2)          | O(n^2)          | O(1)         | Yes      |
| Insertion Sort     | O(n)           | O(n^2)          | O(n^2)          | O(1)         | Yes      |
| Merge Sort         | O(n log n)     | O(n log n)      | O(n log n)      | O(n)         | Yes      |
| Quick Sort         | O(n log n)     | O(n log n)      | O(n^2)          | O(log n)     | No       |
| Heap Sort          | O(n log n)     | O(n log n)      | O(n log n)      | O(1)         | No       |

### Searching Algorithms
##### Linear Search
- Checks each element in sequence - O(n)
##### Binary Search
- Repeatedly halves a sorted search space - O(log n)
- Requires sorted input

### Common Interview Problem Patterns
- Two Pointers -> sorted arrays, pair-sum problems
- Sliding Window -> subarrays/substrings with a running condition
- Fast & Slow Pointers -> cycle detection in linked lists
- Recursion & Backtracking -> permutations, combinations, N-Queens
- Dynamic Programming -> optimal substructure + overlapping subproblems (memoization/tabulation)
- Divide & Conquer -> break into subproblems, solve, combine (merge sort, binary search)
