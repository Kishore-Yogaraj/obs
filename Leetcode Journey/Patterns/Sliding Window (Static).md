### When to use sliding windows
- Use this for linear data structures
	- Arrays
	- Lists
	- Strings
- You can use this when you need to find the **shortest, longest, min, max** of a **subarray**
- If the subarray must **satisfy some condition**

### Why do we need to use Sliding Window
Let's look at a problem where we have to find the longest substring without dupes![[Pasted image 20250309183639.png]]

If we don't have the sliding window and we want to find the longest substring without duplicates we would have to use a double for loop. We start our i and j pointers at the first a and move j until we find the duplicate. If we find the duplicate we move the i pointer to the next letter which is b and move j until we find the duplicate for the letter that i is currently on.

![[Pasted image 20250309184131.png]]

### Dynamic Sliding Window

With the **dynamic sliding window technique (the window sizes changes)** we start with two pointers at the first letter. Then we move j until the condition becomes invalid. In our case the condition is that i and j can not equal the same value.

We then keep a tab that let's us know the most recent index of the letter we just found  whether that be with the i pointer or the j pointer. 

We then move the i pointer until our condition becomes true again and then continue moving j. You would repeat this process until j reaches the end of the array. You need to make sure you keep track of the last index you saw a specific letter so you know what to return for the longest substring:

![[Pasted image 20250309184811.png]]

### Fixed Sliding Window
In this example we will attempt to find the max subarray sum of size 3
![[Pasted image 20250309195856.png]]

Here we can just initialize a subarray of size 2 with the 2 pointers and then move the sub array across the array and compare the sums to see which one results in us getting the largest sum.

Here we keep track of the sums and return the max sum.

```python
"""
A generic template for dynamic sliding window finding min window length
"""
def shortest_window(nums, condition):
    i = 0
    min_length = float('inf')
    result = None

    for j in range(len(nums)):
        # Expand the window
        # Add nums[j] to the current window logic

        # Shrink window as long as the condition is met
        while condition():  
            # Update the result if the current window is smaller
            if j - i + 1 < min_length:
                min_length = j - i + 1
                # Add business logic to update result

            # Shrink the window from the left
            # Remove nums[i] from the current window logic
            i += 1

    return result

"""
A generic template for dynamic sliding window finding max window length
"""
def longest_window(nums, condition):
    i = 0
    max_length = 0
    result = None

    for j in range(len(nums)):
        # Expand the window
        # Add nums[j] to the current window logic

        # Shrink the window if the condition is violated
        while not condition():  
            # Shrink the window from the left
            # Remove nums[i] from the current window logic
            i += 1

        # Update the result if the current window is larger
        if j - i + 1 > max_length:
            max_length = j - i + 1
            # Add business logic to update result

    return result

"""
A generic template for sliding window of fixed size
"""
def window_fixed_size(nums, k):
    i = 0
    result = None

    for j in range(len(nums)):
        # Expand the window
        # Add nums[j] to the current window logic

        # Ensure window has size of K
        if (j - i + 1) < k:
            continue

        # Update Result
        # Remove nums[i] from window
        # increment i to maintain fixed window size of length k
        i += 1

    return result
		
```

Here is the base code for fixed sliding window questions
```python
def duplicateClose(nums, k):
	window = set()
	L = 0

	for R in range(len(nums)):
		if R - L + 1 > k
			window.remove(nums[L])
			L += 1
		if nums[R] in window
			return True
		window.add(nums[R])
	return False
```


### Every Case for Sliding Windows
```python
def duplicatClose(nums, k):
    window = set()
    L = 0
    
    for R in range(len(nums)):
        if R - L + 1 > k:
            window.remove(nums[L])
            L += 1
        if nums[R] in window:
            return True
        window.add(nums[R])
    return False
    
def longestSubArray(nums):
    L = 0
    length = 0
    
    for R in range(len(nums)):
        if nums[L] != nums[R]:
            L = R
        length = max(length, R - L + 1)
    return length
    
def minSubaArray(nums, target):
    L, total = 0, 0 
    length = float("inf")
    
    for R in range(len(nums)):
        total += nums[R]
        while total >= target:
            length = min(length, R - L + 1)
            total -= nums[L]
            L += 1
    return 0 if length == float("inf") else length
```