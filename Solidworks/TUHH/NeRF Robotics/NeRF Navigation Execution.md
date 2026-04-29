We need NeRF navigation working in a simulation now but **how do we do this?** 

The first step was to understand the general flow of NeRF navigation and how exactly it makes an **agent** move. We figured this out already and made a flow chart for it![[43250 1.jpg|400]]

The biggest thing here was understanding how the occupancy grid was made, how the A* algorithm ran on it and what the robot had to do to reach different states given from the output of the A*. We understand now that this system works like any other robotics system in the sense that we take a grid representation of our map, find out what is occupied and not occupied using the NeRF model (this was the **innovative** step)  and then after that we simply follow the path like any other robotics autonomy model.

Another thing that is pretty unique about this approach is the replanning and state estimation feature involved. Typically we would have multiple sensors to localize and estimate where the robot actually is in real time. However, in our case we only have an **RGB** camera which means we need another way to localize after reaching each path.

This is done by feature matching and using the NeRF model. We effectively grab the key features from the image, pick the estimated pose to query from the NeRF model, compare the two pictures, calculate the error and try to minimize this to find the pose of the observed image. (**Does it do this iteratively and find another pose to compare to if the estimated doesn't match?**)

(**We should look into this more in detail when we get a chance because how exactly do you feature match and find the pose from an image**). Continuing on the statement in brackets, how do we quantify these key features and then how do we reduce the error between the observed image and the estimated pose? 

Let's focus on simulation now. We need a way to simulate this entire process but how do we do this? Well first of all what is simulation? To me simulation means to represent how the robot would move if we used the functionality we currently have. The robot needs to be able to handle all the code besides training. Training and the initial path planning can be done on a separate computer and sent to the robot afterwards. However, the rest of the code needs to be run on the robot itself. 

In the flow chart, when the agent is initialized and all the code ran after that is essentially what has to be on the robot. So we need to simulate a robot executing this code. 

In terms of simulation software I think we're going to work heavily with isaac sim for now because they have an option to query the NeRF. But I don't see how this is relevant since all the robot needs are just motor commands to move. Even the occupancy grid isn't necessarily needed other than for visualization purposes. Actually this is wrong.

Lets think about this. First we have a robot. What do we want the robot to do? We want to go from one point to another point in an environment. So we need to put the robot in an environment with obstacles like a room first. After we have this room we need to train the model on this room. We also need to set up a bounding box for the room itself. **How do we scale the robot so that it fits in the room and the dimensions are accurate?** 


### Why and When Should we use ROS2

#### Inter Process Communication
ROS2 enables different nodes of the robot to communicate asynchronously using a publisher-subscriber model.

An example of this would be a perception node publishing an **occupancy grid** and a **path planning node** can subscribe to this in order to create a path

#### Modular Design
ROS2 follows a node-based architecture where each function **(NeRF processing, path planning, localization)** can be implemented in their own nodes which makes debugging and scalability easier

#### Real-time performance
ROS2 has an improved Quality of Service (QoS) feature which makes it better suited for real-time robotic applications compared to ROS1

#### Hardware Agnostic
ROS2 already has standard interfaces for sensors, actuators and controllers making it easy to integrate different hardware components

#### Simulation and Visualization
Tools like Gazebo, Foxglove and Rviz allow for easy simulation and visualization of robotic data before deploying it on real hardware

### ROS2 Infra Design

![[Pasted image 20250129113935.png]]

#### Perception Node
Purpose: Uses the NeRF model to create **3D Occupancy Grid**
Inputs:
- Camera feed (/camera/raw_image)
- NeRF model query (/nerf_query)
Output:
- **3D Occupancy Grid** `/occupancy_grid`
Process:
- Generate a **3D grid** with `torch.meshgrid()`
- Query NeRF at each grid coordinate to obtain **density values**
- Perform **max-pooling** on density values to downsample
- Create **binary occupancy grid** (`density > 0.3 = occupied)
**CAN I GET THIS DONE TODAYYYYY**
#### Path planning Node
Purpose: Runs A* algorithm on the occupancy grid to compute a **collision-free path**
Inputs:
- Start and goal coordinates (`/start_goal)
- Occupancy grid (`/occupancy_grid`)
Outputs:
- Planned path (waypoints) (`/waypoints`)
Process:
- Convert **NeRF world coordinates** to **grid coordinates**
- Run **Astar** to find a collision free path
- Convert path back to **NeRF Coordinates**
- Publish the **waypoints**

#### 3. State Estimation Node
- **Purpose**: Estimate the JetBot’s **pose (position + orientation)** using only an **RGB camera** and the **NeRF model**.
- **Inputs**:
    - **Real camera feed** (`/camera/image_raw`)
    - **Initial pose hypothesis** (`/pose_guess`)
- **Outputs**:
    - **Refined pose estimate** (`/pose_estimate`)
- **Process**:
    1. **Capture an RGB image** from the JetBot’s camera.
    2. **Generate a synthetic image** from the **NeRF model** based on an estimated pose.
    3. **Compare the two images**:
        - Compute the difference between the real and synthesized images.
        - Use a loss function (e.g., **photometric loss**) to quantify the difference.
    4. **Optimize the pose**:
        - Adjust the pose to minimize the difference.
        - Use an **Adam optimizer** to refine the pose iteratively.
    5. **Publish the updated pose estimate** (`/pose_estimate`).

#### 4. Motion Controller Node
- **Purpose**: Converts waypoints from **path planning** into motor commands.
- **Inputs**:
    - **Waypoints** (`/waypoints`)
    - **Current pose** (`/pose_estimate`)
- **Outputs**:
    - **JetBot motor commands** (`/cmd_vel`)
- **Process**:
    1. Compute required **linear & angular velocities** to reach the next waypoint.
    2. Use a **PID controller** to smoothly follow the path.
    3. Publish **velocity commands** to the JetBot.

#### 5. Simulation/Debug Node (Optional)
- **Purpose**: Simulates the JetBot in **Isaac Sim / Gazebo** for debugging.
- **Inputs**:
    - Simulated **camera feed** (`/sim_camera/image_raw`)
    - Simulated **JetBot state** (`/sim_odom`)
- **Outputs**:
    - **Simulated occupancy grid** (`/sim_occupancy_grid`)
    - **Simulated waypoints** (`/sim_waypoints`)
    - **Simulated motor commands** (`/sim_cmd_vel`)
- **Process**:
    1. Load a **NeRF-generated environment** in Isaac Sim.
    2. Run the entire ROS2 stack in simulation.
    3. Verify path planning before deploying to hardware.

First step should be to create a robot with some dynamics. 
Second step should be to put that robot in a room
Third step should be to train the NeRF model on that room
- You need to include bounds for this and specify the dimensions of your area
- For example 1 unit in the NeRF coordinate system could be 1m in the real world with the center of the scene being (0, 0, 0)
Fourth step should be to write the code with respect to the robot so that it can navigate the room
- In the calculate everything step each way point will be matched to an action and that action has to be tweaked to give us the throttle and steering angle