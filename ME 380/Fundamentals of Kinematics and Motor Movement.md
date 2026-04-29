### Kinematics and Code Break Down

#### Questions that need to be answered:
- Where do we pick a coordinate on the plot?
- How does that become joint angles?
- How do those angles become motion on screen?
- What parts are "IK", what parts are "FK", and what parts are "motion planning/safety"?
- How do we map this to a real robot?

#### Mental model of a 5 bar IK 
Let's begin by developing a mental model of what the 5 bar scara looks like and how it's defined in the code

We have two motors fixed at:
`A1 = (L0, 0)` (left base)
`A2 = (+L0, 0)` (right base)

The values for the motor locations are defined in the parameters for the code which can be found here:
```python
# =========================
# User Parameters (mm, rad)
# =========================
L0 = 50.0   # half base separation (mm)
L1 = 140.0   # proximal link length (mm)
L2 = 200.0  # distal link length (mm)
```

Each motor is responsible for driving a **proximal** link of length L1 to an elbow. The left elbow is **P1** and the right elbow is **P2**

Then each elbow connects to the end effector with a "distal" link of length L2

The geometry constraints are as follows:
**‖P1 − A1‖ = L1**
**‖E − P1‖ = L2**
**‖P2 − A2‖ = L1**
**‖E − P2‖ = L2**

![[File_000.png]]

**Inverse kinematics (IK):** given desired end-effector `E = (x,y)` solve for the motor angles `θ1, θ2`

The code written solves IK by finding **elbow candidates via circle intersection** in an algebraic form via law of cosines
- This basically means we're finding the angle that needs to be made between the proximal and distal arms

![[Pasted image 20260210160925.png]]

#### Where do we pick a coordinate on the plot

```python
self.fig.canvas.mpl_connect("button_press_event", self.on_click)
```

This will tell matplotlib: "When user click, call `self.on_click(event)"

In the `on_click` method:

```python
x, y = float(event.xdata), float(event.ydata)
E = np.array([x, y], dtype=float)
self.queue.append(E)
```

- **`event.xdata, event.ydata` are the clicked point in plot/world coordinates**
- It becomes a numpy vector `E`
- It gets pushed into `self.queue` (so you can click multiple targets)

In the code, we define a max workspace that we want the entire matplotlib grid to be which is defined here:
```python
R = L1 + L2
self.ax.set_xlim(-R - L0 - 20, R + L0 + 20)
self.ax.set_ylim(-10, R + 40)
```

We are saying here that we want the left edge of the matplotlib window  to be at -410mm on the left edge and 410mm on the right edge.

And for the y-axis we say we want the bottom to be at -10mm and the top to be 380mm.

Given that `L1 = 140 and L2 = 200` we defined a workspace of 820mm x 390mm

#### How does the queued target trigger motion?

In every animation frame, the `step()` function runs:
```python
def step(self, _frame):
    self.update_state()
    self.draw()
```

Motion logic is contained in `update_state()` 

At the start of this function we run this line:
```python
self.start_next_target_if_idle()
```

This function decides: "Are we currently moving? If not, do we have a queued target to start?"

Inside the above function we determine the next goal point (end effector):

```python
E_goal = self.queue.popleft()
self.target_E = E_goal
```

Then we also do a quick **reachability check**

```python
# Basic reachability check for endpoints
if not reachable_from(A1, E_goal) or not reachable_from(A2, E_goal):
	msg = f"Unreachable target ({E_goal[0]:.1f}, {E_goal[1]:.1f}) mm: outside workspace."
	self.last_status = msg
	print(msg)
	return
```

The function `reachable_from(A, E)` checks whether a 2-link arm with lengths L1 and L2 can reach point from the base A:

![[Pasted image 20260210163242.png]]

```python
def reachable_from(A, E):
	r = np.linalg.norm(E - A)
	return (abs(L1 - L2) <= r <= (L1 + L2))
```

After we get past this check we need to **determine where our current position actually is.**

Even though we clicked a target, the robot is currently sitting at some specific joint angle ($\theta_1, \theta_2$). In order to plan a smooth straight line path from the current position to the goal position we need to extract the exact coordinates of the tip of the end effector right now. So we use **Forward Kinematics** to translate those **angles** into the a **coordinate**:

**Input:** Current motor angles ($\theta_1, \theta_2$)
**Output:** Current tip position (*Estart*)

Without this, the robot wouldn't know where to draw the **start** of the line for the trajectory.

#### Understanding the Forward Kinematic Calculations:
To understand how we find the end-effector position, think of the robot as two compasses. The "joint angles" ($\theta_1, \theta_2$) tell us exactly where the "elbows" ($P_1$ and $P_2$) are. Once the elbows are fixed, the distal links ($L_2$) act like two arms swinging from those elbows.

The end-effector ($E$) **must** be exactly $L_2$ distance away from $P_1$ AND exactly $L_2$ distance away from $P_2$. Geometrically, this means $E$ is where two circles intersect.

![[File_000 (1).png]]

Notice above how we get two intersections when we draw a circle using P1 and P2 as the center and L2 as the radius.

**When doing the forward kinematic calculations to determine where the current end effector position is, we need a way to let the robot know which intersection it's actually at.**

#### The Geometry of the Intersection
The code solves for the intersection of two circles centered at P1 and P2 with radius L2.
- **The Midpoint:** We find the line connecting P1 and P2. The point `mid` is halfway between them
- **The Perpendicular Vector:** The intersection point must lie on a line perpendicular to the line from P1 to P2. In the code, `perp` is that 90 degree vector
- **The offset (h):** We use the Pythagorean theorem to find how far from the mid point we need to travel along that perpendicular line to be exactly L2 away from the centers

![[File_000 (2).png]]

Because we can go "up" or "down" from that midpoint, we get two candidates:
`E1 = mid + h * perp`
`E2 = mid - h * perp`

**How do we choose the right point?**
We use the `prev_E` logic.

**The Logic:** If E1 is 0.5 mm away from the previous position and E2 is 150 mm away, E1 is clearly the correct **continuation** of the movement.

In the real world when we implement this, it is equivalent to **state tracking.** The last calculate position stored in the controller's memory is the **previous E.**

#### Walk through of Forward Kinematics (Used for Trajectory Planning)
Forward Kinematics (FK) for a 5-bar robot is the process of finding the end-effector position E(x,y) when you already know the motor angles θ1​ and θ2​.

The math follows a clear "Chain of Command": first find the elbows, then find the intersection of the two distal arms.

![[Pasted image 20260210171405.png]]

![[Pasted image 20260210171421.png]]

![[Pasted image 20260210171520.png]]

![[Pasted image 20260210171538.png]]

After calculating the two possible points that the end effector could be. We then check to see which end effector coordinate it actually is, using our `prev_E` logic:
```python
candidates = [E1, E2]
candidates = [E for E in candidates if E[1] >= Y_MIN - 1e-9]
if not candidates:
	return None

if prev_E is not None and len(candidates) == 2:
	# choose closest to previous end-effector position
	d1 = np.linalg.norm(candidates[0] - prev_E)
	d2 = np.linalg.norm(candidates[1] - prev_E)
	E = candidates[0] if d1 <= d2 else candidates[1]
else:
	# fallback: choose higher y
	E = max(candidates, key=lambda p: p[1])

return E, P1, P2
```

#### Inverse Kinematics
Now that we have our current end effector position and our goal end effector position we need two more things:
- Find the motor angles that will get us to our goal end effector position
- Create a trajectory plan to get from our current position to our goal position 

![[File_000 (3).png]]

Refer to above diagram for below calculations.

**Splitting the Problem**
The function `all_ik_solution(E)` treats the robot as two separate arms:
**Left arm:** Fixed at A1, reaching for E
**Right arm:** Fixed at A2, reaching for E

For each arm, we know the location of the shoulder (A), the location of the target (E), and the lengths of the two bones (L1 and L2). **This creates a triangle where all of our side lengths are known.**

**The core math** `two_link_elbow_candidates`
This is where the **Law of Cosines** does the heavy lifting.

First we find the **global angle beta**:
We find the angle of the straight line pointing from motor A directly to target E
```python
v = E - A 
beta = np.arctan2(v[1], v[0])
```

Next we need to fine the **interior angle** **alpha:**
We need to know how much the first link (L1) must deviate from that straight line to ensure the second (L2) can touch the target. Again we do this using the **Law of Cosines:**

![[Pasted image 20260210174206.png]]

Solving for alpha:
```python
c = (L1**2 + r**2 - L2**2) / (2.0 * L1 * r)
alpha = np.arccos(clamp(c))
```


Next we need the two Elbow Options:
For every single arm, there are two ways to reach a point: **Elbow Left** or **Elbow Right**

![[Pasted image 20260210174946.png]]

In code:
```python
th_plus = beta + alpha
th_minus = beta - alpha
```

Combining the solutions:
Because each of the two arms has 2 possible configurations we end up with **4 ways** the whole robot can hold the end effector at point E.

The function `all_ik_solutions` generates all four combinations (+ = elbows out, - = elbows in):
- ++
- +-
- -+
- minus and minus

Selecting the solution we want:
This is where the `select_elbows_outward` function comes in. It looks at all 4 candidates and picks the one that matches our "Elbows Out" preference.

**Check side of Base**: It looks at the x-coordinate of the elbows ($P_1$ and $P_2$). If the left elbow is to the left of its motor and the right elbow is to the right of its motor, it gets a score of **0 (perfect)**.

We use this code to determine what the position of the elbow angle currently is:
```python
P_plus = A + L1 * np.array([np.cos(th_plus), np.sin(th_plus)])
P_minus = A + L1 * np.array([np.cos(th_minus), np.sin(th_minus)])
return P_plus, P_minus
```

**Check continuity:** If there are multiple "outward" solutions (or none), it picks the one where the resulting $\theta$ values are closest to the robot's **current** angles (`theta_prev`).

Function for making sure we pick the elbows to be outward:
```python
	def select_elbows_outward(E, theta_prev=None):
	"""
	Choose "both elbows outward" (geometric meaning):
	  left elbow P1 on outer side of A1 => P1.x <= A1.x
	  right elbow P2 on outer side of A2 => P2.x >= A2.x
	
	If theta_prev is provided, use it as a secondary tiebreaker for continuity.
	
	Returns: chosen_solution_dict, all_solutions
	"""
	sols = all_ik_solutions(E)
	if sols is None:
		return None, None
	
	def outward_penalty(P1, P2):
		# 0 if on correct side, quadratic penalty if on wrong side
		p = 0.0
		if P1[0] > A1[0]:
			p += (P1[0] - A1[0])**2
		if P2[0] < A2[0]:
			p += (A2[0] - P2[0])**2
		return p
	
	def theta_cost(th1, th2):
		if theta_prev is None:
			return 0.0
		d = np.array([th1, th2]) - theta_prev
		# wrap to [-pi, pi] for proper angular distance
		d = (d + np.pi) % (2*np.pi) - np.pi
		return float(np.dot(d, d))
	
	# Primary: outward penalty. Secondary: continuity in theta.
	best = min(
		sols,
		key=lambda s: (outward_penalty(s["P1"], s["P2"]), theta_cost(s["theta1"], s["theta2"]))
	)
	return best, sols
```

Once we choose the best P based on our "elbows out" condition, the joint angles that were calculated in `two_link_elbow_candidates` are sent to the simulation

#### Something we should note
The previous program was using **Task Space** rather then **Joint Space** in order to move the end effector from position to position

The `plan_task_move` function runs once when we click. It uses out start E position and end E position to define the physical straight line path from start to finish and it also uses our start angle and our end angle based on our inverse kinematic calculations.

Once the move starts the `update_state` function runs 60 times per second and during this phase it focuses on the task space path:
- It calculates the intermediate commanded point along the straight line between Estart and Egoal using the smooth step function
- It immediately runs the IK on the tiny intermediate point E to find the required angle for just that frame

We use task space when we need a perfect straight line in the air so better for 3D printers and drawing on paper but the math difficulty becomes harder and you risk hitting singularities a lot more. 

**Joint Space** uses the natural swing of the arms or a curved arc and requires much simpler math and is safer from singularities.

A straight line in the real world might force joints to move in a way that is physically impossible. This is why we use the **Jacobian** in order to avoid this but since our goal is to just pick and place and object, there is no need for these calculations.

Using Joint Space is much safer and faster because we aren't forcing a specific path and the robot naturally avoids the mathematical dead zones that occur when trying to force a straight line.

#### Taking our Joint Angles and Translating it to Movement

**The Target is set One Time (IK)**
The moment you click, the robot needs to know where it is going.

- The code calls `select_elbows_outward(E_goal)
- The result is that it solves the Law of Cosines (IK) and gives us two specific numbers which are your theta 1 goal and theta 2 goals
- These are then stored in `self.traj["theta_goal"]`

**Planning the Angular Distance**
Before the robot moves a single millimeter, it calculates how far each motor has to rotate:

![[Pasted image 20260210185410.png]]

In this code, this is `self.traj["dtheta"]` . If Motor 1 is at 90 degrees and needs to be at 120 degrees, its delta is 30 degrees.

**The Move: Interpolation (Frame by Frame)**
This is the core of how the movement actually happens. The simulation runs at 60 frames per second. Every 1/60th of a second, the `update_state` function performs this calculations:

Calculate Progress (tau and s):
The code looks at how much time has passed (t) vs the total time the move should take (T).
- Tau is a value from 0.0 (start) to 1.0 (finish)
- The Smoothstep: The code uses `s = tau * tau * (3.0 - 2.0 * tau)`. This makes the robot accelerate slowly and decelerate slowly, so it doesn't "jerk" at the start or end.

Slide the Angles:
The code updates the robot's current angles by adding a fraction of the total distance:
```python
# self.theta is the angle sent to the drawing/FK logic 
self.theta = self.traj["theta_start"] + s * self.traj["dtheta"]
```

**What is tau and s**
Tau and s are variables that control the **rhythm** and **flow** of the movement. Together, they bridge the gap between clock time and robot position.

Tau is your **progress bar** and s is your **speed filter**

Tau is the normalized time. It represents how far along the robot is in its current journey, expressed as a decimal between 0 and 1.

At the start of the move tau is 0.0 or 0% complete
At the end of the move tau is 1.0 or 100% complete

Inside `update_state` we calculate this by doing:

![[Pasted image 20260210190916.png]]

s is the pacing factor often called Step or S curve. While tau moves forward at a constant robotic pace, s is used to give the robot acceleration and deceleration.

If we set s = tau, the robot would start and stop instantly at full speed and results in linear motion which causes "jerk".

In the code we use this cubic formula called smoothstep:

![[Pasted image 20260210191111.png]]

**How does it all work together**
In every frame, the code performs this hand-off:
1. Cock time (t): "It has been 0.2 seconds since the user clicked"
2. Calculate tau: "If the total trip T is 1.0 second, then tau = 0.2"
3. Calculate s: "Based on the smoothstep formula at 20% of the time (tau = 0.2), the robot should only be at 10.4% (s = 0.104) of its physical distance"
4. Update Joints: The code moves the motor 10.4% of the way to the goal

![[Pasted image 20260210191808.png]]

```python
self.theta = self.traj["theta_start"] + s * self.traj["dtheta"]
```

![[Pasted image 20260210191903.png]]

 **Determining how long it takes to get from current position to end position**
 We define a predefined maximum speed for the motors:
```python
MAX_JOINT_SPEED_DEG = 60.0  # degrees per second
MAX_JOINT_SPEED = np.deg2rad(MAX_JOINT_SPEED_DEG) # approx 1.047 rad/s
```

Then we calculate the total time. We click a target and the code looks at the change in angles for both motors and picks the larger one. It then calculate the time needed to cover that distance without exceeding the speed limit:
![[Pasted image 20260210190313.png]]

We are using a smooth step so the robot starts slow and ends slow. To maintain an average speed that doesn't let the peak speed exceed the limit, the total time has to be stretched by 50%

![[Pasted image 20260210195502.png]]

Max speed = 172.39 mm/s

63% speed = 108.6057 mm/s

Time take from neutral to get to 108.6057 mm/s

