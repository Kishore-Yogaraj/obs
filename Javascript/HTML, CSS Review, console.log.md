
HTML
- Hyper Text Markup Language
	- Giving instructions to a compute
- The code below shows different html elements such as buttons and paragraphs
- {button} (replace curly brackets with arrow brackets) are called an html tag
- {p} is an html tag for paragraphs
- Every tag needs their matching tag which has a slash in front of the text for the tag
- In between the start and end tag you can find the text which is the actual content you want inside of the element 
- Inside each element you can also have other elements
- Multiple spaces are only shown as one space and not as multiple spaces
- New lines in the code are also represented as spaces in html
- It's good practice to have the text within each element in html on their own lines as shown below

```html
<button>
    hello
</button>

<!-- <p> = paragraph -->
<p>
paragraph of <button>hello</button> text
</p>
```

CSS (Cascading Style Sheets)
- Changes the appearance of the website
- CSS code is written inside of the html file
- The **style** element allows us to write css code inside of html
- The button element inside of the style element is called the **CSS Selector** which selects which elements on the web page to change
- Inside of the curly brackets followed by the button selector are the **CSS Styles** which tells the computer how to change the appearance
	- The left side is the **CSS property** which tells the computer what exactly we are changing
	- The right side is the **CSS value** which tells the computer what to change the CSS property to 

```html
<!-- inside the style element we are declaring all buttons on the web page to be changed to the style we specify -->
<style>
    button { 
	    background-color: red;
		color: white;
		border: none;
    }
</style>

<button>
    hello
</button>

<!-- <p> = paragraph -->
<p>
paragraph of <button>hello</button> text
</p>
```

HTML Attributes
- Changes the behavior of an element
- The attribute always follows the the start tag of an element
- "title" in the attribute is the **attribute name** and what follows the attribute name is the **attribute value**
- In this example we are changing the tool top (title) to say "good job"

```html
<!-- inside the style element we are declaring all buttons on the web page to be changed to the style we specify -->
<style>
    button { 
	    background-color: red;
		color: white;
		border: none;
    }
</style>

<button title="good job">
    hello
</button>

<!-- <p> = paragraph -->
<p>
paragraph of <button>hello</button> text
</p>
```

**Class attributes**  add a "label" to different elements
- After labelling an element with a class attribute, you have the ability of styling that specific button with CSS

```html
<style>
    .red-button {
        background-color: red;
        color: white;
        border: none;
    }
    
    .yellow-button{
        background-color: yellow;
    }
</style>

  

<button title="Good job" class="red-button"> <!-- allows text to be displayed over buttons when hovers aka a tool tip-->
    hello
</button>


<!-- <p> = paragraph -->
<p>
paragraph of <button class="yellow-button">hello</button> text
</p>
```

HTML Structure
```html
<!DOCTYPE html> <!-- tells the browser to use the latest version of html-->

<!-- the html element is the entire web page so all contents should go inside this tag-->
<html>
    <!-- contains everything not visible on the page-->
    <head>
        <style>
            .red-button {
                background-color: red;
                color: white;
                border: none;
            }
            .yellow-button{
                background-color: yellow;

            }
        </style>
    </head>
    <!-- contains everything that is visible on the page-->
    <body>
        <button title="Good job" class="red-button"> <!-- allows text to be displayed over buttons when hovers aka a tool tip-->
            hello
        </button>
        <!-- <p> = paragraph -->
        <p>
            paragraph of <button class="yellow-button">hello</button> text
        </p>
    </body>
</html>
```

What are the benefits of using the proper html structure?
- Other elements can be used inside the head element such as the title element 

Opening with **Live Server**
- Right click on the code screen and click "open with live server" from drop down menu