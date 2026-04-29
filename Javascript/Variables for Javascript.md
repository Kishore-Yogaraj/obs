What is a variable?
- A variable is a container in which you can save a value (integer, float, string) and use it later
```javascript
// How to assign a variable
let variable = 3;
```

Rules of variable names
- Can't use special words such as "let"
- Can't start with a number
- Can't use special characters except: $ and __

General Notes
- A semi colon is the end of an instruction 

Code for **Add to cart feature**

```javascript
<!DOCTYPE html> 
<html>
  <head>
    <title>Cart Quantity</title>
  </head>

  <body>
    <button onclick ="
      console.log(`Cart quantity: ${cartQuantity}`);
      ">
      Show Quantity</button>

    <button onclick = "
      // cartQuantity = cartQuantity + 1;
      // cartQuantity += 1; This is the same as the line above
      cartQuantity++; // This also the same as the lines above
      console.log(`Cart quantity: ${cartQuantity}`);
    ">Add to Cart</button>

    <button onclick = "
      // cartQuantity = cartQuantity + 2;
      cartQuanitty += 2; // This is the same as the line above
      console.log(`Cart quantity: ${cartQuantity}`);
    ">+2</button>

    <button onclick = "
      cartQuantity = cartQuantity + 3;
      console.log(`Cart quantity: ${cartQuantity}`);
    ">+3</button>

    <button onclick = "
      cartQuantity = 0;
      console.log(`Cart quantity: ${cartQuantity}`);
      console.log('Cart was reset.')
    ">Reset Cart</button>

    <script>
      let cartQuantity = 0;

    </script>
  </body>
</html>
```

Variable Re-assignment Shortcuts
- += 2 variable = variable + 2
- -= 2 variable = variable - 2
- \*= 2 variable = variable \* 2
- /= 2 variable = variable / 2
- ++ variable = variable + 1
- -- variable = variable -1

Best practices for naming variables
- If we want to have multiple words for a variable name we have to use different naming conventions such as **camel case** (javascript)
	- cartQuantity
- This is where we capitalize every word except the first word
- Pascal case
	- CartQuantity
- Kebab case (html)
	- cart-quantity
- Snake case
	- snake_case

3 Ways to create variables
- using let
	- let variable1 = 3
- using const
	- const variable1 =3 
	- This is a constant which means that the variable can not be reassigned after is assigned the first time
	- We use const to make code safer and easier to understand
	- Use const if you want a variable to be the same the entire time
	- Use const by default and use **let** when you know you have to change the variable later on in the code
- using var
	- var variable1 = 3
	- Same as let and can be used to reassign to variables
	- Var has some issues so we don't use var a lot
	- Older versions of javascript may still use var
- **typeof**
	- Will tell us the type of value of a variable
	- typeof variable1

General Notes
- In javascript the type of values we have are numbers or strings (all we know as of now)