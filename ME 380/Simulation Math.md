### Defining grid space and verification of lengths
```python
# Define the grid resolution to evaluate
x = np.linspace(-300, 300, 601)
y = np.linspace(0, 450, 451)
X, Y = np.meshgrid(x, y)
```

This part of the code defines the grid space we are working with. We have selected a 600 mm by 450 mm workspace. The motors are aligned with the origin (0, 0) in this workspace.

`linspace` is function that takes a space of units and chunks it into an array based on the number of units we want to split the chunk into.

`linspace` gives us `[start end num]`
Start: Starting point
End: Ending point
Num: Number of ticks you want to sample between the two points

```python
x = np.linspace(0, 100, 5)
print(x)
```

We would get
`[0 25 50 75 100]`

It calculated that in order to get 5 points starting at 0 and ending at 100 you needed exactly 25 points apart

In our case of [-300, 300, 601] we would get 1 unit across a span of 600. **As long as out unit is set to 1 we know that scale is relative to the real world.**

`meshgrid` is responsible for putting both arrays together. Linspace gives you an array of all the horizontal points and vertical points in a 1D array. `meshgrid` turns both into 2D arrays and matches all the points together so now we get individual coordinates that we can refer to throughout the code. **Basically determines which x goes with which y**


```python
 # Initialize the mask
mask = np.zeros_like(X, dtype=bool)
```

Makes an array of same size as X with all fields in the array to be 0

### Singularity calculations
Singularities occur when the elbow angles are 180 or 0 degrees. At these points, small motor movements can cause massive jumps which can lead to errors in our control loops.

At 0 degrees we simply do not have enough torque from the motors. Imagine trying to open a door that is flat against a wall by just trying to rotate the hinges. Nearly impossible. Same thing applied here

##### Figure out what "infinite velocity" means

```python
 # 3. Transmission Angle (Singularity Avoidance)
        # Avoid the arms straightening out or folding flat (15-degree buffer)
        cos_gamma_max = np.cos(np.radians(15)) 
        cos_g1 = (L1**2 + L2**2 - D1**2) / (2 * L1 * L2)
        cos_g2 = (L1**2 + L2**2 - D2**2) / (2 * L1 * L2)
        comfort1 = (np.abs(cos_g1) <= cos_gamma_max)
        comfort2 = (np.abs(cos_g2) <= cos_gamma_max)
        
        # A point is only valid if it passes ALL tests
        mask = valid_reach & elbows_out & comfort1 & comfort2
```

The code above will determine if a point is valid or not by adding a 15 degree buffer to the elbow joint. If the point it's looking at happens not meet any of the criteria in the `mask = valid_reach & elbows_out & comfort1 & comfort2` then the point will be kept as a white space.
#### What calculation is done on each point
Refer to 380 notes on iPad