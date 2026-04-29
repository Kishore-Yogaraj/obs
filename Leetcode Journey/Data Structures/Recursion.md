### One branch
Recursion is when a function calls itself with a different inputs. These functions are called **recursive functions**

Recursive functions can be thought of as breaking a problem down in to smaller sum problems and then solving them in reverse order.

Recursive functions have two parts:
- The base case (smallest value or end of the function)
- The recursive case (values after the smallest or end has been reached)

Calculating factorials of a number can be done recursively as shown below:
```python
# Recursive implementation of n! (n-factorial) calculation
def factorial(n):
    # Base case: n = 0 or 1
    if n <= 1:
        return 1

    # Recursive case: n! = n * (n - 1)!
    return n * factorial(n - 1)
```

#### Explanation
Let's analyze this. We have our two parts: the **base case** and the **recursive case** (the function calling itself).

When the code reaches the last line with the initial input of 5, we get: `5 * factorial(4)`, which starts executing the function again from line 1, only now with input 4, so we get `4 * factorial(3)` and then `3 * factorial(2)` and lastly `2 * factorial(1)` after which the base case is reached.

But what happens when the base case is reached? When the function is called with 1 as the input, 11 is returned, and now it can be multiplied by 2, which will result in 2, which is the answer to 2!. We have only solved the first sub-problem so far. Now, we compute `3 * factorial(2)`, which results in 6, then `4 * factorial(3)`, which is 24, and finally `5 * factorial(4)`, which is 120 - the final answer to 5!

The most important part is that when we trigger the base case, we move back "up" the recursion tree because now we have to "piece" together the answers to our sub-problems to get to the final solution.

![[Pasted image 20250126201751.png]]


> [!warning] Hardest Part
> You need a way to quickly identify recursion and wrap your head around how it works. Whether that be with diagrams or code itself. You have to find a way to visualize how to track and what stage the recursive function is in 

Below is my understanding of how to visualize recursive functions. There's not really a trick to it, it's just hard to keep track of everything going on every time you call a function. Just make sure to draw it out if you're lost about how the function goes backwards. Similar to how I did below for calculating factorials

![[7438.jpg]]

### Two Branch
Two branch is a more common occurrence of recursion as we have seen in problems like binary search trees and depth first search

We can solve the Fibonacci sequence using two branch recursion. Solving means to find the Fibonacci number at the nth terms of the entire sequence

Below is an explanation on the Fibonacci sequence and the base case as well as the recursive case:

The Fibonacci sequence is a set of numbers that starts with 00 and 11, and each subsequent number is the sum of the two preceding numbers. The sequence starts like this: 0,1,1,2,3,5,8,13,21,34,...0,1,1,2,3,5,8,13,21,34,....

Generally, the formula to calculate the nth fibonacci number is to sum the the two previous fibonacci numbers, i.e. the (n−1)(n−1)th and (n−2)(n−2)th fibonacci numbers.

More formally, we say that:

1. F(0)=0F(0)=0 and F(1)=1F(1)=1
2. F(n)=F(n−1)+F(n−2)F(n)=F(n−1)+F(n−2)

Part 1 is the base case, and part 2 is the recursive case. The Fibonacci sequence is a classic example of a recursive function:

The below formula is the recursive formula for the Fibonacci sequence, also known as the recurrence relation:

fib(n)=fib(n−1)+fib(n−2)fib(n)=fib(n−1)+fib(n−2)


Below is the code for the fibonacci sequence:

Notice how the function is called twice in the last line. This is what results in the tree like structure developed underneath the code. In BST and DFS we can also see their respective functions being called twice which is what results in the tree.

```python
# Recursive implementation to calculate the n-th Fibonacci number
def fibonacci(n):
    # Base case: n = 0 or 1
    if n <= 1:
        return n

    # Recursive case: fib(n) = fib(n - 1) + fib(n - 2)
    return fibonacci(n - 1) + fibonacci(n - 2)
```

![[Pasted image 20250126204427.png]]

Below is my visualization of two branch recursion with the fibonacci sequence:
![[57069.jpg]]

Time complexity for two branch recursion is O(2^n)