### Difference in Algorithms

#### Slam_Toolbox
slam_toolbox is as cheap as SLAM can get. It matches 10 Hz scans against a 2D pose graph, so on your 11th gen i5 it only ends up using a fraction of one core. We only need these while we build each floor's map. During deliveries using AMCL is still cheaper than the alternative.

#### RTAB_Map 
RTAB_Map has to pull color and depth frames over USB, extract and match visual features, do visual odometry and loop closure, and maintain a richer memory database that grows over time. It can run on an i5 but it competes with the camera workload (door state, fullness check, button detection, people tracking) on the same laptop and the same USB stream. The real strength that comes from RTAB-Map's real strengths are visual relocalization, and handling places where 2D scan matching fails. We don't need this that badly for two reasons
- We always know where we are when we leave the elevator so we never have to re-localize from scratch
- Wheel encoder and the IMU will help us along long featureless hallways

#### Where does RTAB-Map become a better tool
Robots without a good 2D lidar. RTAB-Map is a very mature SLAM option for robots that only have an RGB camera