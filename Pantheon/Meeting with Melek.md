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
- 