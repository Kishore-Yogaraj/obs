Sensors that I initially thought were necessary:
- LiDAR
- RGB-D Camera
- IMU
- Side wide angle cameras
- Wrist RGB camera
- Low mounted 2D LiDAR

Options for Lidars
- Livox Mid-360s
	- $960.48
- RoboSense Helios H32F70
	- $2546.19
- Ouster OS0 Rev 8
	- $3332.31

Why we went with the Mid-360s:
- It provides full horizontal coverage, has a small mechanical footprint and an integrated IMU at the lowest price in the researched shortlist. The range is already well beyond the distances needed for most hallway and elevator approaches.

Limitation with the Mid-360s:
- The Mid-360s looks only 7 degrees below horizontal in the recorded orientation. A roof mount can therefore miss nearby low obstacles. A front RGB-D camera may cover part of this gap, but does not establish complete side or rear coverage. 2D LiDAR mentioned in the notes remains an open design decision

Why the alternatives weren't selected:
RoboSense Helios:
- Longer range and higher point rate offer capability but the recorded cost, mass and power are substantially higher. We will not be performing an indoor task that requires its 110 m range

Outer OS0:
- Its wider downward field of view and denser sampling could improve the near field coverage. However, its price is difficult to justify
We are using the LiDAR for the following:

Unitree LiDAR Comparison

| Specification              | Unitree L1  | Unitree L2  | Livox Mid-360s      |
| -------------------------- | ----------- | ----------- | ------------------- |
| Horizontal FOV             | 360 degrees | 360 degrees | 360 degrees         |
| Vertical FOV               | 90 degree   | 90 degree   | 59 (-7 to +52)      |
| Effective point rate       | 21600 pts/s | 64000 pts/s | 200000 pts/s        |
| 360 degree scan frequency  | 11 Hz       | 5.55 Hz     | 10 Hz               |
| Minimum detection distance | 0.05 m      | 0.05 m      | 0.10 m              |
| Built in IMU               | Yes         | Yes         | Yes                 |
| Power                      | 6 W         | 10 W        | 6.5 W               |
| Size                       | 75×75×65 mm | 75×75×65 mm | 65×65×60 mm         |
| Weight                     | 230 g       | 230 g       | 265 g               |
| ROS 2 Support              | Foxy        | Foxy        | Foxy, Humble, Jazzy |
| Price (CAD)                | $354.08     | $595.80     | $963.43             |

We need the LiDAR to handle the following:
- 3D SLAM
- Localization
- Hallway navigation
- Obstacle detection
- People detection/tracking support
- Doors and elevator door geometry 
- Some perception redundancy

21 600 sampling frequency is extremely undesirable for this which is why the L1 doesn't make sense

The Livox has 3.1 times the point rate as the L2 which is extremely useful for providing more geometric measurements. Especially good for:
- Long corridors
- Large open lobbies
- Elevators
- Repetitive hallways
- Smooth walls

All common in buildings we plan to operate in

We are also dealing with:
- Walking people 
- Rolling cars
- Other moving obstacles

This makes 10Hz more favorable than 5.55 Hz

ROS2 integration:
- Unitree's ROS 2 environment is limited to
	- Ubuntu 20.04
	- ROS 2 Foxy
	- PCL 1.10
- Livox ROS driver supports
	- ROS 2 Foxy
	- ROS 2 Humble
	- ROS 2 Jazzy
	- Ubuntu 24.04

Better to spend time on:
- Autonomy
- Localization
- Elevator handling
- Perception
- Manipulation

rather than patching an old sensor drivers

RGB-D Camera
The Gemini 336 is our preferred front camera because it targets the useful nearby depth range while keep cost and host processing demands lower than some of the alternatives. It also provides 1920 x 1080 RGB images for recognition tasks. The camera is selected to compliment the Lidar rather than replacing it

Options for cameras:
- Gemini 336
	- $471.44
- Gemini 336L
	- $629.23
- RealSense D455
	- $695.24
- Zed 2i
	- $1000+

Why the alternatives weren't selected:
Gemini 336L:
- Costs $157 more. Has a longer baseline, better depth precision, longer ideal range and global RGB shutter but the initial design prioritizes nearby perception and price. We can reconsider if motion distortion or depth quality proves limiting
RealSense D455:
- Costs $223.80 more than the 336. It offers global RGB shutter and a longer baseline but its recorded near depth limit is less suitable for close approaches and its depth field of view is narrower
ZED 2i:
- Offers a wider field of view and higher image resolution, but costs $527.56 more before the optional polarizer. Hose GPU depth processing adds compute requirements that the selected camera avoids

Why RGB-D over RGB:

Recognition with distance:
RGB supports recognition of people, elevator doors, panels and buttons. Registered depth can provide a 3D position for a detected image region which reduces the need to obtain every visual object's range through LiDAR-camera association. Invalid depth, occlusions and calibration errors must still be handled

### Presentation for Meeting with Melek
- What is the goal of the robot
- What does the robot look like
- What does the robot need to be able to do 
- What sensors did you initially think you needed and why
- Which sensors did you end up discarding and why 
- What is the goal of the Lidar
- What is the goal of the camera
- Is this sensor stack feasible?
	- Do we need more sensors 
	- Do we need less sensors?