is the biggest reason why the odom to base_link frame doesn't update it's position after map matching because we don't want it to seem like the robot jumped a certain value despite the wheels not moving

es—that is the exact reason. In ROS (formalized in **REP 105**), the core contract of the `odom -> base_link` transform is **strict continuity**: it is allowed to drift slowly over time, but it must never jump.

## What Breaks If `odom` Jumps

- **Control loop instability:** Local motion controllers (like PID, DWB, or MPPI) calculate motor commands from short-term position and velocity errors. If a map-matching update suddenly shifts the robot's pose by 15 cm, the controller sees an instantaneous error spike and reacts violently—jerking the wheels, oscillating, or overshooting the path.
    
- **Corrupted velocity estimates:** Any node differentiating pose over time ($\frac{\Delta x}{\Delta t}$) will see a massive, physically impossible velocity spike during the jump.
    
- **Smeared local costmaps:** Obstacle avoidance layers accumulate recent LiDAR or depth-camera readings in the `odom` frame. A sudden jump shifts all recently tracked obstacles relative to the robot, making phantom obstacles appear or real ones vanish.
    

## How the `map -> odom` Split Solves It

To give you both smooth control and global accuracy, ROS splits the problem across two transforms:

- **`odom -> base_link` (Smooth, local, drifts):** Driven purely by continuous sensors (wheel encoders, IMU, VIO). Local controllers and obstacle avoidance operate here so they never get startled by a sudden correction.
    
- **`map -> odom` (Accurate, global, jumps):** Driven by map matching (AMCL, SLAM). Whenever drift is detected, the entire discrete correction is absorbed here—effectively sliding the `odom` frame's origin across the map rather than teleporting the robot inside `odom`.
    

This way, the global planner always knows where the robot actually is in the world (`map -> base_link`), while the wheels and local controller always see smooth, physically consistent motion.