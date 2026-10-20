### Enter Commands
This is the command window
![[Pasted image 20261020150827.png]]

This is the workspace
![[Pasted image 20261020150845.png]]

Adding a semicolon to the end of a statement means MATLAB will execute the line but it wont show the result immediately

Pressing up allows you to run commands again

Variables in MATLAB
- Must have letters, numbers, numbers and underscores and is case sensitive

### Saving and Loading Variables
You can save variables currently in your workspace by typing:
```python
save <filename.mat>
```

The "clear" command clears your workspace


You can load variables into your workspace using:
```python
load <filename.mat>
```

You can type "clc" to clear the command window


You can save or load just a couple variables by specifying it in the save and load commands (k is the single variable you want):
```python
save <filename k>
load <filename k>
```

### Use Built-in Functions and Constants
Some built in function in MATLAB:
```python
pi #pi
sin() #sin
sqrt #sqaure root
```

How to display more decimals
```python
format long
x

form short
x
```

### MATLAB Desktop and Editor
The command window allows you to run single commands one time and is good for quick checks and calculation. Often you will have to run more than one command at a time and do it sequentially. The best way to do this is by starting a "new live script"
![[Pasted image 20261020153333.png]]

