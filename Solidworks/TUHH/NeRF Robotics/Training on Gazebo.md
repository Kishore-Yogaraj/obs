### Creating a World
Okay so we were able to get a custom world on gazebo working pretty well which is great. We created a **sim.world file** which is the file that contains the entire world. This includes the specific entities you might have like bookshelf, walls, floors, the robot, etc.

We first used the **building editor** to create the model.sdf which contains the basic environment we created at first with the 4 walls.

The typical **workflow** is to create different **sdf** files with different entities such as specific obstacles, robot parts, etc. and then we can integrate them into the world file so that they get displayed on the screen.

The **pose** and **size** of theses sdf files can be configured in the **world** file or the **sdf** file. The world file takes **priority in pose over the sdf file** but the **sdf file takes priority over the world file when defining the size.**

### Using a Camera
So I know from the start that we have to create a new sdf file for the training since its a new entity that we will be using. I haven't looked at it enough but I believe we have to use ROS to actually get the images from the camera. There's no python script or something similar to automate the recording process and I don't think you can take pictures with the GUI. 

I have created an sdf file for the camera but how do we actually take pictures using this camera that we create? I believe we have to set up a ros2 node to take pictures of the environment and then automate the process of moving the camera with a ros2 node as well. 

I believe we have to setup a **plugin** in order to get ROS2 to interface with the camera in gazebo and get the sensor information. A plugin **bridges** the connection between ROS2 and the simulation environment. This answers the question **how do we get sensor images from gazebo as a ros message?**.

### How to Run our Gazebo with Ros2 Launch File
```python
ros2 launch gz_training gazebo_ros2_bridge.launch.py
```

### How to use rqt to visualize
```python
ros2 run rqt_image_view rqt_image_view
```

### How to check topics being published
```python
ros2 topic list
```

### How to check frequency of topics
```python
ros2 topic hz <topic_name>
```

### How to check topic info
```python
ros2 topic echo <topic_name>
```

### ROS2 Humble
ROS2 humble uses gazebo fortress

```python
ign gazebo sim_environment.sdf

ign gazebo

ros2 launch ros_gz_sim gz_sim.launch.py gz_args:="sim_environment.sdf"

ign topic -l

ign topic --list

ign topic --help

ign topic -i --topic <topic name>

ign topic -e --topic <topic name>

ros2 run ros_gz_bridge parameter_bridge /camera_info@sensor_msgs/msg/CameraInfo@ignition.msgs.CameraInfo

ros2 run ros_gz_bridge parameter_bridge /camera@sensor_msgs/msg/Image@ignition.msgs.Image

ign --service /world/default/set_pose \ --reqtype ignition.msgs.Pose \ --reptype ignition.msgs.Boolean \ --timeout 5000 \ --req 'name: "camera" position {x: 1.0 y: 0.0 z: 1.0} orientation {x: 0.0 y: 0.0 z: 0.0 w: 1.0}'

python3 move_camera.py   --start_x 6.5 --start_y 0 --start_z 0   --start_roll 0 --start_pitch 0 --start_yaw 179.9087   --dome_center_x 0 --dome_center_y 0 --dome_center_z 0   --radius 6.5 --steps 25 --delay 1 --final_pitch 91.6732 --num_positions 30

# Example rendering workflow
real_world_pos = [6.5, 0, 0]  # meters
colmap_scale = 4.0/6.5  # saved from colmap2nerf.py
nerf_scale = 1.0  # from training

nerf_pos = real_world_pos * colmap_scale * nerf_scale
# Use nerf_pos for rendering
```

![[Pasted image 20250327115316.png]]

![[Pasted image 20250327132605.png]]

![[Pasted image 20250327132630.png]]

![[Pasted image 20250327132707.png]]

https://gazebosim.org/docs/fortress/ros2_integration/
