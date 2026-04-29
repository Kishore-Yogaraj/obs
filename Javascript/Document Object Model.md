- One of the most important Java script concepts
- In the previous lesson we learned about objects which are values that hold multiple other values inside of them such as strings, numbers and functions
	- When you use a function that is inside of an object it is called a method
- Javascript provides built in objects such as 
	- In the below example the text in blue is the name of the object and the text in read is the function making both of them together a method
```javascript
console.log('hello');
Math.random();
JSON.stringify(object);
localStorage.getItem('score');
```

- The DOM is another built in object in javascript
	- We use the dot notation to access the document object which contains the property "body" which is also an object and then we use the dot notation again to access the property innerHTML inside of the body object and then we change the innerHTML to 'hello'
```javascript
document.body.innerHTML = 'hello';
```

- **The document object is linked to the html webpage. It basically models the html webpage therefore "Document Object Model**
- Here is an example with the title 
```javascript
document.title = 'Good Job';
```

- Properties for document object
	- The DOM combines html and javascript together which allows javascript to control HTML
	- When HTML element is inside javascript it treats the element like an object (it is an object)
```javascript
document.title;
document.body; //This is usefule because it allows us to have HTML code in our javascript
```

- Methods for the Document Object
	- Methods are functions inside of object
	
```javascript
 console.log(document.querySelector('body')) //Puts the first button inside of our javascript
document.querySelector('button').innerHTML = 'Changed';
```

- Every html element has a property innerHTML that allows us to control and change the HTML inside of the element

### General Idea
- Any time you want an action on the web page to change the html you're going use a DOM
- First you want to set the method of document.querySelector() to a variable and ensure that there is a class attribute attached to the html element you want to work with so that you can access it with the querySelector
	- If you console log the below code you'll get the entire element returned to you
```javascript
const outcome = document.querySelector('.js-outcome');
```

- To access the string content within the html element we want to access the innerHTML property which will return a string that you can then manipulate as you please

```javascript
outcome.innerHTML = 'You lose.';
```