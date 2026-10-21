How to write a function in MATLAB
```python
function [y1,y2] = myfun(x1,x2)
```

This creates a function named "myfun" and accepts inputs x1 and x2 and gives output y1 and y1. 

Example of a function that calculates the are of a circle
```python
function area = areaOfCircle(radius)
	area = pi*(radius)^2
end

rad = 4
areaofCircle(rad)
# This would output 50.26
```

Dynamic Systems
- Dynamic systems have states whose value evolve over time
- A state is just a type of quantity like position or population that depends on previous states as well as other properties of the system
- States depend on the initial conditions, physical characteristics of the system and any external inputs
- System outputs are dependent on the state

