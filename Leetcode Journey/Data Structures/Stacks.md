A stack supports a subset of operations from dynamic arrays can can be thought of as a vertical dynamic array

A good analogy for stacks is you can think of it as stacking plates in a vertical container. You can only add or remove a plate from the top of the stack and not anywhere else in the container

A stack operates on a LIFO basis which means "Last in First out". This means the last element you add to the stack is the first one that comes out

![[Pasted image 20250125142239.png]]

### Pop
The pop operation removes the last element (top element) from the stack
```python
def pop(self):
    return self.stack.pop()
```

![[Pasted image 20250125142419.png]]
### Peek
The peek operation simply returns what the top element is without actually removing it
```python
def peek(self):
    return self.stack[-1]
```

### Push
The push operation will append the element to the top of the stack
```python
def push(self, n):
    # using the pushback function from dynamic arrays to add to the stack
    self.stack.append(n)
```

![[Pasted image 20250125142607.png]]

##### Advantages
- Ideal for problems where the most recently added item should be the first to be removed
	- Reverse strings
	- Keeping track of function calls
	- Evaluating expressions
##### Disadvantages
- Limited access so you can only access the last inserted element of the stack and can't index for other elements
##### When to use
- Use when the Last In First Out order is required
	- Good for backtracking in DFS
- When you need efficient push and pop operations

#### Time complexity
![[Pasted image 20250125142742.png]]