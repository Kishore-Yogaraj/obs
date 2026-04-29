### Intro
- This document will cover the general flow of how robots work
- We will be covering the following terms (Terms will be added as we learn about them)
	- Localization
	- Mapping
	- SLAM
	- Sensors
	- Perception
	- Transforms
	- Path planning
- The basis of the project I'm working on at TUHH is navigation from point A to point B
	- This is the basis of most robotics because this is what we want for the WATO car as well
	- **How do we get a robot to go from point A to point B?**
- This document will talk about how this is typically done in robotics and then we'll go over how to implement this with our specific use case which is navigation with the use of NeRF
- Each section will talk about a specific function of the robot and I'll attempt to document in the order that it occurs in the robot 

### Robot Build
#### Chassis
- The robot first needs a a chassis in order for all the components to sit in
- This doesn't have to be too complex a box with an opening in the middle will do most of the time
- A constraint for the chassis is that it has to be able to house all the other hardware components
- Robot chassis can very from robot to robot because they have different functionalities such as humanoid robots, robot arms, bipedal robots, etc. 
- The focus for you is mobile robotics which is probably the easiest of the 4 just because you aren't working with 3 different axes and the need for transforms isn't as large either 

#### Sensors
- Sensors are what make up the "eyes" or **perception** of the robot
- As humans we have eyes and ears to observe our surroundings and develop and understanding of what's around us 
- For robots we use sensors to simulate this
- Sensors are critical for understanding the environment, detecting obstacles, and determining the robot's position (localization)
- The list of sensors is very long
	- There are so many different sensors for specific applications however, there are industry standard sensors as well which are what will be discussed in this document
	- These are sensors you can typically find on most robots
- Common sensors can be grouped into 3 different kinds of sensors which all have a primary use but can also be used for cases of other groups as well
	- For example: You can localize with localization specific sensors like IMUs but also with a range sensor like a LiDAR
- **Vision Sensors**
	- **RGB Camera:** Captures color images for object detection, obstacle recognition and potential SLAM (NeRF navigation)
	- **RGB-D Camera**: Combines RGB data with dept information to perceive the environment in 3D
- **Range Sensors**
	- **Lidar (Light Detection and Ranging)**: Measures distances by emitting laser beams which provide 2D or 3D scans for operations like obstacle detection and SLAM 
		- Lidars operate at a very high frequency and PPS (points per second) which make it good for real time operation
		- Most lidars operate in the 5 - 30 Hz (number of times it rotates a full 260 degrees in one second) and they operate in the 10000 - 2000000 PPS range (The number of distance measurements in can capture per second)
			- A higher PPS means you'll get a more dense and detailed point cloud which means you capture more information
		- A lidar works by sending out a laser pulse and measuring the time it takes for the laser to return after reflecting off an object and uses this measurement to estimate the distance of the object
	- **Ultrasonic Sensor**
		- Low-cost sensors for detecting nearby obstacles in 2D (typically used in smaller robots)
		- These sensors do the same thing lidars do just not nearly as well
	- **Radar (Radio Detection and Ranging)**
		- Effective in outdoor or low-visibility conditions to detect objects and measure distances
		- Radars work similar to Lidars in the sense that they send out waves (radio waves) and and measure the time it takes for it to return back to the sensor 
		- Radars can also measure velocity 
			- If the object is moving the frequency of the reflected signal will change 
			- The radar can measure this frequency shift to calculate the object's relative velocity
			- You can think of the frequency as waves (actually look like waves) and it refers to the number of oscillations of these waves per second (Top of the wave to the next top of the wave)
			- When an object moves toward the radar the waves compress which changes the frequency and when the object moves away from the radar the waves elongate which changes frequency
	- **Localization Sensors**
		- **IMU (Inertial Measurement Unit)**: Tracks orientation, acceleration, and angular velocity to estimate pose
		- **Wheel encoders (odometry)**: Measure wheel rotations to estimate the robot's movement
		- **GPS**: Provides absolute global positioning for navigation in outdoor environments
			- GPS is great for creating a global planner and creating a path from point A to point B in a real world
				- For example, going from one city to another city
			- However, other sensors are still critical to develop a map and localize within the current environment the robot is in so it knows what obstacles are in the way 
			- The local planner can constantly change the global planner route as obstacles appear 
#### What is Odometry
- Odometry is a method of estimating the robot's position (x,y, theta) over time, based on motion
- For a **differential drive robot** odometry calculates position and orientation using
	- Wheel rotations (encoders) to estimate the distance traveled
	- Robot geometry (distance between wheels) to determine turning angles 
- Odometry is essential in our project for knowing where the robot is in the 3D map and assisting the path planning algorithm by providing accurate positional feedback 
- In order to perform odometry the robot needs **wheel encoders** and **IMU**
	- **Encoders** detect the direction of motion and the distance traveled
	- **IMUs** measure angular velocity, linear acceleration and orientation (optional)
		- IMUs are great to complement odometry because they work even if the robot skids or is bumped
#### Compute Platform 
- With all the information the robot perceives with all of its sensors, it needs instructions on what to do with this information
- Just like how humans are able to process the information we perceive with our brain, robots need to be able to do the same 
- The brain of the robot is the **compute platform**
- With the compute platform, the robot is able to **process sensor data, run algorithms, and execute motion control**
- **Processors**
	- **CPU**: General purpose processing which is good for path planning and control (low complexity tasks)
	- **GPU**: Required for high performance tasks like deep learning based perception and SLAM
- **Storage**:
	- SSDs are needed to store maps, algorithms and logs
- **Microcontrollers**
	- Low power real-time processing for motion control and sensor interfacing
		- Arduino, STM32, ESP32
- **Embedded Computers**
	- Single board computer for moderate processing needs
		- Raspberry Pi, Nvidia Jetsons
	- Embedded computers will be more common for your tasks because most of them will require an onboard GPU of some sort
	- This is basically your computer onboard the robot instead of having your whole laptop sitting on top of your laptop so that it can execute algorithms and motion control

#### Actuators
- Actuators (motors) are the wheels of your robots (if working with 4 wheel mobile robotics) and what allows the robot to physically interact with the environment
- **DC Motor**:
	- DC motors are the most simple motors to use in terms of functionality
	- You communicate with them using Pulse Width Modulation (PWM)
	- With DC motors you can control the direction at which they rotate and the speed at which they rotate
- **Stepper Motor**:
	- Stepper motors have a bit better functionality than DC motors
	- With a stepper motor you can precisely control how the motor moves by specifying the number of "steps" the motor should rotate
		- You can't control the angle but you can control how much it should rotate based on the steps set up for the motor itself
		- The more steps it has the more accurate you can be
		- For example if it has 200 steps, you can set the motor to be in 200 different positions
	- You can also control speed with stepper motors
	- Open loop system 
- **Servo Motor**:
	- Servo motors are probably the best out of the three because you can control position, speed and torque with this motor
	- Can control the specific angle the motor should rotate to
	- Torque control refers to the motors ability to rotate with a specific force 
		- This is good for things like grip force for robotics
		- Allows for controls of joints for smooth and safe movement 
	- Close loop feedback system
- **Motor Controller**:
	- A motor controller interfaces between the compute platform and motors which allow for speed and directional control 
	- Motor controllers are a lot easier to work with because the control logic is already written for them which means with whatever embedded computer you're using, you just have to write high level code to program them such as "turn motor right 90 degrees"
- **Motor Driver**:
	- A motor driver amplifies the low power control signals sent from your embedded computer 
	- For a motor driver you have to write the code for the control logic such as the speed and position control 
	- You will be writing low level code with a motor driver because you have to write the code that turn the electric signal to a value the motor can understand
- **Wheels or Tracks**
	- **Differential Drive**: Used for 2 wheeled robots can has a separate control of left and right wheels for turning
		- In order to turn you would need to rotate one wheel back and one wheel forward 
		- Or you turn the wheels at different speeds
	- **Ackerman Steering**: Used for car like robots with a steering mechanism for smooth turns
		- The two front wheels turn at different angles (physically turning wheel not rotation angle of the wheel) which ensures that each wheel traces an arc around the same center of rotation 
		- This is achieved by mechanically linking the wheels so that the inner wheel (closer to the center of the turn) turns at a sharper angle than the outer wheel
		- This ensures smooth and efficient turns 
##### Open Loop System
- In an open loop system the motor operates purely on the input signal that was sent and doesn't perform any corrective behaviors to see if the desired rotation angle (position), speed or torque was met 
##### Closed Loop System
- In a closed loop system, there are sensors in the motor that provide feedback on the previous output
- If the desired output (torque, position, speed) wasn't met in the first output from the input signal then the motor will make corrections to the input signal to get the desired output in the next iteration

#### Power Supply
- In order for the robot to work it needs to generate power from somewhere which is why we have a power supply which is typically an onboard battery
- It provides power to the motors, the compute, and the sensors

#### Communication Systems
- Each component also needs to be connected in order for it to communicate with the brain 
- Same way humans are connected via their nervous systems, robots will use wires to connect their sensors and motors to the brain of the robot which is the compute
- These wired connections also send different types of signal depending on the type of component you're working with 
- These signals include:
	- USB
	- I2C
	- SPI
	- CAN
- Think about how the brain sends different signals to different body parts 
	- You can't send the same signal you would send to your hand as you would your leg
	- The same applies for robots where the brain communicates to different sensors with different communication protocols as the ones listed above

### Robot Navigation 
- The goal for the project I'm working on is to have a robot navigate from point A to point B while avoiding obstacles 
	- However, what makes this project especially challenging is the method we use to go about doing this which is with NeRF
	- In order to understand how to do this with NeRF, you need to understand the general solution for when you want to achieve autonomous navigation with obstacle avoidance for a robot

#### SLAM 
- SLAM is an acronym that stands for Simultaneous Localization and Mapping
- It's a took used in for exactly what the name suggests, using the Lidar, it simultaneously localizes itself and maps the environment and keeps in memory what the map looks like and where it is relative to the map 
- We've discussed some important terms here which are **localization** and **mapping**

##### Localization
- Localization is the ability of the robot to know where it is relative to the map it created 
- As human, we are smart enough to know where we are relative to an environment that we're in 
	- We have a general understanding of what's around us in a room and even though we may not be able to see what's behind us in a room, we still have a rough idea of what it looks like because we have a memory
	- We're also able to tell how far or close something is from you and we have an understanding about the amount of space something takes up 
	- This is us **localizing** ourselves in an environment. We are able to identify our position relative to the rest of the map (room)

##### Localization Technique - Kalman Filter Localization
- As the robot moves somewhere new, we need to keep track of its position 
- We can make a prediction about where the robot is using **odometry** which is measuring the movement of the wheels and using IMU data to measure distance traveled (or velocity) which is provided by the wheel encoders and orientation which is provided by the IMU
- Then we can use the lidar scan using the robot's new position along with prior knowledge of the map, we can get an actual observation of where the robot is
- We can then compare the odometry with the lidar to get an accurate estimate of the robot's actual location 
- **Key problem:** You can't simply use odometry data to localize because wheels often slip and cause drift which can result in inaccurate measurements of where the robot is in the map 
	- Otherwise you could simply map every single boundary edge to a distance measurement and add a condition for where if the robot approaches this distance measurement to avoid it (this is also time consuming and inefficient)
- **Solution and another problem:** To fix this we can use another measurement sensor such a distance sensor to see how far away we are from a wall
	- The problem we get here is that the distance sensor and the wheel encoders may have different measurements
	- So we use the **Kalman filter** to fix this
- **How Kalman Filter Localization Works**
	- The Kalman filter takes two different inputs and computes a weighted average to get a final estimate of the position of the robot
	- **Predict:** First we use odometry data to predict where the robot is
		- So we use our odometry measurements and add it to the previous pose of the robot to get a prediction of where the robot currently is
	- **Correct (Update step):** Then we take the outputs from the distance sensor and calculate the **Innovation** which is how far off the estimated position (odometry) is from the output of the distance sensor
		- The **Kalman gain** formally is a value that weighs the influence of the sensor measurement relative to the system's prediction during the update step of the the Kalman Filter
		- It basically determines how much trust to put on the sensor measurement
		- The more trust you have, the less uncertain you are and the more weight you give to it
		- **Update step:** This is the phase where the Kalman filter takes in new sensor measurements and updates the new final estimated position of the robot
			- This step and the fuse step kind of go hand in hand
			- This step corrects the predicted state based on the difference (error) between the predicted measurement and the actual measurement, using the Kalman gain to balance trust in the prediction versus the measurement
	- **Fuse:** After we take these outputs we can then fuse both outputs together to get a final better estimate about where the robot actually is in the map
	- **Tuning**: We can tune different covariances based on how confident we feel about different measurements such as
		- Starting Covariance (confidence in the starting position measurement)
		- State noise covariance (confidence in the odometry measurements)
		- Sensor covariance (confidence in the measurement of the sensor)

##### Mapping
- Mapping is the ability of the robot to remember what it's environment looks like
- It simply is as the name suggest, the robot is creating a map of its environment so it knows where it is
- I like to think of this is how pirates would create maps to tressure chests and on the map you would see different land marks like trees of monsters
	- This is an example of how they localize themselves with the map
	- If you look at the map and see a tree around you and there's a tree half way into the map, you know you're halfway to the tressure chest
	- A robot works the same way

- Continuing from the point above, a robot will use localization and mapping together in a sort of feedback loop 
- As it localizes it improves mapping and as it maps it improves localization 
	- How this works is that the robot can first create a general map of the room with all the obstacles that are present and keep it in memory 
	- This is done with the control of a human at first because the robot doesn't know where how to traverse the room yet
	- After it has a general idea, the more you drive the robot around the more detailed the map will become 
	- At first it develops a general map, then it begins to capture more features on the map like how round a surface may be or how sharp the corner is
	- As it develops a more detailed map, it also better localizes because now it has more features to work with 
	- This means when you place it in a certain area for the first time after SLAM has completed, it has a higher chance at recognizing where it is without confusing it for a different part of the map because each part of the map now has more details
	- As a robot continues to localize itself and identify where it is relative to the map, it adds the sensor data to the map to reduce errors in the map 
	- **Mapping and localization go hand in hand**
- SLAM is mostly done with the help of lidars and can use sensor fusion to create even more detailed maps 
- NICE-SLAM is another slam method that uses cameras instead of lidar to create a map and localize it self
	- It creates a 3D representation of the entire environment and then uses the camera to feature match in order to localize itself 
	- It localizes itself more like a human in the sense that it looks at its surroundings and then looks back in its memory to see what part of the room that it mapped earlier looks like the image its currently capturing 

##### Pose Estimation 
- After mapping, the robot is able to better estimate it's pose which is its position and orientation on the map 
- The robot needs a way to specifically identify where it is on the map and so it uses poses which are coordinates 
- The origin is typically where the robot began mapping
- The pose will be outputted as a 3D vector with (x, y, z) and the orientation which are the (**roll** **pitch** and **yaw**)
	- Roll is the rotation around the x axis if the front of the robot is facing the X direction 
	- Pitch is the rotation around the y axis as it tilts its nose up and down
	- Yaw is the rotation around the z-axis
- During SLAM the robot will assign coordinates to all the parts of the map so that it knows where it is relative to the map
- If you put the robot down in the room, it could tell you what pose its at after SLAM has run 
- Pose is how the Robot will be able to continuously navigate around a room 


#### Path Planning
- Now that the robot has a map of its surroundings and knows how to identify where it is on the map we need to give the robot a way to find the path to the goal pose without running into obstacles
- Finding this optimal path is **path planning**
- Path planning is done with algorithms that have already been created before which include
	- A*
	- Dijkstra
	- Rapidly Exploring Random Trees (RRT)
	- Probabilistic Roadmaps
- They are all used to find a collision free path to the goal point 
- The general idea of all these algorithms is that it will sample the map for potential paths, evaluate whether each sampled path intersects with obstacles, and then selects the shortest or most efficient collision-free path 
- There are two types of plans created when the algorithm is run
	- **Global Planner** 
		- The global planner finds a long term path to the goal position using the algorithm 
	- **Local Planner**
		- The local planner will then follow the path and adjust the path in real time while using the algorithm when it detects obstacles in its way 
		- The local planner will then update the global planner and tell the robot to follow a different path instead to avoid the obstacle
		- This is useful in dynamic environments where objects are constantly moving and the path constantly needs to be changed
		- As obstacles enter the robot's field of view, the SLAM generated map will identify it as it obstacle and as occupied space and then generate a new path by querying the algorithm again 
- **A big question I had was how do you tell the path planning algorithm what is considered occupied space and what is considered free space**
	- The SLAM generated map can be shown in multiple different forms:
		- Occupancy grid: A 2D grid where each cell indicates free space, occupied space, or unknown space
		- 3D voxel Map: A 3D equivalent of the occupancy grid, representing obstacles and free space in three dimensions
		- Implicit Neural Map: In systems like Nice-SLAM, a neural representation encodes the scene geometry, which can be queried to determine free space
	- In a 2D occupancy grid for example each grid will be represented with a number 0, 1 or -1 for free space, occupied space or unknown space 
		- This will basically be an array
	- The algorithm will then run on this array after we set parameters like what the initial position is and what the final position should be and the algorithm will then find its way to that path by avoiding all the 1s and going through the 0s
	- The robot will also be equipped with a mapping function that maps the SLAM map to the global coordinate system (x,y,z,roll,pitch,yaw)
	- Each 0 in the array is considered a waypoint which is set by the path planning algorithm 
	- When the robot wants to navigate to the next waypoint it will actually perform the mapping function to map that waypoint to the pose of that waypoint so that the robot can understand where it's supposed to go to 
	- **The robot doesn't understand what the 0 and 1s mean but it does understand how to get from one pose to another pose** - HOW?
	- After performing some calculations to determine the angle it should be at to go to the way point and the distance of the way point, it can then traverse to the next way point
	- The overall flow of path planning:
		- Convert SLAM map into a grid 
		- Mark the grid with 0, 1 and -1 on areas of free space, occupied space and unknown space
		- Represent the grid as an array
		- Run the path planning algorithm on the grid
		- Each point on the grid is now considered a way point
		- The robot will map its current way point to a pose and the next goal way point to a pose
		- It will perform calculations like Euclidean distance and find the heading angle to determine how far and what angle it should face to travel to the next way point
		- The robot will continue to do this for all way points until it reaches the end goal pose

#### MPC
- MPC stands for model predictive control which is a control algorithm that optimizes the robot's motion over a **finite time horizon** 
	- Finite time horizon refers to the limited time window over which the controller predicts and optimizes the future behavior of the system 
	- It's basically the window of time in which the MPC predicts and optimizes movement 
	- **A quick example:**
		- If a robot is navigating from point A to point B and we give it a time horizon of 2 seconds, the MPC will be responsible for predicting how the robot will move along the path over the next 2 seconds (despite whether it reaches the goal pose or not)
		- The MPC will optimize the trajectory to minimize the cost (distance to the target) while ensuring it doesn't hit obstacles
		- This control input is only applied for incremented time steps so lets say every 0.1 seconds
		- This means the MPC will control the robot for the first 0.1 seconds and then re-evaluate the situation after considering the updated position
##### MPC inputs
- After planning a path with A*, the following inputs are needed
- **Reference Path**
	- The output of A* is typically a series of waypoint (x,y) that define the desired trajectory
	- MPC uses this path as the **reference trajectory**
- **Robot State**
	- Current pose (x, y), orientation (theta), and velocity v
	- These are obtained from odometry, GPS, or other sensors
	- SLAM and localization filter will give us this information
- **Dynamic Model of the robot**
	- A mathematical model describing how the robot moves, given its control inputs (steering angle and acceleration)
- **Constraints for the robot**
	- Physical limits like the maximum velocity, acceleration, and steering angle
- **Cost Function**
	- A function that penalizes deviations from the path, excessive control effort or violating constraints
##### MPC Process
- **The robot will begin at an initial position with a series of inputs such as the acceleration, initial position, orientation, optimization function, dynamic model of the robot and velocity**
	- The control inputs **acceleration and steering angle** if the robot is starting from rest will be 0 as well unless stated otherwise
- **Based on the current state and initial control inputs, the MPC will simulate how the robot will move step by step, calculating a sequence of predicted states**
	- MPC will use the reference path and the robot's dynamic model to predict future states over the **selected time horizon**
		- **Time horizon:** The time horizon of N steps will predict the robot's movement for a total time of ![[Pasted image 20250120190426.png#|50]]
		- This means that if you set N = 10 and delta t = 0.1, the robot will make predictions for 1 seconds of travel and you will have a sequence of 10 predicted states in that 1 seconds
		- This means that it will create a sequence of predicted states for every 0.1 seconds
		- For each step, the predicted state will depend on the robot's dynamic model and the control inputs applied at that step
		- Interpolation of the reference path allows us to calculate errors and deviations from the predicted state and the reference state
- Before the first move the MPC will solve an optimization problem that adjust the control inputs to **minimize the error between the predicted states and the reference path**, **minimize the control effort** and **satisfy constraints**
	- Optimization function (cost function)
	- **Predicted States:** These are the states that are predicted by the MPC based on the inputs
	- **Reference States:** A series of waypoints that are determined by the path planning algorithm
	- **Effort of control inputs:** Basically controls how much the control inputs are allowed to make changes to the MPC. You adjust
		- **Tracking Error:** Minimize deviation from the reference path
		- **Control Effort:** Penalize large control inputs
		- **Rate of change:** Penalize rapid change in control inputs
	- The output of solving this cost function is a sequence of control outputs (acceleration and steering angles)
![[Pasted image 20250120185324.png]]
- The first control input will be sent to the robot 
	- The robot will execute this command and move closer to the path
- Replanning during motion
	- After a predefined time step (0.1 seconds), the robot updates its current state (**position, orientation, velocity**) using feedback from sensors like encoders or IMUs
	- Then MPC will start the entire process again
		- Takes the updated state as the new starting point along with the steering and acceleration
		- Predicts a new sequence of future states
		- Optimizes the control inputs based on the updated information
- Process continues until the robot reaches the goal pose

#### Cost Map
- If we assign each cell on the map a "cost" for how close it is to the wall we can then prioritize using cells with a lower cost to build our path
- With the cost map the robot will avoid collision with any walls
