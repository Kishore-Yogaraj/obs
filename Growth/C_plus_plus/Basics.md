### Output

![[Pasted image 20251002113517.png]]

- `#include <iostream>` is a library 
- Including this library makes the cout and cin objects available to the program but are stored in a container called the std namespace
- The `std::` prefix tells the compiler to look for cout and cin inside the specific namespace
- Let's say we are working on a project that uses multiple libraries and they both have the `sort()` function available, the compiler would not know which `sort()` function to use from which library
- The program above prints out Hello World to the Console

#### Compiling the program

`g++ helloworld.cpp -o helloworld.exe` 

#### Running the program

`.\helloworld.exe`

#### Creating a New Line

![[Pasted image 20251002134708.png]]

### Comments

![[Pasted image 20251002134826.png]]

#### Multi Line Comments

![[Pasted image 20251002134926.png]]

### C++ Variables
Variables are containers for storing data values

In C++ there are different types of variables for example:
- int - stores integers (whole numbers without decimals)
- double - stores floating point numbers with decimals
- char - stores single characters such as 'a' or 'B' (use single quotes)
- string - stores text such as "Hello World". String values are surrounded by double quotes
- bool - stores values with two states: true or false

#### Declaring variables

`type variableName = value;`

![[Pasted image 20251002135234.png]]
#### Changing Variable Values

![[Pasted image 20251002135405.png]]

#### Example of type of variables
![[Pasted image 20251002135604.png]]

#### One Value to Multiple Variables

![[Pasted image 20251002135825.png]]

#### Constants
When you do not want other (or yourself) to change existing variable values, use the `const` keyword (this will declare the variable as "constant" which means unchangeable and read only)

![[Pasted image 20251002140111.png]]

### User Input

![[Pasted image 20251002140605.png]]

### Data Types

![[Pasted image 20251002140628.png]]

#### Numeric Data Types

`float` vs `double`

The precision of a floating point value indicates how many digits the value can have after the decimal point. The precision of float is only six or seven decimal digits, while double variables have a precision of about 15. Therefor it's safer to use double for most calculations

#### Booleans

![[Pasted image 20251002141024.png]]

![[Pasted image 20251002141038.png]]

#### String
To use strings you have to include the string header file which is `#include <string>`

![[Pasted image 20251002141415.png]]

#### Auto Keyword
The auto keyword automatically detects the type of variable based on the value you assign to it. It helps you to write cleaner code to avoid repeating types especially for long or complex types.

Instead of writing `int x = 5` you can write `auto x = 5; //x is automatically trated as an int`

Auto only works when you assign a value at the same time. You can't declare a variable without assigning a value to it. Auto is useful when using iterators and lambdas.

### Operators
- Arithmetic
- Assignment
- Comparison
- Logical
- Bitwise

#### Arithmetic Operators

![[Pasted image 20251002142118.png]]
#### Assignment Operators

![[Pasted image 20251002142246.png]]

##### Example of using Compound Assignment Operators

![[Pasted image 20251002142321.png]]
- Compound operators make code shorter and easier to read, especially when updating the same variable many times

#### Comparison
Comparison operators are used to compare two values (or variables). This is important in programming because it helps us to find answers and make decisions,

The return of a comparison is either `1` or `0` which means true or false.

Comparisons need to be enclosed in brackets in order to work

![[Pasted image 20251002142541.png]]

![[Pasted image 20251002150303.png]]

#### Logical Operators
As with comparison operators, you can also test for true or false values with logical operators.

Logical operators are used to determine the logic between variables or values:

![[Pasted image 20251002150419.png]]

#### Precedence
Operations are always done in the correct order of operations

### C++ Strings

#### String Length
To get the length of a string you can use the `length()`  function or you can use the `size()` function. Example of this:

![[Pasted image 20251002150809.png]]
![[Pasted image 20251002150818.png]]

#### Access Strings
You can access the characters in a string by referring to its index number inside square brackets `[]` 

Example of printing the first character:
![[Pasted image 20251002151036.png]]

#### at() function
The `<string>` library also has an `at()` function that can be used to access characters in a string:
![[Pasted image 20251002151146.png]]