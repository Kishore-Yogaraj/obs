### Heap Properties
A heap is a specialized tree based data structure

It implements an abstract data type (like an integer, string, etc.) called **Priority Queue** but sometimes **Heap and Priority Queue** are used interchangeably

We learned that queues operate with a first-in-first-out basis but with a priority queue, the values removed based on a specific priority. The element with the highest priority is removed first, regardless of the order in which it was added

### Two Types of heaps
- Min Heap
- Max Heap

**Min heaps** have the smallest value at the root node. The smallest value has the highest priority to be removed
**Max heaps** have the largest value at the root node. The largest value has the highest priority to be removed

In this lesson we focus on **min heaps** but the implementation is the same for **max heap** except we would prioritize the maximum value instead of the minimum

### Heap Properties
For a binary tree to qualify as a heap, it must satisfy the following properties:

**Structure Property:**
A binary heap is a binary tree that is a **complete binary tree**, where every single level of the tree is filled completely, except possibly the lowest level nodes, which are filled contiguously from left to right.

**Order property:**
The order property for a min-heap is that all of the descendents should be greater than their ancestors. In other words, if we have a root with value `y`, every node in the right and the left sub-tree should have values greater than or equal to `y`. This is a recursive property, similar to binary search trees.

In a max-heap, every node in the right and the left sub-tree is smaller than or equal to `y`.

> [!Attention] Unlike binary search trees, heaps may contain duplicate values

![[Pasted image 20250309145807.png]]

Binary heaps are drawn using a tree data structure but under the hood, they are implemented using arrays. Let's show how we can do this by using the given binary heap: `[14,19,16,21,26,19,68,65,30,null,null,null,null,null,null]`

We will take an array of size n+1n+1 where nn is the number of nodes in our binary heap. This will make sense soon. We will visit our nodes in the same order as we visit nodes in breadth-first search - level by level, from left to right. We will insert these into our array in a contiguous fashion. However, we will start filling them from index `1` instead of `0`, for reasons we will discuss soon.

Once our array has been filled up, it would look like the following:
![[Pasted image 20250309145938.png]]

The reason why we start filling up our array from index `1` is because it helps us figure out the index at which a node's left child, right child, or the parent resides. Because binary heaps are complete binary trees, no space is required for pointers. Instead, a node's left child, right child and parent can be calculated using the following formulas, where ii is the index of a given node.

`leftChild` = 2∗i  
`rightChild` = 2∗i+1  
`parent` = i/2 (always round down)

Suppose we wanted to find the children and parent of the node with value `19`. The following visual demonstrates how using the formulas helps us figure them out.

The number above a node (in blue) is the corresponding index in the array of each node. It is important to note that these formulas only work when the tree is a complete binary tree and the array is filled contiguously from left to right.

We can also now appreciate why we start at index `1`. Suppose we wanted to find `14`'s left and right child and `14` was at `0`. Well, any number multiplied by a 00 is 00, and would tell us that the left child resides at the `0`th index, which is of course not the case.

![[Pasted image 20250309150317.png]]

**Code Implementation:**

```Python
class Heap:
    def __init__(self):
        self.heap = [0]
```

### Heaps Push and Pop
With heaps we can read the minimum or maximum value in constant time, O(1) by simply reading the element at the first position.

However, pushing and popping from a heap is more complicated, but can still be accomplished efficiently with a time complexity of O(log n).

### Push
Taking the same binary heap from before: `[14,19,16,21,26,19,68,65,30,null,null,null,null,null,null]`, let's say we wish to push `17`. We also need to make sure that we maintain our structure and order property.

Since a binary heap is a complete binary tree, and we are required to fill nodes in a contigious fashion, pushing `17` should happen at the 1010th index. However, this might violate the min heap property.

This can be resolved by bubbling or percolating the element up the tree until it reaches the correct position.

1. We start by comparing `17` with its parent, which is `26`.
2. We must ensure that all the descentants of every node are greater than the node itself. Since `17` is less than `26`, we swap the two.
3. We then compare `17` with its new parent, which is `19`.
4. Since `17` is less than `19`, we swap the two.
5. Lastly, we compare `17` with its new parent, which is `14`.
6. Since `17` is greater than `14`, we stop the percolation (also known as bubbling or shifting) process.

The resulting min-heap would look like the following.
![[Pasted image 20250309151737.png]]

**Code Implementation**
```python
def push(self, val):
    self.heap.append(val)
    i = len(self.heap) - 1

    # Percolate up
    while i > 1 and self.heap[i] < self.heap[i // 2]:
        tmp = self.heap[i]
        self.heap[i] = self.heap[i // 2]
        self.heap[i // 2] = tmp
        i = i // 2
```

### Heaps Push and Pop
With heaps we can read the minimum or maximum value in constant time, O(1) by simply reading the element at the first position.

However, pushing and popping from a heap is more complicated, but can still be accomplished efficiently with a time complexity of O(logn).

### Push
Taking the same binary heap from before: `[14,19,16,21,26,19,68,65,30,null,null,null,null,null,null]`, let's say we wish to push `17`. We also need to make sure that we maintain our structure and order property.

Since a binary heap is a complete binary tree, and we are required to fill nodes in a contigious fashion, pushing `17` should happen at the 1010th index. However, this might violate the min heap property.

This can be resolved by bubbling or percolating the element up the tree until it reaches the correct position.

1. We start by comparing `17` with its parent, which is `26`.
2. We must ensure that all the descentants of every node are greater than the node itself. Since `17` is less than `26`, we swap the two.
3. We then compare `17` with its new parent, which is `19`.
4. Since `17` is less than `19`, we swap the two.
5. Lastly, we compare `17` with its new parent, which is `14`.
6. Since `17` is greater than `14`, we stop the percolation (also known as bubbling or shifting) process.

The resulting min-heap would look like the following.
![[Pasted image 20250309152434.png]]

**Code Implementation**
```python
def push(self, val):
    self.heap.append(val)
    i = len(self.heap) - 1

    # Percolate up
    while i > 1 and self.heap[i] < self.heap[i // 2]:
        tmp = self.heap[i]
        self.heap[i] = self.heap[i // 2]
        self.heap[i // 2] = tmp
        i = i // 2
```

### Pop
Recall that when we pop from a heap, we are removing the value with the highest priority.

### The obvious way
Popping from a heap is more complicated than the push operation. One way that you might have already thought about is pop the root node and replace it with `min(left_child, right_child)`. The issue here is that while the order property is intact, we have violated the structure property. Taking the tree from before, popping `14`, and swapping it with `16` - `min(left_child, right_child)` would require `19` to replace `16`. Now, level 22 has a missing node i.e `19` is missing a left child.

### The correct way

The correct solution is very clever.
1. We read the root element since it is the element we wish to pop.
2. Next, we take the right-most node of the last level and swap it with the root node.
3. We have now maintained the structure property, but the order property is violated.
4. To fix the order property, we have to make sure that `30` finds its place.
5. To do so, we will continously swap `30` with `min(left_child, right_child)` until it reaches the correct position, i.e. both of it's children are greater than or equal to `30`.
6. We swap `30` with `16`, then `19` with `30`. The resulting tree will look like the following.

[https://youtu.be/OsNYMa0vtlo](https://youtu.be/OsNYMa0vtlo)

**Code Implementation**
```python
def pop(self):
    if len(self.heap) == 1:
        return None
    if len(self.heap) == 2:
        return self.heap.pop()

    res = self.heap[1]   
    # Move last value to root
    self.heap[1] = self.heap.pop()
    i = 1
    # Percolate down
    while 2 * i < len(self.heap):
        if (2 * i + 1 < len(self.heap) and 
        self.heap[2 * i + 1] < self.heap[2 * i] and 
        self.heap[i] > self.heap[2 * i + 1]):
            # Swap right child
            tmp = self.heap[i]
            self.heap[i] = self.heap[2 * i + 1]
            self.heap[2 * i + 1] = tmp
            i = 2 * i + 1
        elif self.heap[i] > self.heap[2 * i]:
            # Swap left child
            tmp = self.heap[i]
            self.heap[i] = self.heap[2 * i]
            self.heap[2 * i] = tmp
            i = 2 * i
        else:
            break
    return res
```

The pseudocode shown above might seem daunting at first so let's go over it. If our `heap` is empty, there is nothing to pop, hence the `return null`. Our heap also could have just one node, in which case, we will just pop that node and don't need to make any adjustments. If the above two statements have not executed, it must be the case that we have children, meaning we need to perform a swap.

We store our `14` into a variable called `res` so that we don't lose it. Then we can replace `30` to be at the root node.

Our while loop runs as long as we have a left child and we determine this by making sure `2 * i` is not out of bounds. Then, there are three cases we concern ourselves with:

1. The node has no children
2. The node _**only**_ has a left child
3. The node has two children

Because we are guaranteed to have a left child in the while loop, we need to now check if the node also has a right child, which we check by `2 * i + 1`. We also make sure that the current node is greater than its children because of the order property. We replace the node with the minimum of its two children.

If no right child exists and the current node's value is greater than its left child, we swap it with the left child.

If none of the above cases exectue, then it must be the case that our node is in the proper position already, satisfying both the order and the structural property.

### Heapify
Recall that to build a binary search tree with n elements, the time complexity is O(n log n), because each insertion takes O(log n) time and there are n elements to insert.

If we build our heap of size n by pushing each element one-by-one, this would also run in O(n log n) time. But there is actually a more efficient algorithm known as **heapify**, which allows us to perform this operation in O(n)) time.

### Implementation:
To build a heap we must satisfy the heap structure and order properties.

The structure property is trivial to satisfy because we can initialize an empty array and append elements to it. The order property is a bit more complex.

The order property states that the parent node must be less than or equal to its children. We can accomplish this by percolating each element down the tree until it reaches the correct position. Since the leaf nodes cannot be shifted any lower, we can skip the last level of the tree.

**Code Implementation:**
```python
def heapify(self, arr):
    # 0-th position is moved to the end
    arr.append(arr[0])

    self.heap = arr
    cur = (len(self.heap) - 1) // 2
    while cur > 0:
        # Percolate down
        i = cur
        while 2 * i < len(self.heap):
            if (2 * i + 1 < len(self.heap) and 
            self.heap[2 * i + 1] < self.heap[2 * i] and 
            self.heap[i] > self.heap[2 * i + 1]):
                # Swap right child
                tmp = self.heap[i]
                self.heap[i] = self.heap[2 * i + 1]
                self.heap[2 * i + 1] = tmp
                i = 2 * i + 1
            elif self.heap[i] > self.heap[2 * i]:
                # Swap left child
                tmp = self.heap[i]
                self.heap[i] = self.heap[2 * i]
                self.heap[2 * i] = tmp
                i = 2 * i
            else:
                break
        cur -= 1
```

1. We calculate the index of the first non-leaf node, which is n22n​, where nn is the number of elements in the heap.
2. Starting from the first non-leaf node, we will percolate down, the exact same way we did in the `pop()` function.
3. After each iteration, we are going to decrement the index by 11 so we can perform percolate down on the next non-leaf node.
4. We will continue this process until we reach the root node.

The visual below demonstrates `heapify()` being performed on all nodes starting from index 44. The nodes in the blue rectangles are leaf nodes.

![[Pasted image 20250309155459.png]]

