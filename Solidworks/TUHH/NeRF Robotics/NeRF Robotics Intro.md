We are aiming to develop a robot with **Advanced Vehicle Navigation, Control and Autonomation** with the use of only an **Onboard Camera**
- Navigation without the use of Lidar as a cost effective alternative to navigation
	- Avoid the use of SLAM 
	- Attempting to use a sort of **Visual SLAM**
- This would be the first Real-World Implementation of NeRF Navigation System 
- **At the end we want to be able to autonomously drive a robot on an efficient and optimized path from point A to point B while avoiding obstacles**

### Mapping
- We are essentially developing a different way to map an area
- Mapping involves creating a representation of the environment in the form of a 2D or 3D map which the robot can use to navigate 
	- The Map will most likely be 3D
- We want to be able to create this map with the use of cameras and NeRF
- Without mapping the robot would need a pre-made map or external localization methods (GPS)
- In indoor environments or places with obstacles, SLAM is particularly valuable since it allows the robot to explore and operate autonomously


> [!Question] How do we Localize?
> - I'm assuming in order to localize we would need Odometry data (Wheel encoders, IMUs) to figure out where we are in the map
> - We could also use the camera to develop an observation model which matches current sensor data with the map the robot previously built using NeRF to determine its position
> 	- The camera data can identify landmarks or features in the environment

### End Goals
- Get a working demo if NeRF in a Real World Robot
- Develop an instruction manual on how to create and implement real-world NeRF control on a robot
- Work on a Research Paper that will be publishable in the Construction Automation and Control Journal 