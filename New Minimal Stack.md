### **Proposed Minimal Software Stack**

I'll break this down into major system modules. Each module includes:
- **What it does**
- **What tools to use**
- **What sensors it relies on**
---

#### **1. Sensor Drivers and Data Acquisition**
**Goal**: Acquire data from cameras, LiDAR, and GPS in real time and publish to ROS2 topics.
- **Tools**: ROS2 (Humble), custom drivers or existing packages
- **Sensor integration**:
    - **Cameras**: Use camera drivers like `image_transport` with compression
    - **LiDAR**: Use `velodyne_driver`, `ouster_ros`, or `rslidar_sdk` depending on your model
    - **GPS**: Use `nmea_navsat_driver` or similar to publish `sensor_msgs/NavSatFix`

---

#### **2. Localization**
**Goal**: Know where the car is on the test track (pose: x, y, θ).
- **Approach**:
    - Start with **GPS-only localization** for simplicity, especially if the test track is outdoors.
    - Optional: Use **LiDAR-based SLAM (e.g., Cartographer, GMapping)** to build a simple local map if GPS drift is too large.
        
- **Tools**:
    - **`robot_localization`** package for fusing GPS, IMU, and wheel odometry into a smooth `nav_msgs/Odometry`
    - TF2 to manage coordinate frames (`map` → `odom` → `base_link`)

### Diving deeper into localization
The goal here is use GPS to localize where the car is relative to the world. We want to convert noisy and low-rate GPS data into a smooth, reliable pose estimate (position and orientation) of the vehicle in a local reference frame (/map frame or /odom frame)

#### 1. Hardware assumptions
- GPS provides **latitude, longitude, altitude**
- Optionally, you also have:
    - **IMU** (acceleration + orientation)
    - **Wheel odometry** (from encoder data)
	    - This can be used to determine how far a robot has traveled
    - Time-synchronized sensors (important for fusion)

#### 2. Convert GPS to a Usable Coordinate Frame
ROS uses Cartesian (x, y, z) positions, but GPS gives you (lat, lon, alt). To localize on a map:
#### ✅ Convert GPS → UTM (Universal Transverse Mercator)
- UTM basically projects lat/lon onto a flat grid system, which is easier for local navigation
- UTM converts geographic coordinates into meters in a local planar frame.
- You can use `robot_localization` or `geodesy` packages to do this.
- The **robot_localization** package is what we're going to most likely use
	- This comes with a comprehensive sensor fusion package that can estimate a robot pose by combining multiple sensors
	- It fuses data from multiple sensors (GPS, IMU, wheel odometry)
	- Uses Extended Kalman Filters or Unscented Kalman Filters
	- Handles the coordinate frame transformation
	- Provides filtered smooth position estimates
	- Converts GPS to local coordinate frame
- Using GPS just means we are localizing against the Earth itself
	- We know our position as (x,y,z) coordinates in meters relative to a global origin
	- Waypoints, paths, and obstacles are defined in these same global coordinates
	- No pre-built map required, we are localizing against the Earth itself
	- We are basically determining our absolute position on Earth which will then allow us to navigate to GPS-defined goals, follow GPS-defined paths or avoid GPS defined obstacles
- **How do we test this?**
- **Is this a good approach?**

**What is odom frame**
- The `odom` frame is used for real-time planning, control, and localization because it's smooth and continuous — but its position is corrected over time using GPS (via the `map → odom` transform).
- The **`odom` frame is a _local_ reference frame** that behaves _like a global map frame_ over short distances — but **it's allowed to drift**, and **we adjust its position relative to the true global `map` frame** using GPS (or other global sensors).

#### Coordinate Frame Set Up
## 🗺️ Key Coordinate Frame Relationships

### 1. `map → odom`

- Corrects the `odom` frame using absolute sensors (e.g., GPS).
- Handles **global drift correction**.
- Example: If GPS says your car jumped 1m east due to a correction, this transformation reflects that.

### 2. `odom → base_link`

- Estimated using **IMU**, **wheel encoders**, and **dead reckoning**.
- This is the car’s **local position over time**, assuming no global corrections.

### 3. `base_link → sensor frames`
- These are static transforms that define where each sensor sits on the car.
- Used so the LiDAR/camera data can be interpreted in the context of the car.

---
## 🤖 Visualization Example

Imagine your car starts driving forward from a starting line.
- `map`: Always fixed to the ground; doesn't move.
- `odom`: Moves slowly relative to map due to small drift.
- `base_link`: Moves relative to `odom` as the car drives.
- Your camera (in `camera_link`) might be 1.5 meters in front and 0.5 meters above `base_link`.
---

#### **3. Perception (Optional, for basic obstacle detection)**
**Goal**: Detect large static obstacles on the test track to avoid collisions
- **Simplified approach**:
    - Use **LiDAR** point cloud to detect nearby obstacles
    - Optionally use **YOLOv8** with the cameras for obstacle classification (humans, cones, etc.)

- **Tools**:
    - **Point cloud processing** with `pcl_ros` or `pointcloud_to_laserscan`
    - **Vision models** via `ros2-yolov8` wrapper for real-time detection

---
#### **4. Mapping and Path Planning**
**Goal**: Plan a collision-free path from the current pose to the goal pose on the track.
- **Map**:
    - For minimal setup: use **static costmap** (manually define track boundaries)
    - If using SLAM: build a simple **2D occupancy grid** of the track
- **Planner**:
    - Use `nav2`'s **`NavFnPlanner`** or **`SmacPlanner`** to compute global path
    - Use `DWB` or `RegulatedPurePursuitController` for local control

- **Inputs**: `nav_msgs/Odometry`, `sensor_msgs/LaserScan`, and `geometry_msgs/PoseStamped` (goal)


### Mapping and Path Planning
### **Option 1: Waypoint-Based Path (Simplest)**
- Manually drive the track once and record GPS waypoints
- Create a path as a sequence of (lat, lon) or (UTM x, y) points
- Path planning becomes following this predefined route
- No complex algorithms needed - just pure pursuit or similar path following

### **Option 2: Cost Map Approach**
- Create a local 2D occupancy grid around your current position
- Use LiDAR to populate obstacles in this grid
- The "free space" becomes your navigable area
- Run A* or similar algorithms within this local grid
- Update the grid as you move

### **Option 3: Hybrid Approach (Recommended)**
- **Global level**: Rough waypoint path from start to goal
- **Local level**: Real-time obstacle avoidance using LiDAR data
- Combine global GPS navigation with local reactive planning

### Expanding on Option 3
## **Global Planner**
- **Input**: Start position (current GPS) and goal position (target GPS)
- **Output**: High-level path in global coordinates (sequence of waypoints)
- **Planning space**: Global coordinate frame (UTM/lat-lon)
- **Updates**: Infrequently (only when goal changes or major replanning needed)
- **Algorithms**: Simple line, A* on coarse grid, or pre-recorded waypoints
## **Local Planner**
- **Input**: Current segment of global path + real-time LiDAR data
- **Output**: Immediate steering/velocity commands
- **Planning space**: Local coordinate frame around robot (typically 2D occupancy grid)
- **Updates**: Continuously (10-50 Hz)
- **Algorithms**: Dynamic Window Approach (DWA), Timed Elastic Band (TEB), potential fields

### **Local Planner**
- **Input**: Current robot state, local sensor data, segment of global path
- **Output**: Immediate velocity/steering commands or short trajectory
- **Updates**: Continuously (10-50 Hz)
- **Continuous space**: Works with actual robot dynamics

## **Common Local Planning Algorithms**
### **1. Dynamic Window Approach (DWA)**
**Most popular for mobile robots**
**How it works:**
- Samples possible velocity commands (v, ω) within robot's dynamic limits
- For each velocity, predicts where robot will be in next few seconds
- Scores each trajectory based on:
    - How close it gets to goal
    - How well it avoids obstacles
    - How smooth the motion is
- Picks best velocity command
## **How They Work Together**
1. **Global planner** says: "Go from here to waypoint 50m ahead in that direction"
2. **Local planner** says: "I see a tree blocking that path, so I'll steer slightly left while still heading toward that waypoint"
3. Once past the obstacle, local planner guides back toward the global path

## **How to Set GPS Waypoints in RViz**

### **Option 1: RViz with Georeferenced Background**
1. **Load a satellite image** or aerial photo of your test track into RViz as a background
2. **Georeference the image** so pixel coordinates map to real GPS coordinates
3. **Click on RViz** to set waypoints - these get automatically converted to GPS coordinates
4. **Publish the goal** as a GPS coordinate that your navigation stack can use

### **Option 2: RViz in Local UTM Frame**
1. **Convert your test area** to local UTM coordinates
2. **Set RViz's fixed frame** to your UTM frame (e.g., "utm")
3. **Click in RViz** to set points in UTM coordinates
4. **Navigation stack** works directly in UTM without needing GPS conversion

## **ROS Packages That Help**
- **mapviz**: Specifically designed for GPS/outdoor robotics with satellite imagery
- **geodesy**: Handles GPS ↔ UTM conversions
- **robot_localization**: Provides the coordinate frame transforms

## **Alternative: Manual GPS Entry**
For testing, you can also:
- Walk to your desired destination
- Record the GPS coordinates
- Publish them as navigation goals via command line or simple GUI

#### **5. Control**
**Goal**: Convert planned path into low-level drive commands (steering, throttle, brake)
- **Options**:
    - If you can interface with the **Kia’s drive-by-wire system**, use `ackermann_msgs/AckermannDriveStamped`
    - Otherwise, simulate actuation using an external controller or testing framework
- **Controllers**:
    - Use `nav2`'s local planners or write a simple **Pure Pursuit** or **Stanley controller**
- **Interface**:
    - If CAN access: Use something like **SocketCAN** with a DB9 to OBD-II converter

## **Control Architecture Hierarchy**

```
Global Planner → Local Planner → Control → Vehicle
     ↓              ↓              ↓         ↓
  Waypoints    Velocity/Path   Steering/   Actual
  (GPS/UTM)    Commands        Throttle    Movement
```

## **What Each Layer Outputs**

### **Global Planner Output:**

- Sequence of waypoints: `[(x₁,y₁), (x₂,y₂), (x₃,y₃)...]`
- High-level path geometry

### **Local Planner Output:**

- **Desired velocity**: `v_desired = 2.5 m/s`
- **Desired path/trajectory**: Smooth curve avoiding obstacles
- **Sometimes angular velocity**: `ω_desired = 0.1 rad/s`

### **Control Layer Input/Output:**

- **Input**: Desired trajectory from local planner + current vehicle state
- **Output**: **Actuator commands** for your Kia Soul:
    - **Steering angle** (degrees or radians)
    - **Throttle/brake** (percentage or force)
    - **Gear selection** (D, R, P)

## **Control Algorithms for Your Kia Soul**

### **Lateral Control (Steering)**

**Pure Pursuit Controller:**

- Looks ahead on the path from local planner
- Calculates steering angle to reach a "lookahead point"
- Simple and works well for cars

**Stanley Controller:**

- Controls both heading error and cross-track error
- More sophisticated than Pure Pursuit
- Better for higher speeds

### **Longitudinal Control (Speed)**

**PID Controller:**

- Compares desired speed (from local planner) vs actual speed
- Outputs throttle/brake commands
- Simple and effective for basic speed control

## **ROS Implementation**

**Typical message flow:**

```
Local Planner → /cmd_vel (Twist message)
     ↓
Control Node → /vehicle_cmd (custom message)
     ↓
Vehicle Interface → CAN bus → Kia Soul actuators
```

## **Control Node Responsibilities**

1. **Path Following**: Convert local planner's path into steering commands
2. **Speed Control**: Maintain desired velocity from local planner
3. **Safety Limits**: Ensure commands don't exceed vehicle capabilities
4. **Coordinate Transformation**: Convert from planning frame to vehicle frame
---

#### **6. Mission Logic and Supervision**
**Goal**: Monitor system status and issue high-level commands
- **What it does**:
    - Start mission
    - Send navigation goal
    - Monitor goal completion and handle failure (e.g., timeout, blocked)
- **Implementation**:
    - Custom ROS2 node to set `geometry_msgs/PoseStamped` goal for the planner
    - Monitor `/goal_reached`, `/controller_server/feedback`, and sensor health



### Global Planning
## **The Core Concept**

Instead of manually driving around and recording waypoints, you create a **representation** of your test track that the global planner can search through automatically. When you give it a start and end point, it figures out the path on its own.

## **Approach 1: Graph Representation**

Think of your test track like a **subway map**. You define key locations (intersections, turns, straights) as "stations" and the roads between them as "train lines."

When you want to get from Point A to Point B, the planner looks at this subway map and figures out which stations to visit in sequence. It's like asking "How do I get from Times Square to Brooklyn?" - the system looks at the subway map and says "Take the 4 train to Union Square, transfer to the L train, then take it to your destination."

The beauty is you only need to define the track structure **once** - where the major points are and how they connect. After that, any start/goal combination gets planned automatically.

## **Approach 2: Grid Representation**

Imagine taking a satellite photo of your test track and overlaying a grid on top of it, like graph paper. Each square is either "drivable" (empty space) or "blocked" (obstacle, barrier, out-of-bounds).

When you ask to go from Point A to Point B, the planner treats this like a maze-solving problem. It finds a path through the "drivable" squares from your start square to your goal square, avoiding all the "blocked" squares.

This is more flexible than the graph approach because it can handle any arbitrary start and end point, even ones you never thought of when setting up the system.

## **How the Search Actually Works**

Both approaches use the same basic search strategy - they explore possible paths systematically:

1. **Start** at your current location
2. **Look** at all places you can reach in one step
3. **Pick** the most promising direction (closest to goal, shortest distance, etc.)
4. **Repeat** from that new location until you reach the goal

It's like playing a board game where you're trying to reach the finish line - you consider all your possible moves, pick the best one, then repeat until you win.

## **The Key Insight**

The fundamental difference from waypoint navigation is that the planner is making **decisions** about where to go, rather than just following a predetermined route.

With waypoints, you've already decided "go here, then here, then here." With dynamic planning, you just say "get me from here to there" and the system figures out the best way to do it.

## **Why This Matters for Your Test Track**

Even in a controlled environment like a test track, this approach has several advantages:

**Flexibility**: You can ask the car to go anywhere without pre-driving that specific route.

**Obstacle handling**: If there's a temporary obstacle (cone, another vehicle), the planner can route around it automatically.

**Multiple routes**: The system can find different paths between the same two points - maybe a longer but smoother route vs. a shorter but more complex one.

**Goal changes**: You can change your destination mid-drive and the system will immediately calculate a new path.

## **Real-World Analogy**

Think about GPS navigation in your phone. You don't need to pre-drive every possible route in your city. Instead, Google Maps has a representation of all the roads (graph) and when you ask to go somewhere, it searches through this representation to find the best route.

Your test track planner works the same way, just on a much smaller scale. Instead of having maps of the entire world, you have a representation of just your test track, but the principle is identical.

## **The Planning Process**

When you click on RViz and say "go here":

1. **Localization**: System figures out exactly where you are now
2. **Goal identification**: System understands where you want to go
3. **Search**: System explores possible paths through its track representation
4. **Path generation**: System outputs a sequence of waypoints that get you there
5. **Execution**: Local planner and control system follow this path

The crucial point is that the waypoints in step 4 are **generated automatically** by the search algorithm, not pre-recorded by a human driver.

This gives you the flexibility of dynamic planning while still working in the controlled environment of your test track.
