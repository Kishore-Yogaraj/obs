Strings
- Text in code
```javascript
'hello' //basic string
'some' + 'text' //combine strings
// sometext
'some' + 'more' + 'text' //concatenate
typeof 'string' or number //gives you the type of value 
'hello' + 3 //can concatenate different values(type coercian)
'Items (' + (1 + 1) + '): $' + (2095 + 799) /100
// 'Items (2): $28.94'
'Single quote' //Can use single quotes to print strings
"Double quote" //Can use doubele quotes to print strings
\' //Escape characters count the following character by the backslash to be just a piece of text and has no meaning
\n //New line character that creates a new line of text
`back tick string` //template strings
//allows for interpolation = insert value directly into a string
`Itmes (${1 + 1}): $${(2095 + 799)/100}`
// The line above uses interpolation which allows for any value (including numbers) to be inserted into a string without needing to use concatenation
// You do this by using a dollar sign followed by curly brackets and inside the curly brackets you include whatever value you desire as shown below
`string ${number value}` //interpolation

//template strings are used for multiline strings
`some
string`
```

Characters in Javascript
- Letter 
- Number
- Symbol (!, @, #)
- Escape characters

General notes
- When to use single quotes, double quotes or template strings
	- Use single quotes by default
	- If we need to use interpolation or multiline strings then we should using template strings
- String = text
- you can use strings and numbers together
- Escape characters \n and \(any value)
