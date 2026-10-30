### Chapter 1: 
##### Question Bank
**What is an entity in solid works?**
- Entities just refer to a geometric building block that you use to create sketches or models
- Sketch entities
	- Lines, arcs, circles, points, centerlines, slots, etc.
- Geometric entities (3D)
	- Vertices 
	- Edges
	- Faces
- So whenever we refer to entities we just mean any geometric thing we threw into the model that we're going to use for a function like mirror entities

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
- Radius will be the same but center points are free to move

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
	![[Pasted image 20261023154418.png]]

This means that sketch 2 is a shared sketch amongst multiple features. So one sketch is used for two different features


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

**Video 14 - Final Sketching Exercise**
https://www.youtube.com/watch?v=uRjv0BMbHxk&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=14

Here is the folder everything backs up to
C:\Users\kisho\AppData\Local\Temp\SWBackupDirectory\

**Tips**
- You can create an arc in a sketch by clicking the line tool then clicking a point you want to start your line at and then you extend the line by hovering your mouse and the hover the mouse back to the point you started your line and then hover away and you will see an arc form
- Do sketch relations first them dimensions later
	- In some cases you have to put the dimension in first
	- Whatever gets you to where you need to be
- Tangent is just a relation that you can create it doesn't mean you have to use the tangent arc creation tool
- Sketching is just a means to get the shape you want

![[Pasted image 20261023174253.png]]



### Chapter 2

##### Question Bank
**What does blind mean when adding an extruded cut or extruded boss/base in a feature**
- To make this cut using the blind end condition, you will determine a fixed measurement or depth that the cut or boss/base will be extruded to


**Video 15 - What is design intent**
https://www.youtube.com/watch?v=dCVD3hh0p-s&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=15
This video is just about thinking about how you're going to sketch a part before you actually sketch it
- Where the origin is going to be
- What entities you should use
- What feature tools will be use and what order that will be in 
- What design changes will be made in the future and how will you make changes after this
- What is the most efficient and effective way to make a part

Getting good at design intent comes from experience 


**Video 16 - Feature End Conditions**
https://www.youtube.com/watch?v=BdkBCx56das&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=16

End conditions can be set by a specified distance or by using existing 3D geometry

Types of end conditions defined by distance
- Blind (defined by set distance away from sketch plane)
- Through all (continues through the entire shape)
- Mid plane (defined by set distance away from sketch plane)

Types of end conditions defined by geometry
- Up to next
- Up to vertex
- Up to surface
- Offset by surface
	- Requires distance to know how far away from the surface the feature should be
- Up to body

Extruded Boss Base Using Midplane End Condition
- Does not need existing geometry to do so
- It will basically extrude a sketch a specified distance equally about the plane that you suggested

How to add a feature
- Select the sketch and the click the features tab


Cutting from a surface/plane
- This will make the cut that you picked and start it from a surface that you select instead of from the cut location
![[Pasted image 20261025130420.png]]



Making a circular cut
- We're going to make a cut that ends on the angled face of the block 
- We're going to use the **up to next** end condition to do this
- When we make the circular cut, we don't want the cut going through any existing geometry so instead we can say up to next and the cut will only go up to the next detected face
![[Pasted image 20261025131359.png]]

**Through all end condition**
- This will make sure that no matter how the block changes in height the cut will always go through the entire block

**Through all both**
- Will cut through both directions all the way through for infinity without depending on the height of the block



**Video 17 - Extrude Boss and Extrude Cut**
https://www.youtube.com/watch?v=D5LgocNY7fo&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=17
Two most common types of extrusions
- Extrusions are used to create 3D geometry from a 2D sketch

Extruded Boss
- Used to add material by taking a 2D sketch and extending it in the path perpendicular to the sketch 

Extruded cut
- Used to remove material by extending a cut through the path perpendicular to the sketch profile

Co radial relationship
- Just making sure that two circles are always touching and their radii are always the same

Thin feature
- This is good for creating things like tubes
- If you were to make a tube you might do two circles with a smaller one within a larger one and then extrude both circles to give it a thickness
- Thin feature allows you to add this thickness on a 2d sketch with one circle

**Video 18 - Editing Parts**
https://www.youtube.com/watch?v=w2j3VMg1CAg&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=18

The blue line at the bottom is the roll back bar and it takes you back through time to see how the part was made from start to end
![[Pasted image 20261025141252.png]]

Parent feature
- A feature that depends upon other features in order to be defined

Child feature
- Existing feature that depends on an existing parent feature

![[Pasted image 20261025141758.png]]

This tells us that the shell feature is dependent on the three features listed below it. You can access this by right clicking on the feature in the feature menu and clicking parent child

Concentric
- Makes sure that two circles are always aligned by their center points

**Video 19 - Revolve Feature**
https://www.youtube.com/watch?v=lOaP4yCa3f4&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=19

Revolve feature
- Same as extruded feature but instead of using a linear path that's perpendicular to the sketch profile it uses a circular path about an axis
- Revolves require axis symmetry geometry and an axis to be referenced as the sketch 

Create a diameter dimension
- Select two lines for dimensioning and then go to the other side

![[Pasted image 20261025143119.png]]

![[Pasted image 20261025143106.png]]

Revolved Boss Base
![[Pasted image 20261025143449.png]]

Revolved cut
![[Pasted image 20261025154532.png]]

Making a revolved cut
![[Pasted image 20261025154742.png]]

We drew a rectangle that covered the part of the revolved cut that we want to remove. The width of the rectangle becomes the depth of the cut we want

![[Pasted image 20261025154855.png]]

Cross sections:
![[Pasted image 20261025154955.png]]


**Video 20 - Fillets and Chamfers**
https://www.youtube.com/watch?v=YP8Kx50he3g&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=20

Fillets provide rounded edges
Chamfers provide angled edges

Some general rules for fillets and chamfers
- Leave cosmetic fillets until the end of the modelling process
- Create multiple fillets in the same command if they have the same radii
- If you need fillets with different radiuses its best to create fillets with largest radius
- Order in which you create fillets is important because it creates surface faces

**Video 21 - Hole Wizard**
https://www.youtube.com/watch?v=yU--k49CvVM&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=21

Fast and easy tool used to create many different types of holes

**Video 22 - Linear Pattern Tool**
https://www.youtube.com/watch?v=NRU2xCRm-u8&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=

Allow you to replicate geometry in a linear fashion
- You can do it a single direction or two separate directions

The copies are referred to as instances and are based on the original geometry which is called the seed
- If the seed changes its geometry than the instances will also change

Where to access the linear pattern feature:
![[Pasted image 20261025170339.png]]

Direction 1:
You can select an line or an edge to indicate which direction you want the pattern to travel in

Features and Faces:
You can select features or faces in this tab to determine what exactly you want to create a pattern of

Pattern seed only:
![[Pasted image 20261025171223.png]]

Checking this box tells solid works if you want to copy the patterns you already created or if you only want to pattern the seed

Instances to skip:
![[Pasted image 20261025171356.png]]

Allows you to select specific copies and delete them from pattern

Click on the linear pattern in the tree it'll show you which feature is the seed geometry
![[Pasted image 20261025171541.png]]

Instance to vary linear pattern feature:
![[Pasted image 20261025171917.png]]

![[Pasted image 20261025171929.png]]

This means that the distance between each copy will increase by 10 mm along the direction line you selected

You can also vary the dimensions of the feature by clicking on the dimension assigned to them and have them increase or decrease gradually
![[Pasted image 20261025172221.png]]

Here's the result
![[Pasted image 20261025172356.png]]

Summary on how to use linear patterns:
- Click on linear pattern in the features tab
- Click direction 1 to determine in which direction you want the features to copy over by
- Select if you want the features to copy by spacing and instances or up to a reference
- Go to features and faces and click on the feature you want to copy 
- You can select a second direction to copy in as well
- You can select pattern seed only if you only want to copy the seed geometry in a different direction and not everything
- You can select instances to skip if you want to create your own patterns
- You can select instances to vary if you want to vary the spacing between each instance or vary the dimensions of each instance (making the instance bigger or smaller)

**Video 23 - Circular Pattern Feature**
Circular pattern tool
- Used to create a pattern in a circular fashion about an axis
![[Pasted image 20261025173252.png]]

Nothing really crazy to it, its the same as linear pattern and pretty intuitive
- As a not its easier to select features you want a pattern of from the tree instead of selection on the geometry itself


Creating an axis:
In situations where you don't have a reference point to draw a circular pattern around you can create an axis as the reference point of which you will draw the pattern around.
- Click reference geometry
- Then click axis
- Then click origin in the design tree and you'll see an axis form 
- This axis is also what you'll use to determine the direction you want the pattern to move in 

**Video 24 - Mirror Feature**
https://www.youtube.com/watch?v=OQgtM7Pc4pA&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=24

Allows you to replicate geometry about a plane or planar face
- If you have a symmetric design then you can copy it over and move it to the other side 


Mirror Tool
![[Pasted image 20261025174550.png]]

First step is to select the plane you want to use as your mirror

Second step is to click the features you want to mirror over the plane
- You can use the tree on the side to select or you can click the feature itself too

You can also create your own custom plane if needed to mirror over

The mirror feature will mirror the feature as well 

Toggling this will enable if you copy just the geometry or the geometry and the end conditions

You can use the body to mirror option if you want to mirror the entire body instead of just some of the features
- Remember that once you mirror a feature, the original feature can be modified and it will propagate to all other features that were mirrored

**Video 25 - Sketch and Table Driven Patterns**
https://www.youtube.com/watch?v=t9rZ4dToZG4&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=25

Sketch driven pattern:
This allows you to draw patterns given a sketch that you draw instead of it being a repeated linear or circular pattern so it can be random

To do this you do the following:
- Draw a construction line
- Draw points of where you want your feature to appear
- Select the sketch you want to use after clicking the sketch pattern
- Then click the feature you want to copy and make a pattern for and it will copy over the points

Table driven pattern:
This is another good tool to use if you want to place the same features in random spots rather than have the pattern be repeated a set amount by a set distance like the linear and the circular patterns allow for.

To do this you do the following:
- Insert a coordinate system
- Add a table driven pattern using the linear pattern drop down
- Select the feature, face, or body you want to use 
- Select the coordinate system
- Input values where you want your features to end up as if you were working with a coordinate system

**Video 26 - Curve driven pattern**
https://www.youtube.com/watch?v=CK03fJSf7uk&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=26

How to use curve driven patterns:
As a not remember that the **covert entities** tool will project existing 3D model edges, faces, curves or 2D sketches onto the active sketch plane

- Use convert entities to get a sketch of  the edge of the 3D geometry 
- Go to features and click curve driven pattern 
- After this you apply the pattern like you would for any of the other patterns

**Video 27 - Fill Pattern Tool**
https://www.youtube.com/watch?v=1GVLeS8uZXE&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=27

What is the fill pattern tool:
The fill pattern tool allows you to use existing features or pre defined shapes to fill a boundary area

Where is the fill pattern tool:
![[Pasted image 20261025195836.png]]

Property manager functions after selection fill pattern:

![[Pasted image 20261025195916.png]]

This is going to be the area where you want the fill pattern to be. It can be a sketch, a face or multiple faces as long as they are co planar.

![[Pasted image 20261025200121.png]]

The features and faces tab is where you select which faces or features you want to use to fill the boundary.

Selected feature means it'll use the sketch that you created. 

Create seed cut will give you some options of predefined shapes you can use to fill the boundary.
![[Pasted image 20261025200252.png]]


The pattern layout is used to organize how you want the fill to be organized on the selected boundary
![[Pasted image 20261025200807.png]]

They do this in the following order from top to bottom:
- Spacing between different holes
- Degree of offset diagonally between holes
- Spacing away from the edge of the part

**Video 29 - Reference Planes**
https://www.youtube.com/watch?v=69BVWOW_jaY&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=29

Reference Planes
- Sometimes reference planes need to be created in addition to the existing planes to create new sketches and geometries
- Can be created by selecting geometry to locate and identify the new planes
- Can be created by selecting the following
	- Existing planes
	- faces
	- edges
	- vertices
	- surfaces

Creating a reference plane
![[Pasted image 20261029125855.png]]

Go to the features tab, then reference geometry then plane


![[Pasted image 20261029130244.png]]

When creating a new plan you have the option of selecting different ways you want the plane to be placed. The plane needs to be fully defined. For some of the options like parallel, perpendicular and angle, you may have to add a second or third reference to make sure the plane is fully defined.

**Video 30 - Part Configurations**
https://www.youtube.com/watch?v=OtAWg0bWVUc&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=30

What are part configurations:
They allow you to represent more than 1 version of a part within the same file

You can do things such as:
- Independently controlling the size of certain features
- Assign materials to a part
- Suppress and unsuppressed features in a part

 How to access configurations
 ![[Pasted image 20261029131447.png]]

![[Pasted image 20261029131616.png]]

Double clicking will allow you to switch between configurations

When changing a dimension you can select which configuration its going to apply to
![[Pasted image 20261029131801.png]]

If you right click on a feature in the feature manager you should get an option to suppress a feature which will effectively hide the features

You can right click on a feature in the feature manager and also click something called configure feature which will allow you to to suppress whatever you suppressed in the current feature to other configurations you have
![[Pasted image 20261029133102.png]]

In this case in the no holes configuration we suppressed the the cut extrude feature and if we click the check boxes for the other configurations it will also apply the same suppression.

**Video 31 - Part Design Tables**
https://www.youtube.com/watch?v=hVW0rUWOr0M&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=31

What are Part Design Tables
- They are embedded Microsoft excel spreadsheets used to automatically generate and manage multiple configurations of a part or assembly within a single file
- You can control feature dimensions, sketch relations, suppression states, materials and equations

How to create a design table
![[Pasted image 20261029134629.png]]

To add a new feature to control with the design table you click the next free column and then double click on the feature you want to control with the design table in the feature manager
![[Pasted image 20261029135318.png]]

If you ever click of your design table you can access it again by clicking in the configurations manager tab and then right clicking on the design table and clicking edit table
![[Pasted image 20261029140145.png]]

You can also add dimensions to the design table by clicking the next empty cell and the double clicking on a dimension

The main use for design tables is to make multiple configurations of one part so that you don't need one file for each one of the parts especially when only a couple of dimensions are being changed.

This is good for when you get the CAD model of your aluminum extrusion and you want to modify it to different cut lengths for the different parts of the chassis.


### Chapter 3: Assemblies

**Video 32 - Points, Axes and Coordinate Systems**
https://www.youtube.com/watch?v=9WqPPt4QEzk&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=32

Reference geometry
- Soildworks provides reference geometries to help with the creation of sketches, features and 3D geometries
- Reference geometries are things like planes, points, axes, the origin, etc.
- Reference geometries updates with the points its linked to

Finding the reference geometry tab in an assembly
![[Pasted image 20261029145346.png]]

This lesson wasn't that useful for me right now. I don't really see how I can use anything that he showed us. He just kind of went through a tutorial of how to use some of the features that the reference geometry enables. Probably more useful in the upcoming lessons about assemblies.

**Video 33 - Bottom Up Assembly**
https://www.youtube.com/watch?v=OaTo_UELqeA&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=33

Bottom Up Assembly
- In this approach what happens is that all the parts are created individually first in their own files and then an assembly file is created where all the parts are put together into the assembly file and then they are constrained using something called mates

Top Down Assembly
- All parts are created in the assembly file itself

Creating an assembly
- You can click file and then new and then you'll get a pop up that will allow you to select assembly
![[Pasted image 20261029151356.png]]

Changing units in your assembly (because sometimes your part might be in a different unit than the assembly)

Go to the gear icon on the top, then click options, then go to document properties then go to units and then select the unit system you want to use
![[Pasted image 20261029151522.png]]

The first part will be locked once you place it in the assembly. If you want to move around you can right click on the assembly and click float to move it around


Adding new components
- They are set to float by default

![[Pasted image 20261029152229.png]]

You can go to insert components in from the assembly tab and the click browse to browse your documents and then insert the components you want in the assembly file


Rotating and moving components in the assembly
- Right clicking and moving a component will rotate the component
- Left clicking and moving a component will move the component

You can click the assembly tab and then the move component tab and this will give you specific options on how to move a part
![[Pasted image 20261029152753.png]]

If you right click on the part and click move with triad you get more precise control over the part and how it moves
![[Pasted image 20261029152918.png]]

Mates will determine the degrees of freedom a part has in an assembly and its exact position and location in reference to another part, point or axis

If you need multiple of the same parts in one assembly you can click control and then click and drag the part you want from your feature manager
![[Pasted image 20261029153324.png]]

Mates
- You can think of mates as the glue between parts and what's responsible for holding the parts together

To access the mates tab you click the assembly tab and then click the mates tab
![[Pasted image 20261029153522.png]]

To make a mate, select the face of one of your parts and the the face of the other and solid works will attempt to make the mate that makes the most sense. In this case we selected to circular entities so it tried to make the concentric mate
![[Pasted image 20261029154050.png]]

The two arrows button will flip the mate around
![[Pasted image 20261029154502.png]]

Because the wheel was the first component we added in it is fixed and we can't move it like how its supposed to. This means we have to add some additional constraints.
- To do this what we did was mate the original planes in the assembly file with the planes from the plate part so that they would lock together and constrict its movement

**Video 34 - Move and Rotate Components**
https://www.youtube.com/watch?v=-7-HrkPmv6Y&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=34

A component that is underdefined will appear with a minus sign in the feature manager tree
- This indicates that component has some freedom of movement in the assembly
- Left clicking and moving will translate the component
- Right clicking and moving will rotate the component
- You can also right click the component and then click "move with triad" to move a component with more accuracy

Move component tool
![[Pasted image 20261030145441.png]]

You can enable collision detection which will give you an option to check "stop at collision" which means the part will stop moving if it comes in contact with another part

To determine if two components are colliding you can do the following:
- Move component
- Collision detection
- You can select either all components or these components
- If you select these components then you can select two specific components that you want to ensure will not collide
- Also make sure to click resume drag after selecting your components so that you can still use the move component tool

To find the minimum clearance between two components that are moving you can use the dynamic clearance tool
![[Pasted image 20261030151041.png]]

This will tell you what the minimum clearance is off the part once you start moving it. You can also click the button underneath where the 10 mm is greyed out to set a minimum clearance where the part should stop moving


**Video 35 - Standard Mates**
http://youtube.com/watch?v=N23mwzKX6KY&list=PLTR9k4JrYfbSfK385Zpnm91abLczy_P4S&index=35

Mates
- Mates are used to create relationships between different parts within an assembly

Different types of standard mates
- Coincident mate
- Parallel mate
- Perpendicular mate
- Concentric mate
- Distance mate
- Angled mate
- Lock mate

What can be selected to make mates
- Two faces or surfaces
- A line or linear edge
- You can select planes
- Axes or temporary axes
- A point
- A vertex
- A coordinate system
- An arc
- A circular edge


Coincident Mate
- 