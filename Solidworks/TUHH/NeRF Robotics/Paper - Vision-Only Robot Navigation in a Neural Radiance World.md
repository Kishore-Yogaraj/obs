### Basic Idea
This paper discusses a new approach to mapping, localization, path planning and collision avoidance based on a vision only system 
- Mapping
	- NeRFs allow for a compact and highly detailed 3D scene encode which makes it a great tool in theory for creating maps for robots
	- The paper assumes a pre-trained NeRF (we will most likely be training a model and then providing it to the robot instead of having real time NeRF be performed) however it's applications highlight a sophisticated method for using the environment representations in robot navigation
- Localization
	- The paper introduces an optimization-based filtering method for **6DoF pose and velocity estimation** using only an onboard RGB camera
	- This is the method used for localizing the robot in the NeRF environment by minimizing a **photometric loss** between the real world camera image and the NeRF-rendered image
- Path planning and Collision Avoidance
	- The paper also introduces a trajectory optimization algorithm which is tailored to NeRF
	- This minimizes the NeRF based collision metric by avoiding high density regions that represent obstacles
	- The planner generates smooth and dynamically feasible paths using techniques like **differential flatness** and gradient-based optimization
	- For path planning you need:
		- An initial path (A*) also known as initialization
		- Optimizations (Ada optimizer) gradient based optimization that minimizes the combined cost of collision risk and control effort
			- In the optimizations, cost functions are used to minimize the collision probability
				- Collisions are minimized by using penalizing paths that pass through high-density regions (represent obstacles)

### Paper in Detail
#### What are NeRFs?
- They represent continuous **volumetric density** and **RGB values** in a neural network and generate photo-realistic images from unseen camera viewpoints through **ray tracing** 
#### What does the paper propose?
- The paper proposes an algorithm for navigating a robot through a 3D environment represented as a NeRF using only and RGB camera for localization
- The assumption was made that the NeRF for the scene has been pre-trained offline and the robot's objective is to navigate through unobstructed space in the NeRF to reach a goal pose
- A trajectory optimization algorithm was also introduced to to avoid collisions with high-density regions in the NeRF based on a discrete time versions of differential flatness that is amenable to constraining the robot's full pose and control outputs
- The paper also introduces an optimization based filtering method to estimate 6DoF pose and velocities for the robot in the NeRF given only an onboard RGB camera 
- The trajectory planner with the pose filter is combined in an online replanning loop to give a vision-based robot navigation pipeline

#### SLAM vs Vision Based Mapping and Localization
- SLAM
	- Mapping: In SLAM, a LiDAR sensor scans the environment and generates a point cloud that represents the physical space 
	- Obstacle avoidance: The robot interprets areas with a high density of LiDAR points as obstacles
	- Free space: Areas with no or sparse points are considered free space for navigation
- Nerf-Based Navigation
	- Mapping: In this approach, a NeRF model represents the environment as a continuous 3D density field. High-density areas in the NeRF correspond to occupied space (walls, objects), while low-density areas represent free space
	- Obstacle Avoidance: The robot avoids high density regions in the NeRF model interpreting them as obstacles
	- Free Space: Regions with low NeRF density are treated as safe, navigable areas

#### Intro
- Planning and trajectory is a fundamental concept of many robotic applications
- Robot navigation methods are dependent on properties of the underlying environment representation (**voxel grid, point cloud, mesh model, signed distance field (SDF)**)
- This paper proposes a navigation pipeline for a robot when given a pre-trained NeRF of its environment similar similar to how a robot would navigate when given a SLAM map of its environment
- The paper wants to use NeRF as a geometric environment representation that enables any robot (drones or ground robots) to navigate through it

#### More NeRF
- NeRFs can compactly encode detailed 3D geometry and color which makes them beneficial to mapping applications
- NeRFs take a collection of camera images and train a neural network to give a function relating each 3D point in space with a density and a vector of RGB values (radiance)
- **The representation of this 3D point can then generate synthetic phot-realistic images through a differentiable ray tracing algorithm

#### Problems with other vision based navigation systems
- Most vision based systems require an extensive and specific amounts of training data and labels

#### NeRF Advantages
- Unlike voxel models, point clouds or mesh models, they are trained directly on dense photographic images without needing precise feature extraction, matching and alignment
- They inherently represent the geometry as a continuous density field, and so they are well-suited to robot motion planning and trajectory optimization using gradient methods or used with differentiable simulators
- NeRFs can produce photo-realistic synthethic images which gives a mechanism for the robot to "hallucinate"
	- Hallucinating for a robot means predicting or simulating what it would see if it moved to a different position or took a specific actions
	- Using a pre-trained NeRF, the robot can:
		- Specify a hypothetical pose (What would the environment look like if I moved 2 meters forward and turned 30 degrees
		- Query the NeRF to render a synthetic image of the scene from that pose)
- NeRFs are able to store geometry efficiently in memory as neural network weights rather than as dense point clouds or voxel grids which makes them ideal for use in memory constrained systems like robots


> [!Question] What are ablation studies
> - Purpose
> 	- Identify which components or features contribute most to the model's success
> 	- To assess whether certain components are redundant or ineffective
> - Methodology
> 	- Start with the full model or system
> 	- Gradually remove, replace or modify individual parts (layers, features, modules)
> 	- Measure the change in performance (accuracy, precision, recall)
> - Analysis
> 	- Performance drops indicate that the removed component is important
> 	- Minimal or no performance change suggests that the component might not be crucial

#### Problem Formulation
- Robot is only equipped with a monocular camera which seeks to navigate an environment
- The robot seeks to plan and track a collision-free path from its initial state to a final state
- The robot has access to NeRF representation of the environment which it can use for both planning and localization
- The robot body is approximated using a finite collection of points which represent the robot's bounding box

#### Methodology for Collision Avoidance and Trajectory Optimization
- Robot's body is approximated as a finite collection of points denoted by B where collision check are performed
	- This could be a 3d grid of points within the robot's bounding box or any arbitrary model of the robot's body
- **Nerf Density and Collision Probability**
	- NeRF Density describes the likelihood of spatial point stopping a ray of light
		- If the ray of light hits a wall or object it cuts off or stops the ray of light and indicates an object is present
	- This density is assumed to be a proxy of the likelihood of colliding with a physical object
		- A proxy is something used as a substitute for something else when it is difficult to measure or directly observe the real thin
		- In this context, the NeRF density is not exactly the same as the likelihood of a colliding with an object
		- instead it's just used as a proxy for the collision probability
		- The assumption is that high density in NeRF corresponds to physical objects in the scene

#### Differential Flatness
- Used to compute trajectories that pass through a series of waypoints 

#### State estimation flow
- Using the robot's dynamics model, we predict its next state based on its previous state and control inputs (motor commands or velocities)
- Feed the predicted state into the NeRF model. NeRF generates a synthetic image of what the robot "should see" from that pose
- The robot captures a real world observation (from camera) at current time step
- The image reflects what the robot is actually seeing in the environment
- Error calculation. If the predicted pose is slightly off, the wall in the synthetic image might not align with the wall in the real image leading to a measurable error
- Correction step to refine the robot's state estimate
- Replan trajectories if needed to avoid obstacles and navigate towards a goal