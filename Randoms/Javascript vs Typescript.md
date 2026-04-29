Typescript basically checks your code before you actually run the code. This is what it means to be a dynamically typed language vs a typed language.

Typescript is a typed language which means it makes you determine what **type** of thing goes in each variable.

In Javascript when you create a variable, you can actually change what's in the variable as shown below:

```javascript
let myNumber = 8;
myNumber = "hello";
myNumber = true
```

At run time javascript will execute whatever is stored in the variable at the point where you execute your code.

Whereas in typescript:
```typescript
let myNumber = 8;
myNumber = "hello"; //Type script will flag this and throw an error because the variable myNumber was already declared to be an integer
```

**You can think of typescript as a tool to use that allows you to develop faster and cleaner because it checks for mistakes and is a bit more strict on the types of your variables and maintaining consistency. This is a good thing because when you have struc**


> [!QUESTION] Why is coding in a typed language a good thing?
> I think coding in a typed language is a good thing because it makes it easier to collaborate with others on a project? For example if someone is looking at your code for the first time and is trying to test a function out, they may not know that a function can only take integers and not strings. Instead of having to test this our by running the program, typescript will catch this mistake early on and let you know that it's not allowed. Whereas if you were using regular javascript, you may get an output that you weren't expecting and you wouldn't know where exactly you went wrong.

```javascript
function multiply(a, b){
	a * b;
}
multiply(5, "hello"); // Java script would not catch this and would send out an error but in a large codebase you wouldn't know where this error is coming from
```

```typescript
// notice how in typescript you need to specify what type the variable should be the parameters of the function 
function multiply(a: number, b: number){
	a * b;
}
multiply(5, "hello"); //Typescript will tell you that there needs to be a number here
```