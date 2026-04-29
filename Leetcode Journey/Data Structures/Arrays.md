### Static Arrays
 In statically typed languages, arrays have an allocated size and type when they are initialized. This means the size of the array can not change. You can't add to this array and you can't make the array smaller. However, to remove an element from a static array, you are simply changing that element to 0 to indicate that it has been removed if you are removing a value from the end. When removing from an ith index other than the end of the array, you have shift your values down to ensure that the first element in the array is always filled.

#### Reading from an Array
To read an individual element from an array we can choose the position we want to access via an **index**. Below we have initialized an array of size `3` called `myArray`. We also attempt to access an arbitrary element using the index `i`.
```python
# initialize myArray
myArray = [1,3,5]

# access an arbitrary element, where i is the index of the desired value
myArray[i]
```

#### Traversing through an array
We can also read all values within an array by traversing through it. Below are examples of how we could traverse `myArray` from the start to the end using loops.
```python
for i in range(len(myArray)):
   print(myArray[i])

# OR

i = 0
while i < len(myArray):
   print(myArray[i])
   i += 1
```

The last element in an array is always at index n−1 where n is the size of the array. If the size of our array is 33, the last accessible index is 22.

To traverse through an array of size nn the time complexity is O(n)O(n). This means the number of operations grows linearly with the size of the array.

For example, with an array of size `10` we would have to perform `10` operations to traverse through it, with an array of size `100` we would have to perform `100` operations, and so on.

#### Deleting from an Array
In statically typed languages, all array indices are filled with `0s` or some default value upon initialization, denoting an empty array.

When we want to remove an element from the **last index** of an array, setting its value to `0` / `null` or `-1` is the best we can do. This is known as a **soft delete**. The element is not being "deleted" per se, but it is being overwritten by a value that denotes an empty index. We will also reduce the length by `1` since we have one less element in the array after deletion. The code below demonstrates the concept using `[4, 5, 6]` as an example.

```python
# Remove from the last position in the array if the array
# is not empty (i.e. length is non-zero).
def removeEnd(arr, length):
    if length > 0:
        # Overwrite last element with some default value.
        # We would also consider the length to be decreased by 1.
        arr[length - 1] = 0
```

#### Deleting an ith index
If instead of deleting at the end, we wanted to delete an element at a random index `i`. Would we be able to perform this in O(1)O(1)?

We could naively just replace it with a `0`, but this would break the contiguous nature of our array. Notice that deleting from the end of an array doesn't make it non-contigious, but deleting from the middle will.

A better approach would be the following:

1. We are given the deletion index `i`.
2. We iterate starting from `i + 1` until the end of the array.
3. We shift each element `1` position to the left.
4. (Optional) We replace the last element with a `0` or `null` to mark it empty, and decrement the length by `1`.

The following code demonstrates this operation.

```python
# Remove value at index i before shifting elements to the left.
# Assuming i is a valid index.
def removeMiddle(arr, i, length):
    # Shift starting from i + 1 to end.
    for index in range(i + 1, length):
        arr[index - 1] = arr[index]
    # No need to 'remove' arr[i], since we already shifted
```

#### Insertion at the end
If we want to insert an element at the end of the array, we can simply insert it at the next open position which will be at index `length` where `length` is the number of elements in the array.\

```python
# Insert n into arr at the next open position.
# Length is the number of 'real' values in arr, and capacity
# is the size (aka memory allocated for the fixed size array).
def insertEnd(arr, n, length, capacity):
    if length < capacity:
        arr[length] = n
```

#### Inserting at the ith index
Inserting at an arbitrary index `i` is more involved since we may insert in the middle of the array.

Consider the array `[4, 5, 6]`. If we need to insert at index `i = 1`, or `i = 0`, we cannot overwrite the original value because we would lose it. Instead, we will need to shift all values, starting at index `i`, one position to the right. Below is the code and visual demonstrating this.

```python
# Insert n into index i after shifting elements to the right.
# Assuming i is a valid index and arr is not full.
def insertMiddle(arr, i, n, length):
    # Shift starting from the end to i.
    for index in range(length - 1, i - 1, -1):
        arr[index + 1] = arr[index]
    
    # Insert at i
    arr[i] = n
```

#### Time Complexity
![[Pasted image 20250125134846.png]]
### Dynamic Arrays

Dynamic arrays are arrays that automatically free up space when adding an element to the array and reduce space when removing a significant amount of elements in an array. This is done by doubling the size of the array when adding an element to a full array. 

So the array doubles, new element is added in and then space is also freed up for other elements to be added. To add to the end of an array in python you can follow the code below:

```python
# Create a dynamic array (Python list)
arr = []

# Add elements
arr.append(10)
arr.append(20)
arr.append(30)

print(arr)  # Output: [10, 20, 30]

# Remove an element
arr.pop()  # Removes the last element
print(arr)  # Output: [10, 20]

# Insert an element at a specific index
arr.insert(1, 15)
print(arr)  # Output: [10, 15, 20]

```

Below is the code for what is happening in the back end and if you were to implement the data structure from scratch (unlikely to be asked)

#### Dynamic Array Insertion
```python
# Insert n in the last position of the array
def pushback(self, n):
    if self.length == self.capacity:
        self.resize()
        
    # insert at next empty position
    self.arr[self.length] = n
    self.length += 1
```

#### Resizing
```python
def resize(self):
    # Create new array of double capacity
    self.capacity = 2 * self.capacity
    newArr = [0] * self.capacity 

    # Copy elements to newArr
    for i in range(self.length):
        newArr[i] = self.arr[i]
    self.arr = newArr
```

Keep in mind that you may have to implement these data structures from scratch in some interviews if they ask for it. They might want you to implement a dynamic array from scratch 

#### Time Complexity
![[Pasted image 20250125134830.png]]


##### Advantages
- Can directly access specific indices in arrays so it takes O(1) time
##### Disadvantages
- Needs to be resized if you want to insert or remove an element from the array which takes O(n) time (if the specific element is not at the end of the array)
##### When to use
- Use when random access memory is required 
- When number of elements are known in advance and won't change frequently