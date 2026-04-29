hat are booleans?
- Another type of value like numbers and strings but only have two different kinds of values which are true and false

What's the purpose of boolean values?
- Represents whether something is true or false

How do we create a boolean?
- Comparator operators
	- > greater than
	- < less than
	- >= greater than or equal to
	- <= less than or equal to
	- === equal to
	- == equal to (Converts both values being compared to the same type before checking to see if they are equal)
		- Use === more often unless you absolutely need to use ==
	- !== not equal to
	- !=
		- This will also perform conversions  but use !== more often
	- Comparison operators have a lower priority than math operations

What are if statements?
- Let's use write multiple groups of code
- And then lets us decide which code to run

```javascript
<!DOCTYPE html> 
<html>
  <head>
    <title>Booleans</title>
  </head>

  <body>
    <script>
      
      true
      false

      console.log(3 > 5)
      
      // If the boolean value in the circle brackets is true then we run the code in the curly brackets
      //If false then we don't run the code in the curly brackets
      if (false) {
        console.log('hello');
      } else {
        console.log('goodbye') //If the boolean value is false then this code will run
      }

      //Inside the bracket you can put in any expression that will evaluate to a boolean value (also known as the condition)
      //If the condition is true then the code in the curly brackets will run
      //If the condition is false then the code in the curly brackets will not run
      // If you have an else statement then the code for the else statement will run if the condition is false
      //Each if and else statement are considered branches
      // If you have multiple lines of code inside a branch you need curly brackets, otherwise you don't need them

      //In the code below we check to see if the first condition is true, if false we go to the else if. If the else if is false then we go to the else statement

      const age = 15;

      if (age >= 16) {
        console.log('You can drive');
      } else if (age >= 14) {
        console.log('almost there');
      } else {
        console.log('You cannot drive');
      }

     console.log(true && true); // checks to see if two different conditions are true. If they are both true then the if statement will execute
    </script>
  </body>
</html>
```

**Logical operators**
- AND operator
	- console.log(true && true)
	- Checks to see if both conditions are true before executing if statement
- OR operator
	- console.log(true || false);
	- Checks to see if at least on condition is true 
- NOT operator
	- console.log(!true);
	- Flips the boolean value to the opposite value

**Scopes**
- If statements create a scope which limits where a variable exists
- Any variable that is created inside of the curly brackets can only be used within the curly brackets
- Scopes can help us avoid naming conflicts

How do we access a variable inside an if statement?
- We have to create the variable outside of the if statement
- In the code below we can see that computerChoice was assigned outside of the if statement so any changes made to the variable inside of the if statement will be reflected when printing out the variable

```javascript
<!DOCTYPE html> 
<html>
  <head>
    <title>Rock Paper Scissors</title>
  </head>

  <body>
    <p>Rock Paper Scissors</p>
    <button onclick = "
      const randomNumber = Math.random() //Generates a random number between 0 <= and <1
    
      let computerChoice = '';

      if (randomNumber >= 0 && randomNumber < 1/3) {
        computerChoice = 'rock';
      } else if (randomNumber >= 1/3 &&randomNumber < 2/3) {
        computerChoice = 'paper';
      } else if (randomNumber >= 2/3 && randomNumber < 1) {
        computerChoice = 'scissors';
      }
      
      console.log(computerChoice);

    ">Rock</button>
    <button>Paper</button>
    <button>Scissors</button>
    <script>

    </script>
  </body>
</html>
```

How to use if statements and logical operators

```javascript
<!DOCTYPE html> 
<html>
  <head>
    <title>Booleans</title>
  </head>

  <body>
    <script>
      
      true
      false

      console.log(3 > 5)
      
      // If the boolean value in the circle brackets is true then we run the code in the curly brackets
      //If false then we don't run the code in the curly brackets
      if (false) {
        console.log('hello');
      } else {
        console.log('goodbye') //If the boolean value is false then this code will run
      }

      //Inside the bracket you can put in any expression that will evaluate to a boolean value (also known as the condition)
      //If the condition is true then the code in the curly brackets will run
      //If the condition is false then the code in the curly brackets will not run
      // If you have an else statement then the code for the else statement will run if the condition is false
      //Each if and else statement are considered branches
      // If you have multiple lines of code inside a branch you need curly brackets, otherwise you don't need them

      //In the code below we check to see if the first condition is true, if false we go to the else if. If the else if is false then we go to the else statement

      const age = 15;

      if (age >= 16) {
        console.log('You can drive');
      } else if (age >= 14) {
        console.log('almost there');
      } else {
        console.log('You cannot drive');
      }

     console.log(true && true); // checks to see if two different conditions are true. If they are both true then the if statement will execute
    </script>
  </body>
</html>
```

Truthy and Falsy Values
- Truthy and Falsy values are just ways to represent values in ways other than just using "true" and "false"
- IF statements work with any type of values including numbers and strings
- List of Falsy values (anything not on this list is a truthy value)
	- false
	- 0 
	- ' '
	- NaN
	- Undefined
	- null

Why do we use truthy and falsy values
- One way we can use them is a short cut in our code

```javascript
   //5 act like a true value
    if (5) {
      console.log('truthy')
    }

    //0 acts like a false value
    if (0) {
      console.log('truthy')
    }

 /* Instead of using a comparison like cartQuantity > 0, we can just use the variable name itself. If the variable is truthy then the code will run. If the variable is falsy then the code will not run. */
    if (cartQuantity) {
      console.log('cart has products')
    }

    console.log('text'/5); //NaN is a falsy value

    let variable1; //undefined can only be used with let and not const
    console.log(variable1); //undefined is a falsy value  
```

Shortcuts for If Statements (You can think of all of these as a way of writing an if statement in one line)
- Ternary Operator (?:)
	- An advantage of the ternary operator over an if statement is that you can save the ternary operator in a variable
```javascript
 //Similar to an if else statement
    /*
    The true value is the condition. If the condition is true then the first value after question mark will be returned. If the condition is false, the second value after the colon will be returned
    */
    const result = true ? 'truthy' : 'falsy' 
    console.log(result);

    const result2 = false ? 'truthy' : 'falsy' 
    console.log(result2);

    //Above is the shortcut for the code below
    if (true) {
      'truthy'
    } else {
      'falsy'
    }
```

- Guard Operator (&&)
	- AND operator &&
		- value1 && value2
		- checks if both conditions are true
	- If you have a scenario like this:
		- false && value2
			- We already know that this condition is going to be false and doesn't continue writing the code
			- This is called a short circuit operation
	- In the code below you can think of the first value "guarding" the code after the && from running. So its good to use this if you have a scenario where certain code can only be executed if a condition is met
	- You can also save the guard operator in a variable
```javascript
false && console.log('hello'); //If the first value is false then the code after the && will not run

    //The above code is a short cut for the code below
    if (condition) {
      console.log('hello');
    }

    const message = false && 'hello';
    console.log(message); 

    //The above code is a short cut for the code below
    let message2;

    if (condition2) {
      message2 = 'hello';
    }	
```
- Default operator (||)
	- OR operator checks to see if at least one condition or value is truthy
		- value1 || value2
		- if one these values are already true there's no reason to check the other value as well so it performs a short circuit operation
	- The OR operator can be used to set a **default** value as shown in the code below
```javascript
//Choosing a currency pay for the order
    //Choosing a currency is optional so if nothing is selected we can use the default currency which is USD
    const currency = 'EUR' || 'USD'
    console.log(currency); //This will return EUR

    const currency2 = undefined || 'USD'
    console.log(currency2); //This will return USD

    //The above code is a short cut for the code below
    let currency3;

    if (!condition) {
      currency3 = 'USD';
    }
```


General Notes
- Boolean Values (true, false)
- If statements
- Comparison operators
	- > greater than
	- < less than
	- >= greater than or equal to
	- <= less than or equal to
	- === equal to
	- == equal to (Converts both values being compared to the same type before checking to see if they are equal)
		- Use === more often unless you absolutely need to use ==
	- !== not equal to
	- !=
		- This will also perform conversions  but use !== more often
	- Comparison operators have a lower priority than math operations
- Logical operators
	- AND operator
		- console.log(true && true)
		- Checks to see if both conditions are true before executing if statement
	- OR operator
		- console.log(true || false);
		- Checks to see if at least on condition is true 
	- NOT operator
		- console.log(!true);
		- Flips the boolean value to the opposite value
- Truthy and Falsy Values
- Shortcuts for if-statements
	- Ternary 
	- Guard
	- Default