### What is Big O
- A way of analyzing how long it takes for an algorithm to execute (run time) as the input size grows
- We can expect as the input size grows, the execution time increases as well 

### O(1)
- O(1) is the most efficient algorithm 
- No matter how much the input size grows the time complexity will always stay the same
- The input size would be the size of the array or the size of HashMap
- No matter how many inputs there are it will take the same amount of time for each operation as shown below in the code block
![[Pasted image 20250122134130.png]]

```python
# O(1)
# Array
nums = [1, 2, 3]
nums.append(4)    # push to end
nums.pop()        # pop from end
nums[0]           # lookup
nums[1]
nums[2]


# HashMap / Set
hashMap = {}
hashMap["key"] = 10     # insert
print("key" in hashMap) # lookup
print(hashMap["key"])   # lookup
hashMap.pop("key")      # remove

```


### O(n)
- Linear growth
- As the input size grows, the time complexity is going to increase proportionally
- If we want to take the sum of sum array of numbers it will be a linear time algorithm because we're going to have to go through each number in the array and add it to the previous number
	- Under the hood it's a loop (looping is also a linear time algorithm)
- Inserting and removing at "ith" (middle) position in an array is also O(1) complexity because if we want to insert a number into the beginning of an array then we have to move the number in the 1st position in the array to the second position and second position to the 3rd position which means as the input size increase the time it takes increases as well
- Searching is also a linear time algorithm because we may have to search through each individual value in the array for the specific value we want (worst case)
- Monotonic stack and sliding windows are both O(n) time complexity
![[Pasted image 20250122134518.png]]
```python
# O(n)
nums = [1, 2, 3]
sum(nums)           # sum of array
for n in nums:      # looping
    print(n)

nums.insert(1, 100) # insert middle
nums.remove(100)    # remove middle
print(100 in nums)  # search

import heapq
heapq.heapify(nums) # build heap

# sometimes even nested loops can be O(n)
# (e.g. monotonic stack or sliding window)
```

### O(n^2)
- Nested loops are typically O(n^2) time complexity
- An example of this is a 2 dimensional array in which you need nested loops to access each element of the array
	- The outer loop will go through every row in the array and the inner loop will go through every element in that row
	- So the outer loop will access the first row, then the inner loop will access the elements in the first row then the outer loop will go to the second row and the inner loop will access all the elements in the second row
- Iterating through an array of size 4 where you iterate through the first 4 elements then the next 3 then 2 and so on is also of time complexity O(n^2)
![[Pasted image 20250122135431.png]]
```python
# Traverse a square grid
nums = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
for i in range(len(nums)):
    for j in range(len(nums[i])): 
        print(nums[i][j])


# Get every pair of elements in array
nums = [1, 2, 3]
for i in range(len(nums)):
    for j in range(i + 1, len(nums)):
        print(nums[i], nums[j])

# Insertion sort (insert in middle n times -> n^2)

```

### O(n x m)
- An example of this would be operation with a 2 dimensional array that is not a square
- n might be the number of rows and m might be the number of columns
![[Pasted image 20250122140806.png]]

```python
#O(n*m)
# Get every pair of elements from two arrays
nums1, nums2 = [1, 2, 3], [4, 5]
for i in range(len(nums1)):
    for j in range(len(nums2)):
        print(nums1[i], nums2[j])

# Traverse rectangle grid
nums = [[1, 2, 3], [4, 5, 6]]
for i in range(len(nums)):
    for j in range(len(nums[i])):
        print(nums[i][j])
```

### O (logn)
- Usually people just memorize what this time complexity is and what algorithms its applied to like binary search on an array or binary search on a BST
- Binary search on an array
	- On every iteration we eliminate half of the elements of each search
	- We keep cutting in half until we have nothing left
	- Given an array size n, how many times do we divide by 2 to get one
	- Or how many times can you multiply 1 by 2 to get 
	- Or 2^x = n
	- x = logn
	- The number of times you can take an array of size and divide by 2 is equal to log2n
	- Any time you cut something in half to search (binary search) you're essentially asking yourself the question: "How many times can I cut this in half until I have 1 result left" (the value you're searching for)
![[Pasted image 20250122144710.png]]

```python
# Binary search
nums = [1, 2, 3, 4, 5]
target = 6
l, r = 0, len(nums) - 1
while l <= r:
    m = (l + r) // 2
    if target < nums[m]:
        r = m - 1
    elif target > nums[m]:
        l = m + 1
    else:
        print(m)
        break

# Binary Search on BST
def search(root, target):
    if not root:
        return False
    if target < root.val:
        return search(root.left, target)
    elif target > root.val:
        return search(root.right, target)
    else: 
        return True

# Heap Push and Pop
import heapq
minHeap = []
heapq.heappush(minHeap, 5)
heapq.heappop(minHeap)

```

### O(nlogn)
- Only marginally less efficient than O(n)
- This is mostly for sorting
	- Anytime you're using a built in sort algorithm you can assume that its O(nlogn) time complexity
![[Pasted image 20250122145551.png]]

```python
# HeapSort
import heapq
nums = [1, 2, 3, 4, 5]
heapq.heapify(nums)     # O(n)
while nums:
    heapq.heappop(nums) # O(logn)

# MergeSort (and most built-in sorting functions)
```

### O(2^n)
- Most common when you have recursion or the Fibonacci sequence
![[Pasted image 20250122145859.png]]

```python
# Recursion, tree height n, two branches
def recursion(i, nums):
    if i == len(nums):
        return 0
    branch1 = recursion(i + 1, nums)
    branch2 = recursion(i + 2, nums)

```

### O(squroot(n))
- Rarely see this time complexity
- Commonly used to see how many factors there are of n
- Less of a coding problem
![[Pasted image 20250122150253.png]]

```python
# Get all factors of n
import math
n = 12
factors = set()
for i in range(1, int(math.sqrt(n)) + 1):
    if n % i == 0:
        factors.add(i)
        factors.add(n // i)

```

### O(n!)
- Calculating permutations or sometimes in graphing problems
- You just have to know that O(n!) is the worst case scenario
![[Pasted image 20250122150454.png]]

```python
# Permutations
def permute(nums):
    def backtrack(path, nums):
        if not nums:
            res.append(path)
            return
        for i in range(len(nums)):
            backtrack(path + [nums[i]], nums[:i] + nums[i + 1:])
    res = []
    backtrack([], nums)
    return res

```

![[Pasted image 20250122150822.png]]