### What is it?
- Most often used in data science 
	- Quantitative analysis of large data sets is very needed for today's data scientist
- Python lacks the ability to analyze complicated data sets straight out of the box which is why we use the NumPy [[Python Libraries]]
- Used for data analysis and scientific computing
- Comes with a high level of mathematical functions

### Vector Addition - Arrays vs Lists
- Array can appear very similar to python lists
```python
[123] #Array
[1,2,3] #List
```

- A one dimensional or multidimensional array general consists of a homogeneous grid of values that are all for the same type, whereas lists would be the python equivalent of an array but are heterogeneous
- NumPy arrays allow vectorized operation such as element, wise, addition and multiplication, which is a lot more concise than how it would be done with Python lists.

- Without NumPy
```python
list_two = list(range(1,4))
list_three = list(range(1,4))
list_sum = []

for index in range(3):
# list_two[index] ** 2 - This is incorrect because we may be modifying the value of the index but we must make sure we assign it to the index as well
    list_two[index] = list_two[index] ** 2 # Correct because we assign the configured value to the index
    list_three[index] = list_three[index] ** 3
    list_sum.append(list_two[index] + list_three[index])
print(list_sum)
```

- With NumPy
```python
import numpy as np # Imports all submodules which can be accessed using alias np

array_two = np.arange(1,4) ** 2 #Raises every element in array to the power of 2
array_three = np.arange(1,4) ** 3
print (array_two + array_three)
```

```python
sample_array = np.array([1,2,3])
print(np.power(sample_array, 4)) #Raise all elements by a defined number for the power
print(np.negative(sample_array)) #Turn every element negative

print([1,2,3]) #List
print(np.array([1,2,3])) #Turns list into array

print(np.exp(sample_array)) #e^each element in the array
print(np.log(sample_array)) #log of each element
print(np.sin(sample_array)) #sin of each element
```

### Multidimensional Arrays
- Multidimensional arrays are all homogeneous
	- This means that that the items within the array are all of the same data type
- N dimension arrays are defined by shape and size
- Shape
	- 1D Array (n, ) - "n" is the number of elements in the array
	- 2D Array (n, m) "n" is the number of rows and "m" is the number of columns

**How to create a Multidimensional Array**
- "arange" is similar to "range" function
- arange creates an array and range creates a list with the specified range
	- np.arange(1,4) would be [1,2,3] 
	- 4 is exclusive
```python
import numpy as np

# All arrays create 1 row with 3 columns
x = np.arange(3) # creates 1 array ranging from 0 to 2
y = np.arange(3) # creates 1 array ranging from 0 to 2
z = np.arange(3) # creates 1 array ranging from 0 to 2

# Combining them will create a 3 x 3 array
multi_array = np.array([x, y, z])
print(multi_array)
print(multi_array.shape)
```

**Using linspace**
```python
b = np.linspace(1, 30, 3) #linspace gives use 3 evenly spaced numbers between 1 and 29
print(b)

c = np.arange(1,30, 3) # Gives us numbers from 1 to 29 with a step count of 3
print(c)

# 3 in linspace specifies number of elements in the range and 3 in arange specifies the step size
# in your range

#Output:
[ 1.  15.5 30. ]
[ 1  4  7 10 13 16 19 22 25 28]
```

**Indexing with Multi Dimensional Arrays**
```python
import numpy as np

# All arrays create 1 row with 3 columns
x = np.arange(3) # creates 1 array ranging from 0 to 2
y = np.arange(3) # creates 1 array ranging from 0 to 2
z = np.arange(3) # creates 1 array ranging from 0 to 2

# Combining them will create a 3 x 3 array
multi_array = np.array([x, y, z])
multi_array = np.array([x, y, z], dtype = np.int16) #Can specify data type with arrays (No output listed for this)
print(multi_array)
print(multi_array.shape)
multi_array[1, 2] # multi_array[n,m] - n indicates which row and m indicates which column we want to access

#Output
[[0 1 2]
 [3 4 5]
 [6 7 8]]
 (3, 3)
5 
 
```

### 1D Slicing
- How can we extract a range of elements from an array by slicing?
```python
[start stop step]
start - specifies index you want to start slice
stop - specifies index until which you want to stop slicing
step - spacing inbetween sequence of values

import numpy as np

x = np.arange(1, 10)
print(x)
print(x[2:7]) #Stop (7) index is exclusive
print(x[2:7:2]) #Step of 2
print(x[:7]) #Start at index 0 to index 6
print(x[2:]) #Start at index 0 to the last index

# Output
[1 2 3 4 5 6 7 8 9]
[3 4 5 6 7]
[3 5 7]
[1 2 3 4 5 6 7]
[3 4 5 6 7 8 9]
```

### Reshaping

```python
import numpy as np

x = np.arange(9).reshape(3,3) #New shape must define an array with the same total number of elements 
print(x)

y = np.arange(27).reshape(3,3,3) #(3 - number of individual arrays, 3 - rows, 3 - columns)
print(y)

#Output 1
[[0 1 2]
 [3 4 5]
 [6 7 8]]

# Output 2
[[[ 0  1  2]
  [ 3  4  5]
  [ 6  7  8]]

 [[ 9 10 11]
  [12 13 14]
  [15 16 17]]

 [[18 19 20]
  [21 22 23]
  [24 25 26]]]
```

### Multidimensional Slicing
**Remember when trying to extract values from arrays you are always referring to the index and always start at 0 
```python
import numpy as np

x = np.arange(18).reshape(3,2,3) 
print(x[1,1,1])

print(x[1, 0:2, 0:3]) #Select block(individual array), select range of rows, select range of column 

comparison_operation = x > 5
print(comparison_operation) #Prints array x with boolean values

print(x[comparison_operation]) #Prints single array with values that are true to the comparison operator

x.max() #grab max value in array
x.min() #grab min value in array

#Output 1
10

#Output 2
[[ 6  7  8]
 [ 9 10 11]]
 
#Output 3
[[[False False False]
  [False False False]]

 [[ True  True  True]
  [ True  True  True]]

 [[ True  True  True]
  [ True  True  True]]]
  
  #Output 4
  [ 6  7  8  9 10 11 12 13 14 15 16 17]
```
![[IMG_1572.jpg#center]]

### Manipulating Array Shapes

**ravel()**
```python
import numpy as np

x = np.arange(9).reshape(3,3) 
print(x)

ravelled_array = x.ravel() #Returns a view - modifying an element in the 'ravel array' would modify the element in the origianl array as well
ravelled_array[2] = 95
print(ravelled_array)
print(x)

#Output 1
[[0 1 2]
 [3 4 5]
 [6 7 8]]
 
 #Output 2
 [ 0  1 95  3  4  5  6  7  8]
 
 #Output 3
 [[ 0  1 95]
 [ 3  4  5]
 [ 6  7  8]]
```

**flatten()**
```python
flattened_array = x.flatten() #Returns a copy
flattened_array[2] = 95
print(flattened_array)
print(x)

# Output 1
[ 0  1 95  3  4  5  6  7  8]

#Output 2
[[0 1 2]
 [3 4 5]
 [6 7 8]]
```

**shape()**
```python
y = np.arange(9)
y.shape = [3,3]
print(y)

#Output 1
[[0 1 2]
 [3 4 5]
 [6 7 8]]

```

**transpose()**
```python
print(y.transpose()) #Flip over diagonal
print(y.T)

#Output 1 and 2
[[0 3 6]
 [1 4 7]
 [2 5 8]]

```

**resize()**
- If the new array is larger than the original array, the new array will add copies of the array that is being resized
- In the example below notice how the data is repeated to fill in the extra rows and columns added (repeat begins at index [1,3])
```python
print(np.resize(y, (6,6)))

#Output 1
[[0 1 2 3 4 5]
 [6 7 8 0 1 2]
 [3 4 5 6 7 8]
 [0 1 2 3 4 5]
 [6 7 8 0 1 2]
 [3 4 5 6 7 8]]
```

**zeros(), ones(), eye()**
```python
print(np.zeros((2,3), dtype=int))
print(np.ones((2,3), dtype=int))
print(np.eye((3), dtype=int))

#Output 1
[[0 0 0]
 [0 0 0]]
 
 #Output 2
 [[1 1 1]
 [1 1 1]]

#Output 3
[[1 0 0]
 [0 1 0]
 [0 0 1]]

```

**random.rand()**
```python
print(np.random.rand(4,4)) #Outputs uniform distribution between 0 and 1

#Output 1
[[0.52091275 0.96950946 0.35070463 0.74216131]
 [0.97217316 0.68949353 0.84002001 0.40612558]
 [0.72674025 0.8597018  0.1848122  0.30991404]
 [0.25443794 0.69006248 0.28087116 0.5813102 ]]
```

### Matrix Multiplication
- The product of Matrix A and B is defined only if the number of columns in Matrix A equals the number of rows in Matrix B
- Matrix is just a 2d numpy array that allows for different operations that otherwise couldn't be done on a regular 2d numpy array such as matrix multiplication

![[Pasted image 20240820170711.png#center]]


```python
import numpy as np
mat_a = np.matrix([0,3,5,5,5,2]).reshape(2,3) 
mat_b = np.matrix([3,4,3,-2,4,-2]).reshape(3,2)
print(mat_a * mat_b)
# Both print statements output same result
product = np.matmul(mat_a, mat_b)
print(product)

#Output 1
[[ 29 -16]
 [ 38   6]]
 
 #Output 2
 [[ 29 -16]
 [ 38   6]]

```

### Stacking
**Horizontally Stacking 2 Arrays**
- Both arrays must be of the same size in rows in order for arrays to be stacked horizontally
- Stacking concatenates the second dimension (columns)
```python
import numpy as np

x = np.arange(4).reshape(2,2)
print(x)

y = np.arange(4,8).reshape(2,2)
print(y)

z = np.hstack((x,y))
print(z)

#Output 1
[[0 1]
 [2 3]]
 
#Output 2
 [[4 5]
 [6 7]]
 
 #Output 3
 [[0 1 4 5]
 [2 3 6 7]]

```

**Vertically Stacking 2 Arrays**
- Both arrays must be of the same size in columns in order for arrays to be stacked vertically
- Stacking concatenates the first dimension (rows)
```python
import numpy as np

x = np.arange(8).reshape(4,2)
print(x)

y = np.arange(4,12).reshape(4,2)
print(y)

z = np.vstack((x,y))
print(z)

#Output 1
[[0 1]
 [2 3]
 [4 5]
 [6 7]]
 
 #Output 2
 [[ 4  5]
 [ 6  7]
 [ 8  9]
 [10 11]]
 
 #Output 3
 [[ 0  1]
 [ 2  3]
 [ 4  5]
 [ 6  7]
 [ 4  5]
 [ 6  7]
 [ 8  9]
 [10 11]]
 
 #This could be used as well for vstack
 w = np.concatenate((x,y), axis =0) #Axis specifies which dimension to use (row or col)
print(w)

```

**Depth Stacking**
- Stacking along the third access which is the depth of the array
- Essentially adds a column to each block

```python
x = np.arange(4).reshape(2,2,1) # 2 Blocks, 2 rows in each block, 1 cols in each block
print(x)
y = x*2
print(y)
depth_stack = np.dstack((x, y)) #dstack adds a column to each block
print(depth_stack)

#Output 1
[[[0]
  [1]]

 [[2]
  [3]]]

#Output 2
[[[0]
  [2]]

 [[4]
  [6]]]
  
#Output 3
[[[0 0]
  [1 2]]

 [[2 4]
  [3 6]]]
```

**Column Stacking**
```python
x = np.arange(4).reshape(2,2)
print(x)
y = x*2
print(y)
print(np.column_stack((x,y)))

#Output of column stack
[[0 1 0 2]
 [2 3 4 6]]
```

**Column Stacking 1-D Arrays**
- With 1D Arrays for column and horizontal stacking they stack differently then with multidimensional arrays
- column_stack() will turn the 1d arrays vertical and stack (increasing number of cols and rows)
- hstack() will increase number of columns
```python
x = np.arange(4)
y = x*2
print(np.column_stack((x,y)))
print(np.hstack((x,y)))

#Output 1
[[0 0]
 [1 2]
 [2 4]
 [3 6]]
 
 #Output 2
 [0 1 2 3 0 2 4 6]
```

**Row Stacking**
- Row stacking function the same as v stacking where the number of rows increase (increase in the first dimension)
```python
x = np.arange(4)
y = x*2
print(np.row_stack((x,y)))
print(np.vstack((x,y)))

#Output 1
[[0 1 2 3]
 [0 2 4 6]]

#Output 2
[[0 1 2 3]
 [0 2 4 6]]
```