### main_nerf.py
This is the script that gets executed first. This executable is responsible for training the NeRF model using the provided dataset. The script will set up the mode, define the training parameters and also manages the training loop

#### Argument Parsing
The script begins by parsing command-line arguments to configure the training process such as the number of iterations, learning rate, and whether to use CUDA raymarching. These different arguments can be seen in lines 15 to 62

#### Model Initialization
Depending on the options provided by the command line, it will initialize the NeRF model using the appropriate network architecture which is done in lines 71 - 94

#### Training or Testing
If the `--test` flag is set, the script will evaluate the model using a test data set and saves the results. Otherwise it will set up the training loop including the optimizer and learning rate scheduler, and trains the model using the training data set instead

#### Metrics and Evaluation
During training it evaluates the model's performance using metrics like PSNR and saves checkpoint for later use

#### Purpose
Main purpose of this script is that it's crucial for preparing the NeRF model, which will later be used in the simulation phase which is handled by `simulate.py`. The output of `main_nerf.py` is the checkpoint folder which contains the trained parameters of the NeRF model. 

### Simulate.py
The `simulate` function in `simulate.py` is the core function in the the script and is what is executed after everything is initialized. This is the function that orchestrates the interaction between the **planner** and the **agent** and the **state estimator** to simulate navigation through the 3D environment represented by a NeRF model

### Inputs
The simulate function takes in 7 inputs which are:

#### planner_cfg
Configuration dictionary for the planner which includes:
- Start state (position, velocity, and orientation)
- End state (position, velocity and orientation)
	- The start and end state are tensors which in the case of simulation is just an array with states like `[x, y, z, vx, vy, vz, roll, pitch, yaw,] `
- Learning rates
- Total duration of simulation
- The number of actions the planner will generate
- Learning rate used during the gradient descent process to refine the trajectory
- Number of gradient descent steps to perform during the initial trajectory planning phase
- Number of gradient descent steps to perform when replanning the trajectory during the simulation
- Name of the workspace where simulation data is stored
- Inertia tensor which is used in dynamics calculations
- Gravitational constant used in the simulation (Earth's gravity
- Mass of the agent
- Physical dimensions of the agent's body, used for collision checking and planning
#### agent_cfg
Configuration dictionary for the agent:
- Mass 
- Inertia
- Simulation parameters
#### filter_cfg
Configuration dictionary for the state estimator
- Learning rates
- Noise covariances
#### extra_cfg
Additional configuration settings
- Noise characteristic for the model predictive control
#### density_fn
This function is used to query the density from the NeRF model used by the planner to assess the environment
#### render_fn
This function will render images from the NeRF model used by the agent to simulate observations
#### get_rays_fn
This function will generate rays for rendering based on camera poses and intrinsic

### Detailed Flow
The first thing that's done when you run this code is that it creates directories to store trajectory data such as the **initial poses**, **costs** and **replan data**. We set this up so that all simulation data is organized and accessible for analysis.

The initial pose is just the starting position of the robot

#### Cost
The cost refers to the numerical values that quantify the desirability or feasibility of a particular trajectory or action. Lower costs generally indicate more desirable or feasible trajectories. Costs are used to evaluate and compare different trajectories made by the planner. The planner aims to minimize these costs to find the most efficient and safe path from the start state to the end state

In these costs you have your collision cost, control effort cost and deviation cost
- Collision cost
	- Penalizes trajectories that pass through obstacles or unsafe areas
- Control effort cost
	- Measures the amount of effort or energy required to follow a trajectory
	- Lower control effort is preferred
- Deviation cost
	- Penalizes deviations from the desired path or target state

What happens is that the robot will execute the planner and the planner calculates the cost for each potential trajectory and selects the one with the lowest cost. This involves evaluating the trajectory against various criteria and constraints

The costs mentioned above are computer dynamically. The planner has a cost function which holds these costs as parameters and then aims to minimize these costs by penalizing things like collision, control effort and deviation cost

#### Replan Data
Replan data refers to the information and parameters used when the planner needs to adjust or update the trajectory during the simulation. This necessary when the agent's estimated state deviates from the planned path due to noise or unforeseen changes in the environment.

Replanning is to ensure the robot can adjust to changes and continue towards the goal even if the initial plan becomes sub optimal or infeasible

The replanning directory will have the current state, the revised path that accounts for the current state and any changes that occurred and replanning parameters (settings that control how often and under what conditions replanning occurs)

If the robot goes off path during simulation, the planner will uses the replan data to compute a new trajectory which involves recalculating costs and optimizing the path based on the current conditions

### Planner Initialization
The first step in this entire process after creating the NeRF model and getting the data from the NeRF model itself is **creating a path**. Keep in mind that a NeRF model specializes in its ability to predict what a scene looks like when given a camera pose.

So lets figure out how this code actually creates a path using NeRF, the different algorithms and the optimizer

#### How does path planner work
The planner is first initialized on line 41 as shown below:
`traj = Planner(start_state, end_state, planner_cfg, density_fn)`

It then creates the folder that holds all the costs by doing:
`traj.basefolder = basefolder`

In line 46 we create the **coarse trajectory** which is a rough estimate or approximation of the path that the robot follows. The coarse trajectory should be viewed as a simple path from point A to point B and does not necessarily avoid obstacles yet. In some cases it may avoid static obstacles.

Let's look further into how the coarse trajectory is created

#### Coarse trajectory creation
The coarse trajectory algorithm used is A* which main goal is to find the shortest path from the start pose to the end pose. This path will be the starting point and then will be further refined using optimization techniques.
##### Grid Set Up
We first need to setup a 3D grid that we can traverse through. Each cell in the grid will represent a potential position in the environment. The size of the grid and the number of resolutions(number of cells are determined by parameters like `side` and `kernel_size`). **Side** is the dimension of the cube in the x, y and z direction so if you have a side of 100 then the cube would be 100 x 100 x 100. If the **kernel size** is 5 then there will be 20 cells along each axis.

We also approach a problem where we need to make sure the scene the NeRF generates actually fits inside of the grid we create. From lines 67 to 70 of `quad_plot.py` the code uses linspace to create a `side` number of points between the coordinates mentioned. So this makes sure there's 100 evenly spaced points (linearly spaced) along the distance specified for each axis. In a grid the **cells** are the **spaces between these points**. So if you have a 100 points between the start and end, you effectively have 99 cells between these points

We then use the `torch.meshgrid` function in line 72 which will take these points and create a 3D grid where each combination of x, y and z points forms a vertex of a cell in the grid.

The grid is defined by these points and the cells are the spaces between them. The A* algorithm will consider these cells when determining a path through the grid. `coods` represents the spatial structure of the environment which will be crucial for our path planning and other spatial computations.

So keep in mind that **coods** is now the 3D space we have created for our robot with points that can be traversed through. Also keep in mind that we are essentially working with a 3D coordinate plane. The NeRF model should return the min and max coordinates along each axis that the NeRF model covers. Many implementations will actually define a bounding box during training within which NeRF evaluates and sample points

##### Occupancy Grid
Before we continue with path we need to find out which cells are occupied and can't be accessed by the robot. This is where we go to the **NeRF model** to check and see the occupancy of each grid cell which is done by passing the coordinates to the **NeRF model's density function**. This is done in line 79: `output = self.nerf(coods)` 

`self.nerf` will query NeRF model (we are making predictions in this step) to obtain all the density values at each one of the points from the grid we made. So the output variable will contain the density values for all the points in the gride which is typically a tensor with same spatial dimensions as coods.

Now that we have the density values, we have to actually process these values to determine which regions of the 3D grid are occupied. So far we just grabbed the density values, but the planner doesn't actually know what that means yet.

To solve this problem we would use lines 80 to 84 which is the **max pooling** and **downsampling operation** 

`maxpool = torch.nn.MaxPool3d(kernel_size = kernel_size)` this is a 3D max pooling operation which is used to take the maximum value of a local region defined by the kernel size. The **kernel** size is a window which groups all the density values in a local region and finds the max value.

The max pooling is applied here: `occupied = maxpool(output[None,None,...])[0,0,...] > 0.3` where we check to see if the max density value is greater than the threshold and if it is, it converts it to a binary occupancy grid (1 or 0 for occupied space vs free space).

**Downsampling** is the process of taking larger sample and making it smaller. **Resolution** is how detailed a dataset is. More resolution means more grid cells and less resolution means less grid cells.

We still have a 3D occupancy grid except now the resolution is a lot smaller and the value are all binary indicating free space or occupied space. We also lose all original 3D coordinates. However each binary value effectively represents a space in the environment.

##### Conversions
Now that we have our occupancy grid set up and we're able to path plan through them, we need to find out how to represent the real world coordinates in terms of the grid so that we have an accurate start and end for the path planning algorithm

lines 89 to 82 take car of the conversion for us. What we need is a localization step. The conversion happens from the NeRF models coordinate system to the occupancy grid that created. So it would start on some binary value. The localization step could be to place the robot down, query the NeRF model to return the pose based on the image it sees. Or we could just estimate it in a gui.

In line 86: `grid_size = side // kernel_size` gives us the new 3D grid size after performing max pooling. Now we are able to access each binary space by using their index

##### Finding the path with astar
Line 95: `path = astar(occupied, start, end` will find the shortest path from the start to the end point within the occupied 3D grid. This line calls the A* algorithm (can be found in `quad_helpers.py)` The variable returned which is `path` is a list of indices representing the path from the start to the end point. This path avoids occupied cells and is optimized for the shortest distance.

Lines 98 to 115 are responsible for smoothing out the path and transforming the index coordinates back into real world coordinates that we can use to traverse.

We then optimize this path to further avoid any other collisions by using the `learn_init` method which optimizes the path that was created to further avoid any other obstacles.


### General Flow
Initialize the planner `traj = Planner(start_state, end_state, planner_cfg, density_fn)`
Initialize folder to store all states after optimization `traj.basefolder = basefolder`
- These are called "states" but they're actually jus waypoints that were optimized by the path planner
- They are called states because they have another dimension added to them for actual state calculation later
Create a coarse trajectory with A* `traj.a_star_init()`
Optimize A* waypoints and add extra dimension for state calculations `traj.learn_init()`
Initialize agent (robot) with the correct config and start points `agent = Agent(agent_cfg, camera_cfg, blender_cfg)`
Figure out how many states there are in between the start and end position
- This starts off by calling `get_actions` which calls `calc_everything` 
- `calc_everything` will give you the states for the corresponding waypoints you calculated using A*
- The `steps` variable will hold record the number of states between the start point and the end point
Loop through each state in steps
- For each step get the corresponding action (thrust and torque in all directions) `traj.get_next_action`
Apply the action to the agent (robot) `agent.step`
- After applying movement, observed image
Calculate state based on predicted state from `self.agent.drone_dynamics` and the actual state from the observed image
- The code uses the find_POI to detect points of interest in the observed image which is done using the SIFT algorithm
- The estimate_relative_pose function will then take a random pose and find the image rendered from that pose
- The optimization function will attempt to match keypoints from the random pose image to the observed image
- If the keypoints don't match, then the function will attempt to reduce the error that quantifies these key points 
- It will keep doing this iteratively until it matches or a max number of iterations has been reached
- Once it matches, we know the pose from the random pose is the same as the pose for the observed image
- The estimate state function will then compare and provide a new state that is more accurate
The planner is then updated with the current estimated position `traj.update_state(state_est)`
The planner then calculates everything using the new estimated position as the new start value as well as the new path and the process repeats until we reach the end
![[43250.jpg]]



















Update the state and shift everything up one to start at current state
Use the adam optimizer to create a new path
Calculate new states using the new path