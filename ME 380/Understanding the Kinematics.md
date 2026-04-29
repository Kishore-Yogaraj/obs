vfKinematics = geometry of motion, ignoring forces

There are two main questions:

**Forward Kinematics (FK)**
"If I set the motors to these angles, where does the end effector end up?"

**Inverse Kinematics (IK)**
"If I want the end effector here, what motor angles do I need?"

Finding the IK for parallel robots is easier than finding the FK

**ICCAIS Paper Terminology**
Let's define it cleanly:
- Two motors fixed to the base:
	- Left motor at point Ai
	- Right motor at point A2
- Each motor drives a proximal link of length l1
- Each proximal link connects to a distal link of length l2
- The two distal links meet at the end effector E

```
A₁ —(l₁)— P₁ —(l₂)— E —(l₂)— P₂ —(l₁)— A₂
```

In a 5 bar:
"position - angles that satisfy two geometric constraints"


### Inverse Kinematics

Let's say the end effector target is:

```
E = (x,y)
```

Let's just look at the left arm

The end effector must lie on a circle:
- Center: A1
- Radius: L1, L1 (via joint P1)

![[Pasted image 20260127160941.png]]

More precisely we can say:
- The elbow joint P1 must satisfy :
	- Distance (A1, P1) = L1
	- Distance (P1, E) = L2

The same logic applies to the right arm.

The most important insight is as follows:
"A 5 bar IK = two independent 2 link IK problems that must agree on the same end-effector point"


### The law of cosines step (where alpha comes from)

From the ICCAIS paper, for the left arm:

Distance from base motor to target:
```
r1 = sqrt((x + lo)^2 + y^2)
```

**lo is half of the motor spacing**

We can apply the law of cosines to find the elbow angle:
```
cos(α₁) = (l₁² + r₁² − l₂²) / (2 l₁ r₁)
```

With the law of cosines you can two possible values for the elbow angles which are **positive** and **negative**

Positive: (elbow facing out)
Negative: (elbow facing in)

### The direction angle (where beta comes from)

Simply put it is:
```
β₁ = atan2(y, x + l₀)
```

This tells us:
"If the arm were fully stretched toward E, what direction would it point in"

You can think of the direction angle as the **compass heading** from the motor to the target. Without it, your robot knows how much to bend its arm but it doesn't know whether to poiont that arm forward, backward, or sideways

![[Pasted image 20260127162306.png]]

### Assembling the joint angles (this is IK)

Now we combine geometry:

Left motor angle:
```
θ₁ = β₁ ± α₁
```

Right motor angle:
```
θ₂ = π − β₂ ± α₂
```

This is one of the most confusing parts of 5-bar kinematics because it involves moving from **pure geometry** (triangles) to **robotic reality** (how the motors are actually bolted to the frame).

The $\pi - \beta_2$ term exists to handle **symmetry**.

#### The Global vs. Local Reference Frame
In a 5-bar parallel robot, you have two motors (Left and Right). Usually, we define the "Zero" position for both motors such that they point **inward toward each other** or **outward along the X-axis**.
- **Left Motor ($M_1$):** Its natural "zero" points to the right (positive X-direction).
- **Right Motor ($M_2$):** Its natural "zero" points to the left (negative X-direction).
Because the motors are "facing" each other, the way we measure their angles is mirrored. If you told both motors to go to $10^\circ$ without this $\pi$ correction, one would point right and the other would also point right. They wouldn't be symmetrical.