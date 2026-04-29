Queues are very similar to stacks except they work on a FIFO basis meaning that it works in the First In First Out order. Queues are best implemented with linked lists

Queues can be viewed as bank lines where the first person in line is the person that gets served first.

Queues have two operations which are **Enqueue** and **Dequeue**

### Encqueue
This operation inserts an element to the end of the queue. Doing this with a singly linked list will operate in O(1) time complexity

```python
def enqueue(self, val):
    newNode = ListNode(val)

    # Queue is non-empty
    if self.right:
        self.right.next = newNode
        self.right = self.right.next
    # Queue is empty
    else:
        self.left = self.right = newNode
```

### Dequeue
This operation removes an element from the front of the queue and returns that element

```python
def dequeue(self):
    # Queue is empty
    if not self.left:
        return None
    
    # Remove left node and return value
    val = self.left.val
    self.left = self.left.next
    if not self.left:
        self.right = None
    return val
```

##### Advantages
- They execute tasks of elements in the exact order they are added making them ideal for managing tasks
- They are good for questions where you need to process elements sequentially
##### Disadvantages
- Don't allow for random access memory usage
- Searching for a specific element takes O(n) time which is why you would never actually perform searches with queues
##### When to use
- BFS uses queues to process nodes layer by layer
- Any problem that requires you to perform tasks on an element sequentially

#### Time Complexity
![[Pasted image 20250125144132.png]]