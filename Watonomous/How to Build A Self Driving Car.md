I'm a little overwhelmed leading the team because I have no idea how a self driving car works end to end and I'm not too clear on how to integrate everything together. I think I need a solid step by step plan on how to integrate the entire thing and really lay out the details for me to understand what needs to get done. First we need to understand each core concept of building a self driving car which comes down to **perception**, **world modelling**, and **action**

### My understanding
Currently I have a brief understanding of everything. Perception is what allows the car to perceive the world. We write algorithms and using machine learning models to perform tasks that allow the world to be perceived by the robot using sensors. We write algorithms like camera object detection, lidar object detection, depth estimation, 3D tracking.

We feed all this information into an HD map. I don't have a solid understanding of what an HD map is but it seems to be the basis of all of world modelling. My assumption is that an HD map is a map of the entire world. So we take every piece of information we have available and we load that into a collection "map" which we call the HD Map.

We then use this map and feed the information into out Action node which all revolves around Model Predictive Control or MPC. Model predictive control is our approach to controlling the planned path that the car has made using the global planner and local planner. For the nerf navigation project, we created a global planner and just stuck to using that planner. However, for the car we need a local planner which creates a plan based on what it sees in its surroundings. 

### The Goal
Our end goal for the term is to build a car that can get from point A to point B while avoiding obstacles. So we are basically just trying to implement a navigation stack on the car. What makes building a self driving car more difficult is that we are dealing with a variety of dynamic obstacles. For example, we have to deal with traffic lights, handle traffic signs, handle pedestrians crossing, handle control when coming near another car. However, if we look at this from a point of building a self driving car from end to end then we're not really going to get anywhere. We are tackling too many problems all at once. 

A better approach to all of this is start with what we know. Which is, can we build a robot that can travel from point A to point B while detecting obstacles and avoiding them. Obstacles in this case will most likely be humans which we need to detect and avoid. Now that we dumb it down to something a lot more simple, the question that still remains is how can we implement this stack onto the car. For any normal navigation stack where we try to make a robot get from point A to point B while avoiding obstacles, we don't use an HD map or have 3D tracking, or the HD map. 

I think we are collectively trying to achieve too much with little direction.

What I need right now, is to understand the entire stack and see what tasks we can hand off to members to use in the near future and a team of core individuals who can help me develop the plain navigation stack. This team would be **Kishore**, **Lucas**, **Brian**, **Helen**, **Jaden**, and **Stephen** 

If we can achieve this goal this term then we would have accomplished something pretty great. What this allows us to do is create a good starting point to develop a Self Driving Car. Here's the biggest issue right now. We have nothing to show to anyone. But if we can get a car to drive from point A to point B while avoiding obstacles then we would have done something pretty great. At the point we would have created at the most basic level a functioning robot. After we create a functional robot, we can start adding in the things that make a car self driving. All a self driving car is is allowing it to make dynamic adjustments based on the perceived world around us. After we make the car travel from point A to point B while avoiding obstacles, the next most important thing would be how can we make the car stay within the lane lines. After that, it would be, how do we get the car to navigate from point A to point B while staying within the lane lines. And then we continue moving onto bigger goals. 

The team lacks a lot of direction right now and I would like to start fresh again almost. Create a new repo, change our goals, start from the beginning. I think we can adapt into the mono repo after we get the basics of robotics down but until we get basic navigation set up, I don't see a point in getting everyone to work on different things without any direction. I feel like no one knows what direction this project is going in. That's a problem with having such a large team. Eddy's whole idea of building an Autonomous Car was to turn this into a research project where students can come on the team and implement whatever algorithms they wanted on the car and just test it out. I think that tackling all of this at once is a terrible way to go about developing this. I think we should take on Eve in phases rather than build a self driving car all at once. Of course we have a lot of of useful things done for the car as well like object detection, the boards, HD map is coming together, but we don't have a way to put all those pieces together. I feel it that it might be more effective to start with Phase 1, which is to get a car in an open lot to go from point A to point B. This would be a much more basic navigation stack. I can finalize what this stack should look like but I think simplifying our goals and getting everyone on the same page would be great. 

The one thing about taking on this smaller team approach is that we need to make sure all members feel included. We can't have it so that only a few members know what's going on. We have this huge team so we need to make sure they are all working on things that are relevant to the future of the car as well. This is where things like depth estimation, HD map, 3D tracking all come into play.

## ✅ Recommended Architecture: Lot Navigation System

### **1. Localization**

#### **Primary Method:**

- **RTK GPS + IMU + Wheel Odometry**
    
    - Fuse using **robot_localization** package (ROS2) or a custom **Extended Kalman Filter**.
        
    - Provides global `base_link` pose in the `map` frame.
        

#### **Backup / Drift Correction:**

- **Visual-Inertial Odometry (VIO)** using the **3 cameras + IMU**:
    
    - Options:
        
        - [VINS-Fusion](https://github.com/HKUST-Aerial-Robotics/VINS-Fusion)
            
        - [OpenVINS](https://github.com/rpng/open_vins)
            
        - [ORB-SLAM3](https://github.com/UZ-SLAMLab/ORB_SLAM3)
            
    - Use this when GPS degrades (e.g., near buildings or under trees).
        

---

### **2. Perception (GPU-powered)**

#### **A. Obstacle Detection**

- **YOLOv8** on your 3-camera views (front, left, right or stitched to wide FOV).
    
- Output:
    
    - Bounding boxes for obstacles (cars, people, cones).
        
    - Optional: Use **depth estimation** (see below) to project these into 3D space.
        

#### **B. Depth Estimation**

Options depending on your setup:

- If you have **stereo pairs**: Use stereo matching (e.g., OpenCV + ELAS/SGBM).
    
- If **monocular**: Use **Monodepth2**, **Midas**, or **DPT** for dense depth.
    
    - GPU enables real-time inference (~10–15 FPS on Jetson Orin / RTX cards).
        
- If using **LiDAR**:
    
    - Use point cloud directly to build occupancy grid or fuse with vision (e.g., camera-LiDAR fusion for object distance validation).
        

#### **C. Semantic Segmentation (Optional but powerful)**

- Use **DeepLabV3+** or **YOLO-Seg** to distinguish between drivable vs. non-drivable regions.
    
- Helps improve costmap safety and trajectory planning.
    

---

### **3. Mapping & Costmaps**

#### **Local Costmap:**

- Built from:
    
    - Real-time LiDAR point clouds.
        
    - Detected obstacles projected into local frame.
        
- Resolution: 0.1–0.2m
    
- Use `grid_map` or `nav2_costmap_2d` in ROS2.
    

#### **Global Map (Optional):**

- If the parking lot is static and reused:
    
    - Map once with **LIO-SAM** or **Cartographer** (LiDAR SLAM).
        
    - Localize using AMCL or Scan Matching.
        
    - Else, you can rely solely on GPS and costmaps.
        

---

### **4. Path Planning**

#### **Global Planner:**

- Waypoint-based navigation using GPS (convert to `map` frame).
    
- Use **Nav2’s global planner**, or custom A* path between waypoints.
    

#### **Local Planner:**

- **TEB (Timed Elastic Band) planner** or **Pure Pursuit** for path following.
    
- Inputs:
    
    - Local costmap
        
    - Goal pose in `map` or `odom` frame
        

---

### **5. Control**

- Control vehicle using **ackermann_msgs** if using Ackermann steering.
    
- Or convert to `cmd_vel` (for differential drive).
    
- Tune PID or use MPC (Model Predictive Control) if higher accuracy needed.
    

---

## 🧠 Optional (Advanced Ideas)

### **A. NeRF-Based Pose Estimation (Experimental but GPU-possible)**

- Capture parking lot from various angles and train NeRF.
    
- Use it for localization via image matching (as you explored in your other project).
    
- Not real-time yet but can be a fallback/localization correction tool.
    

### **B. Visual Place Recognition (VPR)**

- Use **NetVLAD**, **D2-Net**, or **SuperPoint/SuperGlue** for coarse relocalization using captured images (loop closure-like functionality).