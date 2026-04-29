### What is it?
- One of the easiest and most in demand programming languages
	- Versatility
	- Readability
	- Clear syntax
- General purpose language meaning it can be used to develop multiple different kinds of applications
	- Data science
	- Game Dev
- Machine learning 
### Jupyter Notebook
- No need to call "print" in Jupyter notebook
![[Pasted image 20240810090425.png#center|400]]
```python
# Jupyter Notebook HowTo
Shift + Enter #Executes shell
Click cell + esc + d + d #Delete shell
B #Adds new cell ensure you are in command mode of the cell and not the editing mode
alt + Enter #Creates new cell 
```
### Arithmetic Operators
- Very simple in python
	- + (addition)
	- - (subtraction)
	- *  (multiplication)
	- / (division)
	- % (modular output) - outputs the remainder
	- ** (exponents)
	- // (divides and rounds down to nearest integer)
- Python follows order of operations
### Variables
**Definition:** A label used to hold a value (holds one value in memory that can be retrieved multiple times)

### Numeric Data Types
```python

```

### Things to Know
- Operations with int and float will result in a float
```python
age = 25
age += 5
print(age) # Takes current value and increments it by 5
# Output = 30

age = 25
age -= 5
print(age) # Takes current value and decrements it by 5
# Output = 20

int(7/3.5)
# output = 2

float (7/3.5)
# output = 2.0

type(var) #Returns data type
```

### Methods
- Every data type has its own distinct set of methods which can be accessed through dot notation
- For example
```python
movie = "Harry Potter"
movie.upper()
# Output = "HARRY POTTER"
```
- Sometimes we create our own libraries which are classes that can be used to create our own methods
- This means that when we declare a string for a variable we're creating a string object which can then access different methods methods within that object
### Lists
- Lists allow us to look up individual elements which can be accessed via an index
- The index starts at 0 
- Lists can contain multiple data types
- Lists are mutable
```python
list = ['John', 'Jane', 'Joe']
print(list[1])
# Output = Jane

random_var = [True, False, 'Hello', 1, 1.2]
length = (len(random_var))
print(random_var[length - 1])
#Output = 1.2

print(random_var[-1])
#output = 1.2 - accesses last element in list
```

### Slicing
- Slicing is done with a set of indices that specifies where we want to start slicing from and end slicing from
- This allows us to pull multiple elements from a list
```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
print(numbers[2:10]) #Index 10-1
#Output = [2, 3, 4, 5, 6, 7, 8, 9]

print(numbers[2:])
# Output = [2, 3, 4, 5, 6, 7, 8, 9, 10]

print(list(range(0,10)))
# Output = [2, 3, 4, 5, 6, 7, 8, 9]

print(list(range(0,100,5)))
# Output = [0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]

title = "game of throne"
print(title[2])
# Output = m
```
### Membership Operators
- Used to determine if something is inside of a list or string
	- "in" and "not in"
	- in evaluates to true if it finds a variable in the specified sequence
	- not in evaluates to true if it does not find a variable in the specified sequence
```python
months = ["march", "april", "may"]
print('June' not in months)
# Output = True

course = 'python crash course'
print('crash' in course)
# Output = True
```

### Mutability
- Mutable: Liable to change
	- Whether or not we can change something after its been created
- Immutable: Not liable to change
- Lists and string are very similar as one encloses multiple different data types and one encloses multiple different characters
	- The difference between these 2 data types is mutability
- Strings can not be directly changed (immutable)
- Lists can be directly changed (mutable)
```python
grocery_list = ['banana', 'apple', 'flower']
grocery_list[2] = 'orange'
print(grocery_list)
# Output =   ['banana', 'apple', 'orange']

spelling = 'cucomber'
spelling[3] = 'u'
pring(spelling)
# Output = Error


name = "Ameer"
other_name = name
name = 'John'
print(name)
print(other_name)
# Output: John
# Output: Ameer
# Shouldn't other_name also be 'John?' No because strings are immutable. When we assign other_name to name we are making other_name point to the same object in memory as name. When we reassign name to a new value, other_name still points to the original string


books = ['The catcher', 'The Mist', 'Lord']
more_books = books
books[0] = 'Song of Ice'
print(books[0])
# Output = Song of Ice

print(more_books)
# Output = ['Song of Ice', 'The Mist', 'Lord']
```
- Things to keep in mind when working with data types
	- Mutable?
	- Ordered?
- Since lists are mutable changing a list will reflect in all variables for that list
- Any variable used for that list can be changed and configured no matter which variable that list is being changed for 

### Tuples
- Immutable ordered sequence of elements
	- Related Information
```python
traits = ('tall', 'slim', 'blond') # Paranthesis not needed
height = traits[0]
build = traits[1]
print(height, build)
# Output = tall slim

# Tuple unpacking
height, build, hair = 'tall', 'slim', 'blond'
print(height, build)
# Output = tall slim


# Tuple of strings 
tuple_of_strings = ("apple", "banana", "cherry") 
# Tuple of mixed data types 
mixed_tuple = (42, "Hello", 3.14, True) # Tuple without parentheses
tuple_no_parentheses = 1, 2, 3, 4 
# Single-element tuple (note the comma) 
ingle_element_tuple = (42,)

print(tuple_of_strings[0]) #Output:"apple" 
print(mixed_tuple[2]) #Output:3.14

for item in tuple_of_strings: 
	print(item)

#Unpacking tuples into variables
x, y, z = tuple_no_parentheses print(x,y, z) # Output: 1 2 3

#Mutable lists
emotion = ['happy', 'sad', 'normal']
emotion[2] = 'change'
emotion

#Immutable Tuples
emotion = ('happy', 'sad', 'normal')
emotion[2] = 'change'
emotion
```
- Can not change tuples
- Can not add or remove elements from tuples without creating a brand new tuple
- Think of tuples as lists that are never changed and stay together and are related in some way
- They are useful for representing fixed collections of items
- They can be accessed via indexes and for loops

### Sets
- Mutable, unorders, unique sequence of elements
```python
duplicate_numbers = [1,1,2,2,3,3]
unique_numbers = set(duplicate_numbers)
print(unique_numbers)
# Output =  {1, 2, 3}
```
- A set is unordered so we can't use indexing to configure an element
- We can check if an element is in a set
- A set will only contain unique elements

### Dictionaries
- Dictionaries are composed of unique keys and each key corresponds to a specific value
- It is mutable so we can add, change or remove key value pairs as we please
	- Keys are immutable and values are mutable
- The keys itself must be unique and of an immutable type
	- Either a string, number or tuple
- Dictionaries are unordered and indexed by a list of keys be referencing the corresponding key
- Dictionaries main purpose is store and extract values based on its key
```python
inventory = {'bananas': 1.29, 'apples':2.99, 'papayas': 1.39}
print(inventory['bananas'])
# Output = 1.29

inventory = {'bananas': 1.29, 'apples':2.99, 'papayas': 1.39}
print(inventory['bananas'])
```

### Compound Data Structures
- Dictionary within a dictionary
```python
inventory = {'bananas': {'price': 2.99, 'country of origin': 'Canada'}, 
             'apples': {'price': 3.99,'country of origin': 'USA'}, 
             'papayas': {'price':4.99, 'country of origin': 'Mexico'}}
print(inventory['bananas'])
# Output: {'price': 2.99, 'country of origin': 'Canada'}

# Accessing country of origin for apples
inventory = {'bananas': {'price': 2.99, 'country of origin': 'Canada'}, 
             'apples': {'price': 3.99,'country of origin': 'USA'}, 
             'papayas': {'price':4.99, 'country of origin': 'Mexico'}}
print(inventory['apples']['country of origin']) # Access the 'apples' which returns the dictionary for apples and then accesses the key country of origin for apple which then returns the country of origin which is USA  
#Output: USA
```

### Control Flow
- Sequence at which the code is run
	- Loops
	- Conditionals

### If, else
- Executes block of code if certain condition is met otherwise it is skipped
```python
inventory = {'bananas': 1.29, 'apples':2.99, 'papayas': 1.39}
item = 'brussel sprouts'
if item in inventory:
    print('found the', item)
else:
    print('could not find the', item)
    inventory.update({item: 2.99})
    print('Just aded the item, here is the updated grocery list', inventory)
# Output: could not find the brussel sprouts
# Just aded the item, here is the updated grocery list {'bananas': {'price': 2.99, 'country of origin': 'Canada'}, 'apples': {'price': 3.99, 'country of origin': 'USA'}, 'papayas': {'price': 4.99, 'country of origin': 'Mexico'}, 'brussel sprouts': 2.99}

```

### elif
- Used when if statement fails and you're not ready to move on to the else statement
- Want to test a few more conditions and if those conditions are true we use a separate block of code
```python
item, price = 'rutabagas', 3.99
if item in inventory:
    print('found the', item)

elif price > 2.99:
    print('too expensive for inventory')
    
else:
    print('could not find the', item)
    inventory.update({item: price})
    print('Just aded the item, here is the updated grocery list', inventory)
# Output: too expensive for inventory

daytime = 'morning'

if daytime == 'dawn':
    print('still asleep')
elif daytime == 'morning':
    print('time to work')
elif daytime == 'noon':
    print('time to take a lunch break')
else:
    print('time to go to sleep')
# Output: time to work
```

### For Loops
- Iterable: Can be iterated through
	- Anything that can return one of its elements one at a time
	- List
	- Dictionary
	- String
	- Tuple
- For loops can be used to extract elements
```python
months = ['March', 'April', 'March']
for month in months: # 'month' is the iterable which can be named anything. A common recommended pattern is to name it something singular to the name of the list. 'month' is the iteration variable which means that each element will be assigned to that variable when it is being passed through the loop
    print(month)
# Output: 
# March
# April
# March

```

- For loops can be used to create elements
- We can use the index of each element to point to that element in the list 
```python
names = ['hillary', 'diana', 'brian']

for index in range(len(names)): # range is 0-2
    print(index)
# Output: 
# 0 
# 1
# 2

names = ['hillary', 'diana', 'brian']

for index in range(len(names)): # range is 0-2
    names[index] = names[index].title() 
print(names)
# Output: ['Hillary', 'Diana', 'Brian']
```
- We use the index Iterable to access each element in the list. We then make a change to that element and the completes the loop. Then we iterate to the next element. This happens for all 3 elements because that is the number of times we requested the loop to run for. This means we can use for loops to access indices of an element and the element itself
- We can use for loops to extract elements in which it would be structured like this
```python
months = ['March', 'April', 'March']
for month in months: 
    print(month)
# Output: 
# March
# April
# March
```

- We can use for loops to to create or configure elements which would be structured like this
```python
names = ['hillary', 'diana', 'brian']

for index in range(len(names)): # range is 0-2
    print(index)
# Output: 
# 0 
# 1
# 2

names = ['hillary', 'diana', 'brian']

for index in range(len(names)): # range is 0-2
    names[index] = names[index].title() 
print(names)
# Output: ['Hillary', 'Diana', 'Brian']
```
- Notice how when extracting the Iterable is assigned the element
- When attempting to configure an element the iterable is assigned the index (integer)
	- This is because the .title() method is a method for lists in which each element is a string
### Iterating through a dictionary
- Can iterate through just the keys
- Can iterate through the key-value pairs using the .items() method which allows you to easily access key value pairs
```python
movie = {'Titanic': 1997, 'Finding Nemo': 2003}
for key in movie: #Key iterable variable is assigned to each key in the dictionary
    print(key)
# Output
# Titanic
# Finding Nemo

movie = {'Titanic': 1997, 'Finding Nemo': 2003}
for key, value in movie.items(): #Each dictionary item is turned into a tuple that contains the movie and release date. This means that each element in movies has become a tuple. For each element the iterable key is assigned to the first element of the tuple and the second iterable is assigned to the second element of the tuple
    print(key, value)
# Output
# Titanic 1997
# Finding Nemo 2003

#.items() is dictionary method used to turn key value pairs into a view object of a list of tuples. The elements within the tuple can then be accessed with a for loop however it dict_items data types are not ordered therefore you can't access them by indexing.

movie  = {'Titanic': 1997, 'Finding Nemo': 2003}
movie.items() = [('Titanic', 1997), ('Finding Nemo', 2003)] #List of tuples

```
### While Loops
- While loop depends on conditions and logic affecting condition
```python
random_number = 20

while random_number < 30:
    print(random_number)
    random_number +=1
```

#### General About Loops
- For loops will keep iterating based on the range of the Iterable
- While loops keeps iterating through a block of code until its condition stops being meant

### Continue and Breaks
- Continue jumps through one iteration of the loop
- It skips one iteration of a loop
- Break jumps out of the loop completely and breaks out of the loop
```python
# If it is odd don't continue through the loop
for number in numbers:
    if number % 2 != 0: #If number divided by 2 gives us a remainder
        continue
    print(number)
# Output: 
# 0
# 2
# 4
# 6
# 8

# If it is odd break the loop
for number in numbers:
    if number % 2 != 0: #If number divided by 2 gives us a remainder
        break
    print(number)
# Output: 0
```

### Function
- Named section of your program that performs a specific task
- Once a function is defined it can be used over and over again
```python
def rectangle_area (length, width):
	area = length * width
	return area
or

def rectangle_area (length, width):
	return length * width
```

### Scope
- Parts of your program that can use or access a variable
- Local
	- Variable names assigned within a function
- Global
	- Variable names that can be used anywhere in the code
```python
number = 2 # Global scoped

def random_function():
    name = "Bill" #'name' is scoped to this function (local scoped)
```

### Doc Strings
- Docstrings area comment used to explain the purpose of a function
```python
def rectangle_area (length, width):
    """ calculates area of a rectangle """
    """ or """
    """
    INPUT:
    
    This function takes in two parameters length and width
    
    OUTPUT:
    
    This function calculates the area based on the length and width 
    
    """
    area = length * width
    return area

print (rectangle_area(3,6))
```

### Lambda and Higher Order Function
- High order functions are functions that takes another function as an argument
```python
numbers = [1,2,3,4,5]

def even_or_odd(number):
    return number % 2 == 0 #Double = makes it comparitive so True or False is what's returned

print(list(filter(even_or_odd, numbers)))
```
- If the sole purpose of a function is to be an argument in another function its not exactly reusable in your code
	- We use a lambda operator which allows us to create small helpful functions which aren't needed later in our code
```python
numbers = [1,2,3,4,5]

print(list(filter(lambda number: number % 2 == 0, numbers)))
```
##### Just something to know
- The filter function will take the function that evaluates the list for even or odd numbers and the list itself as arguments
- The function will then pass the list of numbers to the other function which returns true if even and false if odd
- The filter function will then iterate through each of the results of the other function and if true it will keep it in the list and if false it will get rid of it from the list