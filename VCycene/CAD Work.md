Okay Jesus I was just thrown into the fire to get some cad work done and I suck at cad. I haven't touched it in such a long time. So the goal right now is to design a connector for a 90 degree bit to connect to the machine because the current one we use is too heavy. 

The first part of this to reverse engineer the part we're thinking about using which is originally used for a drill. How on earth do we do this?

I understand a bit better what my goal is. I have to design a part that can be 3d printed so that I can fit it onto the 90 degree drill attachment and connect it to the machine. After connecting it we're going to test with the drill if it can rotate well as long as its connected and if it has enough power.

hole diameter: 4.2mm > 2.1mm
upper to hole: 5.88mm
side to hole: 3.50mm

distance y direction: 7.88mm
distance x direction: 5.6mm

0.2 mm

Inner diameter: 21.13 mm

Depth: 14.43 mm

Distance from outer outside edge: 3.49 mm

Screw hole diameter: 2.70 mm

We are going to use a self tapping screw M2.5 and use a hole diameter of 2.1 mm

Distance from self tap hole to outer edge: 4.09

Creating a square
Mirroring
Chamfers
Friction Fits
Using Planes and axes to your advantage when designing for symmetry
Extruded cuts
Extruding onto a curved surface

Depth of notch: 2.17 mm

Circle thickness: 1.87mm

notch width 3.65 mm actual 3.72


### Process to Creating the Attachment and Things I Learned

The goal of this project was to design a connector for a 90 degree attachment that can connect to the rotating blades inside the chamber for Lila. The problem with the attachment we use right now is that it's really expensive. After finding this drill bit attachment from Dewalt, we realized we can simply find the manufacturers that built the attachment and ask them to create one for us which we can then attach to our motors. The problem right now is that we need a way to attach the 90 degree drill bit attachment to the chamber which was what I designed. 

The solution I went with for this was to create a piece that friction fit to the attachment. It would slide into the hole of the attachment and then I would attach this to the already existing connection point on the chamber using the 4 holes. Below is an example of the finished product.

#### Creating the Base Plate
First we start with the base plate by creating a square, filleting the corners and adding holes for the screw to go in
- Thickness of 5 mm
- Height: 54 mm
- Width: 54 mm
- Corner fillets: 3.60mm

#### Creating the Mounting Holes for The Base Plate
- Diameter of hole: 4.50 mm 
	- Radius: 2.25 mm
- Distance from vertical (Center to edge): 3.85 mm + 2.25 mm = 6.1 mm
- Distance from horizontal (Center to edge): 3.85 mm + 2.25 mm = 6.1 mm

For this step I create a hole on one corner and then used the planes I had to be able to mirror the hole onto the other corner and then mirror both holes to the bottom of the base plate. In order to mirror, you need to create planes that you can mirror over if you can't use the planes that already exist (front, right, top). **We need to find out how planes are actually created when we use the reference geometry feature**

#### Creating the extruded cylinder and extruded cut
This part was pretty easy:
- Draw a circle in the middle of the base plate
- Extrude it
- Draw another circle in the middle of the extruded part you just created
- Extruded cut it
- Add a chamfer

#### Adding the notch
This was the hardest part of the entire process:
- We need a plane to sketch on so we draw one using the reference geometry tool to draw a plane tangent to the extruded curved surface
- Then we draw our rectangle inside this plane
- After that we align the midpoints of the rectangle we have with the edge of the back plate so they are aligned
- Then we extrude in two directions 
	- One direction is up to the surface because solid works doesn't like when you have a part with edges or corners connected to a curved surface
	- The other is the actual height you want the notch to be


### 90 Degree Attachment
Outer Diameter: 24.90 mm
Long length: 43.83mm
Short length: 30.93 mm
Hole Diameter: 2.64mm
Counter sink hole diameter: 5.40mm
- Radius: 1.34mm
Edge to hole (center): 4.95 mm


#### Creating Countersink Holes on A Curved Surface
- To create a counter sink hole on a curved surface you need to use the hole wizard
- **As a rule of thumb, the best way to do any kind of operation on a curved surface is to use a plane as a reference geometry and draw on there. Much easier when you set up a reference plane tangent to the surface you want to work on so you can draw things like center points, center lines**
- For our case we created a plane that was tangent to the surface we wanted to work on 
- The we drew a construction line from the end edge of the curved surface to the distance we wanted the countersink hole to be from the edge
- Then we created the hole using the hole wizard at the point of 4.95 mm using the construction line as the guide