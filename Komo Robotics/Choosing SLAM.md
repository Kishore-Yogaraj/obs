### Difference in Algorithms

#### Slam_Toolbox
slam_toolbox is as cheap as SLAM can get. It matches 10 Hz scans against a 2D pose graph, so on your 11th gen i5 it only ends up using a fraction of one core. We only need these while we build each floor's map. During deliveries using AMCL is still cheaper than the alternative.

#### RTAB_Map 
RTAB_Map has to pull color and depth frames over USB, extract and match visual features, do visual odometry and loop closure, and maintain a richer memory database that grows over time. It can run on an i5 but it competes with the camera workload (door state, fullness check, button detection, people tracking) on the same laptop and the same USB stream. The real strength that comes from RTAB-Map's real strengths are visual relocalization, and handling places where 2D scan matching fails. We don't need this that badly for two reasons
- We always know where we are when we leave the elevator so we never have to re-localize from scratch
- Wheel encoder and the IMU will help us along long featureless hallways

#### Where does RTAB-Map become a better tool
Robots without a good 2D lidar. RTAB-Map is a very mature SLAM option for robots that only have an RGB-D camera. If a camera is our only sensor then this becomes our no brainer choice.

It's also really good for when we need a 3D map and not a 2D map. It builds 3D point clouds and 3D occupancy grids. This matters for:
- Drones, which need 3D free space
- Legged robots and rough terrain where a single 2D slice can't describe stairs
- Mobile manipulators that need to know what's on table and shelves

Also really good for global relocalization. Its loop closure works on visual appearance so it can recognize a place it has seen before without a pose. This helps when:
- Robot boots up or loses power somewhere random
- Someone pushes the robot
- Hallways are geometrically repetitive but look different

AMCL needs a reasonable starting guess. RTAB-Map can often recover without one.

#### Where RTAB-Map becomes the wrong tool
Flat indoor floors with a decent 2D lidar and a navigation only goal
- Slam_Toolbox is just very reliable
- Featureless or visually difficult spaces like white walls, glass, big lighting changes
- Tighter compute budget

#### Rough Compute Estimates

![[Pasted image 20261116215023.png]]

Our biggest gap comes from running visual odometry running on every frame. However, if we ran RTAB-Map with wheel and IMU odometry instead of visual odometry, the cost ends up dropping a lot. The camera can then only be used for loop closure about once a second but then we just end up paying for localization we don't need.