### Hardware Design Phase
#### Sensor Suite Selection
- ##### RGB-D Camera (Intel RealSense D435) or OAK-D Lite
	- Ideal choice for generate NeRF maps and depth information
- ##### IMU (BNO055 or MPU-9250) 
	- **The paper only uses an RGB camera but I don't see why we can't use an IMU if needed to get more accurate poses**
	- These are affordable, reliable IMU's with onboard sensor fusion
- ##### Additional Sensor Suggestions
	- Ultrasonic or IR sensors for redundancy in obstacle detection
	- These are cost effective and can cover blind spots where the RGB-D camera might struggle (near ground obstacles or rubble in construction sights)
	- Wheel encoders: These are vital for odometry in differential drive. **See motor selection**
#### Drive System
- ##### Motor type (Pololu 37D series motors with gear boxes):
	- DC gear motors are good for differential drive because of their simplicity and torque
	- We should use a 12V brushed DC gear motor which provide
		- Torque: 1- 2 Nm
		- Speed: 100-150 RPM
- ##### Encoder Capabilities
	- We should opt of quadrature encoders for precise odometry (detection direction and distance)
	- A resolution of 100-300 ticks per revolution is good enough for a medium sized robot
	- **Find appropriate motor drive or motor controller**
- ##### Chassis
	- We want a turtle bot that's roughly 30-40cm in diameter
	- This gives us room for a compact compute unit (Jetson) and for sensors to be mounted with unobstructed views
	- If 3D printing we should use PLA/ABS
- ##### Motor Driver
	- L298 is a budget friendly option
	- Sabretooth 2x12 is a more robust option and offers support for differential drive
- ##### Motor Controller
	- I would opt for a motor controller instead so we can avoid writing the low level code for the driver
	- Pololu also sells motor controllers that can be connected via USB to the Jetson Orin nano as well 
		- Here is an example of one that can be used with our selected motor: https://www.pololu.com/docs/0j77/all
#### Compute Platform
- ##### Nvidia Jetson Xavier NX or Orin Nano
	- Excellent for handling NeRF computations, sensor processing, and real-time path planning

#### Power System
- ##### Battery
	- LiPo 3S (11.1V, 5000mAh) or similar
	- This will provide enough power for motors and compute
	- We also need step-down converters for powering the compute unit (5V for Jetson)

#### General Notes on Hardware Design
- The use of IMU and encoders are optional however using odometry makes localization a lot more robust 
- We can use techniques like Kalman filter localization to better localize the robot in the environment
- This works by using odometry initially to predict where the robot is in the environment and then we compare to the sensor measurement from the RGB-D camera to see if there is an error between the two distances
- If there is an error, the Kalman filter will fuse the two measurement depending on which measurement is weighted more (typically the RGB-D sensor is more reliable) and then determine a final well estimated position accounting for noise from both the sensor and odometry measurement
- This would mainly be helpful if notice errors in the depth measurement of the RGB-D camera

### Software Design and Flow

#### General Notes on Software Design and Flow
- It might be better to pick up the robot and take pictures with the on board camera or another suitable method rather than using iPhone cameras
- iPhone's lack depth sensing sometimes (or may not be reliable) which means the NeRF has to estimate depth entirely from RGB data
- The biggest problem is the fact that both cameras will have different camera properties
	- Differences in lens distortion, field of view, and image resolution between the iPhone and the RGB-D can cause mismatches between the map and real-time sensor data
- RGB-D is better because:
	- It provides depth data
	- Ensures intrinsic parameter and sensor consistency between mapping and runtime
	- It integrates seamlessly with the robot's existing hardware and workflow
- The constraints of the project mean that we have to use just an RGB camera and no external sensors or encoders to ensure accuracy 
- 

#### 5 main modules we'll be working with
- Perception Module
- Localization Module
- Path Planning Module
- Control Module
- System Integration Module

### 1. Image Capture and NeRF Generation
#### 1. Image Capture
- ##### Equipment Needed
	- RGB-D Camera: For capturing images 
	- Robot Chassis: To move the robot in a systematic manner to capture the entire room
	- IMU and Encoders: Use these to assist in estimating camera poses for training
- ##### Capture Strategy
	- Ensure complete coverage of the room by capturing images from diverse angles and heights
	- Move the robot around the room systematically (maybe follow a grid patter) to capture key areas
	- Minimize variations by keeping lighting conditions consistent throughout the capture process
	- Capture overlapping frames to ensure feature continuity for NeRF training
- ##### Settings
	- Use the highest resolution the RGB-D camera supports to ensure NeRF accuracy
	- Set an appropriate frame rate for the robot speed (2-5 fps if moving slowly)
	- Ensure the depth camera is calibrated for the room's size and avoid very close of far objects if outside of the sensor's rage
- ##### Data storage
	- Store captured data (RGB images, depth maps, corresponding metadata) on the robot's local storage or stream it to the host computer over Wi-Fi or Ethernet
- ##### Pose estimation for each frame
	- You can use a visual odometry tool (COLMAP or OpenVSLAM) to estimate all the camera poses 
	- Encoders will be useless in assisting with pose estimation of the cameras but you can still use the IMU for orientation of the camera
	- **Fiducial Marker Systems Potentially (Barcodes)?**
#### 2. Preparing Data for NeRF training
- ##### Organize the dataset
	- Group each frame with its RGB image, depth map and estimated pose (if available)
	- Structure the dataset in the folders (images/ depths/ poses/)
		- **Depending on the NeRF model we use we probably wont have depths to use**
- ##### Pose Refinement
	- If poses are estimated using visual odometry, refine them using bundle adjustment (built into COLMAP)
	- Ensure poses are saved in a NeRF-compatible format (camera intrinsic and extrinsic file)
- ##### Format Conversion
	- Convert images to formats supported by NeRF implementation if needed
	- Normalize depth maps if required
#### 3. NeRF Creation on a Host GPU
- ##### NeRF Framework
	- Select the framework you're going to use
		- Instant-NGP
		- Torch-NGP
- ##### Setup on Host GPU
	- Install NeRF framework on host machine and ensure all dependencies are met
- ##### Training Process
	- Load the prepared dataset (images, poses, depth maps)
	- Configure any training parameters
		- Learning Rat
		- Number of iterations
		- Voxel or grid resolution (Instant-NGP)
- ##### Output
	- The trained NeRF model, typically saved as a checkpoint file or pre-exported into a query able format (density functions, voxel grids)

#### 4. Exporting the NeRF Map
- ##### Export Options
	- Raw NeRF Model: If you want to retain the full NeRF representation for runtime queries
	- Derived formats (**This is my preferred method because its easier to work with this for robotics**)
		- Point Cloud
			- I think a point cloud might defeat the purpose of the lab because that means we have to start dealing with distances and we only use the depth option of the RGB camera
		- Mesh
		- Occupancy Grid (2D or 3D)
			- This is needed for our path planning so we need to find a way to convert the NeRF map to an occupancy grid
- ##### Transfer to robot
	- Upload the exported map or NeRF model to the robot's storage

### 1.1 Intermediate Step (Validation of Occupancy Grid and Localization in NeRF map)

#### 1. Validate Occupancy Grid
- Use visualization tools to do this
#### 2. Robot Localization
- This step is pretty crucial, we need the robot to able to localize itself
- I think there's some localization methods in the repo for nerf navigation which would be to match the camera pose with the NeRF model to see where it is in the environment (**You'll have to give the paper a read to see how they localize the robot in the env**)
- In the paper they actually use Kalman Filter localization (EKF)
- **I wonder how they get their prediction though**

### 2. Path Planning
- **Path planning (trajectory generation) with NeRF is actually covered in the paper which means there may not be a need to develop a solid plan for path planning**
#### 1. Pre-Processing
- Occupancy grid preparation:
	- Validate that the grid resolution matches the robot's size (cells are not too large or too small for the robot to navigate)
	- Free cells are marked as 0, and occupied cells are marked as 1
	- Unknown cells are handled appropriately 
- Define the start and goal pose

#### 2. Post Processing
- Path smoothing:
	- Simplify the waypoints to reduce sharp turns
	- Apply a spline or line-fitting algorithm to smooth the trajectory
	- Ensure the smoothed path avoids obstacles

#### 3. Integration with the Robot
- Pass the waypoints to the robot's control module
- Use a controller to follow the waypoints