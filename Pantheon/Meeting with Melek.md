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

The Livox has 3.1 times the point rate as the L2 which is extremely 