
Initializing Variables
![[Pasted image 20250930130407.png]]


Using size of
![[Pasted image 20250930130843.png]]

Functions are blocks of code that are designed to perform a specific task. Functions in classes are called methods. Function prevent code duplication if you have to reuse something multiple times. This makes it easier to edit code as well. Functions can have certain parameters and it can provide values for us.

A function that returns something:
![[Pasted image 20250930132856.png]]

Difference between Void and Regular Function in C++:

Every function has:
- A return type - what kind of value it gives back
- A name - what you call it
- Parameters - what values you pass into it
- A body - the code inside the brackets

Regular function (with return types):
If a function is supposed to give you a result, you give it a return type like int, float or std::string

![[Pasted image 20250930153637.png]]

![[Pasted image 20250930153645.png]]

For the above example:
- The return type is int
- The function name is add
- It has two parameters (a and b)
- It returns an integer using return

Void functions are functions that are not supposed to return a value. This function will do something but does not return a result. Example:
![[Pasted image 20250930153839.png]]

Here:
- Return type is void
- Function name is printHello
- It has no parameters
- No return is needed

The main things needed to learn because we are taking too long with this is:
- Variables
	- Types
		- Integer
		- Float
		- Char
		- String
- Functions
	- Void
	- Return Type
- Loops
	- While Loops
	- For Loops
- Header Files
- Pointers
- References
- Classes
- Inheritance
- Polymorphism
- When to use OOP vs when not to use OOP
- Interfacing with Hardware

The goal is to quickly learn the syntax and then ask chat gpt for projects that can teach you more about it
**When to use each function type**
You use the return function type when you need to calculate something or produce a result that the caller cares about. Examples of this include:
- Math calculations
- Checking a condition
- Converting data
- Reading and displaying data from a sensor

A good rule of thumb is if you need the answer back, make it return something

Use the void function when you need to do something but don't need the value back. Examples of these include:
- Printing or logging
- Changing a variable by reference
- Drawing something on the screen
- Sending commands to a motor or LED

A good rule of thumb: If the function is more like a command, "do this" instead of a question, "what is the value", then use void

Regular function
- You ask a question and it answers
Void function
- You give a command and it just does it

Calling a function:
![[Pasted image 20250930164237.png]]

Purpose of a function will always be to prevent copy and pasting code. If we find our selves reusing code over and over again we know that we should create a function for it instead.


### Header Files
The purpose of headier files is for declarations