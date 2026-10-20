### Enter Commands
This is the command window
![[Pasted image 20261020150827.png]]

This is the workspace
![[Pasted image 20261020150845.png]]

Adding a semicolon to the end of a statement means MATLAB will execute the line but it wont show the result immediately

Pressing up allows you to run commands again

Variables in MATLAB
- Must have letters, numbers, numbers and underscores and is case sensitive

### Saving and Loading Variables
You can save variables currently in your workspace by typing:
```python
save <filename.mat>
```

The "clear" command clears your workspace


You can load variables into your workspace using:
```python
load <filename.mat>
```

You can type "clc" to clear the command window


You can save or load just a couple variables by specifying it in the save and load commands (k is the single variable you want):
```python
save <filename k>
load <filename k>
```

### Use Built-in Functions and Constants
Some built in function in MATLAB:
```python
pi #pi
sin() #sin
sqrt #sqaure root
```

How to display more decimals
```python
format long
x

form short
x
```

### MATLAB Desktop and Editor
The command window allows you to run single commands one time and is good for quick checks and calculation. Often you will have to run more than one command at a time and do it sequentially. The best way to do this is by starting a "new live script"
![[Pasted image 20261020153333.png]]

### Vectors and Matrices
![[Pasted image 20261020154113.png]]

Row Vector
```python
x = [7 9]
# x = [7 9]
```

Column Vector
```python
x = [7;9]
# x = [7
#      9]
```

Combining rows and matrices
```python
c = [5 6 7;8 9 10]
```
![[Pasted image 20261020154622.png]]

Performing calculations inside arrays and matrices
```python
d = [sqrt(10) pi^2]
```

Creating evenly spaced arrays
```python
y = 1:5
```
![[Pasted image 20261020155007.png]]

Specifying spacing
```python
z = 1:0.5:5
```
![[Pasted image 20261020155147.png]]

If you know the number of elements you want in each array instead of spacing you can using the "linspace function"
```python
linspace(firstnumber,lastnumber,numberofelements)
b = linspace(1,10,5)
```

Transposing a vector (row vector to column vector and vice versa)
```python
b = b'
```

Transposing in a single command
```python
c = (5:2:9)'
```

Creating random matrices (square matrix)
```python
x = rand(5)
# This creates a 5 by 5 random matrix
```

Create random matrices (rectangular matrix)
```python
x = rand(2,3)
# This creates a matrix with 2 rows and 3 columns
```

Create matrices of zeros and ones
```python
x = ones(6,3)
z = zeros(6,3)
# This creates a matrix with 6 rows and 3 columns of ones and zeros
```
![[Pasted image 20261020160245.png]]

Checking size of matrix
```python
size(x)
```

Creating random matrix the same size as another matrix
```python
rand(size(x))
```

### Indexing
Referring to elements in an array is called indexing. Indexing is how you extract and modify elements in an array.

Vectors only need one index in order to extract a value
```python
x = [3 7 5]
x(1) # This will give you 3
```

Matrices need a row and column value
```python
x = [3 7 5;4 6 7]
y = x(1,2) # This would give you 7
```

Extracting all row or all column
```python
y = x(1,:) # This would give you all the values in row 1
z = x(:,1) # This would give you all the values in column 1
```

Extracting last element in vector or matrix
```python
a = x(end)
```

Extracting second to last value in vector or matrix
```python
a = x(end-1)
```

You can also used end to extract the end row or end column
Extracting last element in vector or matrix
```python
a = data(end,3) # Will extract the the element in the last row in the 3rd column
```

You can use variables to index an element

If you use one index to try and grab an element in a matrix it will traverse downwards in column order:
![[Pasted image 20261020162051.png]]

Selecting the last 2 columns of a matrix
![[Pasted image 20261020162437.png]]

![[Pasted image 20261020162447.png]]

Picking specific elements in a vector or matrix
```python
x = density([1 3 6]) # This would give you the 1st, 4rd and 6th element in a vector or matrix
```

Changing elements in a matrix
Picking specific elements in a vector or matrix
```python
x(1) = x(2) # Changes the first element in the matrix to be the second element in the matrix
```

You can apply mathematical operations to all elements within a matrix
Picking specific elements in a vector or matrix
```python
r = v1 + 1
```
![[Pasted image 20261020163608.png]]

You can add any two vectors that are the same size
```python
vs = v1 + v2
```
![[Pasted image 20261020163721.png]]

Finding the max value in a vector
same size
```python
vm = max(va)
```
![[Pasted image 20261020163858.png]]

Functions like "sqrt" and "round" can be applied to all the elements within a matrix as well

Using the * operator will perform matrix multiplication but matrices of the same size can not be multiplied with matrix multiplication. Instead we use the .* operation which will do element wise multiplication.
```python
mass = density .* va
```

![[Pasted image 20261020164330.png]]

Requesting multiple and individual outputs from function calls
```python
[dr, dc] = size(data)
```
![[Pasted image 20261020170753.png]]

Finding the max in a vector and the index of the vector
```python
[vMax, ivMax] = max(v2)
```

![[Pasted image 20261020171059.png]]

Find just how many columns there are in a matrix
```python
[~, datacol] = size(data)
```
![[Pasted image 20261020171249.png]]

### Plotting
Plotting two vectors of the same size against each other

```python
plot(sample, mass1) #(x-axis, y-axis)
```

![[Pasted image 20261020171907.png]]

You can specify color and line style using the following (refer to line styles for different ways to make plots):
```python
plot(sample, mass2, "r*") # x-axis, y-axis, line style
```

![[Pasted image 20261020172059.png]]

Creating a plot on top of another plot in the same plot
```python
plot(sample, mass1, "r*")
hold on
plot(sample, mass2, "ms")
```
![[Pasted image 20261020172353.png]]

While hold is on, all plots created with be laid on top of one another. Make sure to type in hold off if you want plots to not be laid on top of one another.

```python
hold off
```

When plotting a single vector, MATLAB will use the vector values as the y axis and the indices as the x axis

```python
plot(v1)
```
![[Pasted image 20261020172642.png]]

You can annotate plots using annotation functions. An example is "title" which adds a title to a plot. More examples below:

```python
title("Enter a title")
ylabel("Enter y axis label")
xlable("Enter x axis label")
legend("Enter a legend name, Enter a legend name") # Adjust as needed after inputting beacuse it will randomly assing a legend
```


### Working with Imported Data

To assign data from a table into a variable we do the following
```python
#An Example
var = tableName.variableName

d = elements.density
```
![[Pasted image 20261020174644.png]]

You can also add values to an existing table so that you can keep all the data in the same place

```python
elements.Mass = elements.density .* elements.Volume1
```

Below is the below and after of the tables
![[Pasted image 20261020174932.png]]
![[Pasted image 20261020174939.png]]

You can also interact with a table directly and re organize the tables by changing the way its sorted by clicking the mass drop down
![[Pasted image 20261020175113.png]]

You can use regular array indexing with tables as shown below. Notice that it ends up creating a new table (This gives you all the columns from rows 1 to 3):
![[Pasted image 20261020175314.png]]

### Relational Operators
Relational operators like >, <, == and ~= (not equal to) will return either a 1 (true) or 0 (false)

You can test to see if elements in an array are less than, greater than, equal to or not equal to using relational operators. The result will be an array of the same size with the elements of this array showing true or false

```python
y = v1 < 4 # v1 is an array
```
![[Pasted image 20261020175903.png]]

You can combine relational operators and logical comparisons using the logical operations "&" and "|" (or). Finding values that are greater than 10 and less 20 
```python
x = sample > 10 & sample < 20
```

### Logical Indexing
You can also using logical operators to do indexing. Lets say you have an array and you want to extract all the elements in that array that are greater than 6. You would do the following:

First you create a variable that is the same size as your array of interest and the elements show either 0 or 1 depending on if the element is greater than 6
![[Pasted image 20261020180736.png]]

```python
isGreater = v1 > 6
```
![[Pasted image 20261020180803.png]]

After this you can create a new variable that extracts the elements that are 1 and it will create a new array

```python
x = v1(isGreater)
```
![[Pasted image 20261020180905.png]]

You can also use indexing with multiple arrays. This one is interesting. Below is an example of how you can create an array that that will contain the elements of one array depending on the index of elements in the second array that follow the logical comparison
```python
z = sample(v1 < 6)
```
![[Pasted image 20261020181411.png]]

You can see above that the 2nd element and the 6th element were both greater than 6 so we extracted the indi