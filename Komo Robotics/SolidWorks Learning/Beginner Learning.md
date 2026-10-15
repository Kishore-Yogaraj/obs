### Chapter 1: Sketching
**Video 1**
https://www.youtube.com/watch?v=atLRraBiVXI&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S
- Ctrl + middle scroll wheel will pan 
- Ctrl + 7 will go to isometric view
- Ctrl + 1 will show you everything on the screen
- Exit sketch with saving and without saving (The red x)
![[Pasted image 20261015125649.png]]
- A lot of solid works and designing will be about drawing a 2D sketch and then manipulating it in some way to get a 3D shape


**Video 2**
https://www.youtube.com/watch?v=RMw0OXb8P10&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=2
Sketch relations 
- A way to force relationships that must stay true throughout your design process

**Line sketch relations**
Coincident relation
- Two sketches must always be touching no matter what changes happen

Perpendicular icon is the view sketch relations icon to tell you what relations exit between sketches. (Make sure you are in the edit sketch mode to use this)
![[Pasted image 20261015131710.png]]

Merge relation
- Will attach two points together throughout all changes

Parallel relation
- Two lines following the same angle at all times

Perpendicular relation
- Makes two lines perpendicular

Collinear relation
- Two lines that always follow the same axis
- The two lines will always follow each other

Horizontal relation
- Makes a line perfectly horizontal

Vertical relation
- Makes a line perfectly vertical

Equal relation
- Makes two lines equal to each other the entire time

Anchor relation
- Makes a point immovable

Midpoint relation
- Endpoint will always be in the middle of another line

Symmetric relation
- Need to draw a construction line
- Then you can select two points or lines or what not and they will always be symmetrical along the construction line you built

**Arc Sketch Relations**

Co-radial relation
- Make radius the same for two arcs
- Make the center point of two arcs are merged to the same point

Concentric relation
- Center point will match to a certain point but the radius can still be adjusted for both circles

Tangent relation
- Will always be touching at the intersection point through all changes

Equal relation
- Radius will be the same but center points are free to movs

**Dimensioning**
- Gives you straight line measurements, diameter measurements and angles
- Double click on dimension to edit the dimension


**Video 3**
https://www.youtube.com/watch?v=upOEUGDHhcw&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=3

**Lines and Centerlines**
Centerline
- Just a construction line without having to draw a line and then label it "for construction"
- Can't be used for features (extrude, cut, etc.)


**Video 6 - Ellipises and Slots**
https://www.youtube.com/watch?v=2VhpLUDUroU&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=6

**Video 7 - Fillets and Chamfers**
https://www.youtube.com/watch?v=RsKM71l7lLk&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=7

Fillets are curve corners and chamfers are straight edge corners

Fillets
- You add by selecting 2 entities (lines)
![[Pasted image 20261015144302.png]]

- Keep constrained corners will make sure fillets don't remove any relations or constraints placed
- Dimension each fillet allows you to individually dimension each fillet instead of using the same radius for all fillets

**Video 8 - Trim and Extend Entities**
https://www.youtube.com/watch?v=DMXzlG2xz4E&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=8
Power trim is the best tool to use for trimming entities. It's a pretty intuitive tool that you should be able to use to remove parts of entities that you don't want.

**Video 9 - Mirror Entities**
https://www.youtube.com/watch?v=LfraP3tds1I&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=9

Use the mirror entities icon then select an entity and then a construction line you want to copy onto the other side of and then it will copy over

You can use dynamic mirror entities if you want to create a mirrored geometry while you're building


**Video 10 - Offset Entities and Convert Entities**
https://www.youtube.com/watch?v=AfJJhh6cjL8&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=10

Offset Entities
- Allows you to offset entire entities or sketches by a certain amount as a whole

Convert Entities
- Allows you to move entities from one plane to another plane
- It's basically just used to copy over sketches from one plane to another plane so you don't have to draw it again
- It gives you a constraint to the original sketch but that can be deleted if needed

**Video 11 - Contour Select Tool**
https://www.youtube.com/watch?v=aHIppfubM2k&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=11

Contours
- These are just regions within a larger sketch
- Allows us to select specific regions in a sketch which we can then use to make features of this region
- The major advantage with this feature is that you can use one sketch for multiple 3D features instead of having to make 2 separate sketches with two separate features
	- If you did the ladder you would have to go to one sketch, edit it then go to the second sketch and edit it whereas with the contour select you can edit all features within one sketch 

**Video 12 - Global Variables and Equations**
Used to create mathematical relations between dimensions
- Using global variables and equations you can fully leverage parametric models within solid works
- This just makes it pretty easy to edit the dimensions of parts without having to go into the sketch 

Steps to use global variables
- Go to tools
- Click equations
- Give the global variable a name
- Give the global variable a value
- Leave a comment if you want
- Click OK
- Click on a dimension
- Click = and enter the global variable

![[Pasted image 20261015171455.png]]

If you have automatic rebuild selected in the bottom left then when you update the values in the global variable it will automatically update it in the model

**Video 13 - Fully Defining a sketch**
https://www.youtube.com/watch?v=8XhXDbY6buo&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=13

Just move entities around to see what is exactly changing and the set your relations and dimensions

Underdefined
- When it doesn't have enough sketch relations and dimensions to fully constrain the geometry
- Lines are blue

Over defined
- When you have too many dimensions trying to control the same geometry

Fully defined
- When you have the right amount of sketch relations and dimensions to fully constrain the geometry
- Lines are black

Fully defining a sketch is best practice because it makes it so that you can create features without having to worry about issues with geometry 