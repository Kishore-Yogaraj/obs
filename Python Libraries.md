### What is a python library?
- A library is a collection of books or is a room of place where many books are stored to be used later
- In the programming world, a library is a collection of precompiled codes that can be used later on in a program for some specific well-defined operations
- A python library is a collection of related modules
	- Contains bundles of code that can be used repeatedly in different programs
	- It makes Python Programming simpler and convenient for the programmer 

### Example Use of Python Library

```python
# Importing math library
import math

A = 16
print(math.sqrt(A))

# Output: 4.0
```
- Here in the above code, we imported the math library and used one of its methods i.e. sqrt (square root) without writing the actual code to calculate the square root of a number. That’s how a library makes the programmers’ job easier. But here we needed only the sqrt method of math library, but we imported the whole library. Instead of this, we can also import specific items from a library module.


```python
# Importing specific items
from math import sqrt, sin

A = 16
B = 3.14
print(sqrt(A))
print(sin(B))

# Output: 4.0
# Output: 0.0015926529164868282
```
- In the previous code we imported the entire library to use one of its methods
- We could have just imported "sqrt" from the math library
- Python allows us to import specific items from a library