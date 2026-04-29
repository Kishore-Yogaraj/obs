**List of different bags available to be played**
- NuScenes-v1.0-mini-scene-0061  
- NuScenes-v1.0-mini-scene-0553  
- NuScenes-v1.0-mini-scene-0757  
- NuScenes-v1.0-mini-scene-0916 
- NuScenes-v1.0-mini-scene-1094
- NuScenes-v1.0-mini-scene-0103  
- NuScenes-v1.0-mini-scene-0655  
- NuScenes-v1.0-mini-scene-0796  
- NuScenes-v1.0-mini-scene-1077  
- NuScenes-v1.0-mini-scene-1100

### ROS Bag commands
- **Wato documentation:** https://github.com/WATonomous/wato_monorepo/blob/main/docs/dev/rosbag.md
- **Volumes created when running data_stream**
![[Pasted image 20241109232840.png]]

- You can find the path of the bag you want to play by accessing /mnt/wato-drive2\
	- This is where you should save any new bags created as well
	
```bash
./watod run data_stream ros2 bag play ./nuscenes/NuScenes-v1.0-mini-scene-0061/NuScenes-v1.0-mini-scene-0061_0.mcap #Plays the specified bag

./watod run data_stream ls nuscenes #Checks available bags in folder
```

### Placeholder to copy bag path
- Just a place to copy and past the path of the bags you want to run when testing because you can't use ./watod commands in the /mnt/wato-drive2 folder

```
ros2 bag record -o /mnt/wato-drive2/rosbags2/camera_object_detection /annotated_img /detections
./watod run data_stream ros2 bag play -l ./nuscenes/NuScenes-v1.0-mini-scene-0655/NuScenes-v1.0-mini-scene-0655_0.mcap 
```

### Steps to play the ros bag you want
- **There are two possible folder locations of where you can find the ros bags you want**
	- The first one is: /mnt/wato-drive2/rosbags2
	- The second one is: /mnt/wato-drive2/nuscenes_mcap
- To make it easier for yourself to see what bags are available to be played you can use this command:

```bash
./watod run data_stream ls <folder you want to look>
```

- Keep in mind that there may be several folders within the rosbags2 folder and the nuscenes_mcap folder
	- You must first go into the folder and find out what is the parent directory of the ros bags you want to use
		- For example the ros bags you recorded with Steven are in /mnt/wato-drive2/rosbags2/2024
	- Once you find the parent directory you can run the command shown above and it'll show you a list of all the ros bag folders
		- Each ros bag folder contains an mcap or db3 file (this is the actual bag you play) and a yawl file
	- To play you specified ros bag you will run this command (example):
	
```bash
./watod run data_stream ros2 bag play ./rosbags2/2024/rosbag2_2024_07_25-13_35_55/rosbag2_2024_07_25-13_35_55_0.mcap
```

- **Notice how the file following the name of the ros bag folder has "underscore 0.mcap" appended to the name of the ros bag file**
- Once you play the bag you can subscribe to the topic in foxglove and view the raw recording

### Check frequency of ros bag

```bash 
ros2 topic list #List of active topic being published to
ros2 topic hz <topic name>
```

### List of Good Bags
- ./watod run data_stream ros2 bag play ./rosbags2/2024/rosbag2_2024_07_25-12_47_06/rosbag2_2024_07_25-12_47_06_0.mcap
- ./watod run data_stream ros2 bag play ./rosbags2/2024/rosbag2_2024_07_25-12_14_07/rosbag2_2024_07_25-12_14_07_0.mcap
	- **Single cars**
	- **Not a data heavy environment**
- ./watod run data_stream ros2 bag play ./rosbags2/2024/rosbag2_2024_07_25-13_46_11/rosbag2_2024_07_25-13_46_11_0.mcap
- ./watod run data_stream ros2 bag play -l ./nuscenes/NuScenes-v1.0-mini-scene-0796/NuScenes-v1.0-mini-scene-0796_0.mcap 

### Run node via launch file

```bash
ros2 launch <your_package_name> nuscenes.launch.py (name of launch file)
```

### Print statement

```python
self.get_logger().info("This is an info message.")
```

### Recording a ros bag 

```bash 
ros2 bag record -o /path/to/your/folder/<bag_name> -a
# -0 specifies the output path and base name of the bag file
# -a records all topics

# or you can specify which topics to record as shown below:
ros2 bag record -o /path/to/your/folder/bag_name /camera/image_raw /lidar/scan

# you can also specify the file type you want
ros2 bag record --storage mcap -o /path/to/your/folder/bag_name -a
```

### Playing back our recorded 2d detection bag
```
./watod run data_stream ros2 bag play ./rosbags2/camera_object_detection/detections_2d/2detections_2d_0.db3
```

### Static Publisher

- This command publishes a static transform between two coordinate frames (in this case LIDAR_TOP) and (CAM_FRONT)
- This command will define the relationship (translation and rotation) between the two frames
	- It's typically used to describe the fixed position and orientation of one sensor relative to another sensor in coordinate space
- `ros2 run tf2_ros static_transform_publisher` runs the node from the tf2_ros package to publish a static transform
- Translation parameters
	- X, Y, and Z
	- All in meters
- Rotation parameters
	- roll, pitch and yaw
	- All in radians
- Frame ID
	- LIDAR_TOP is the parent frame
	- CAM_FRONT is the child frame
- Since we don't currently have transforms set up, this command will continuously publish a static transformation between two different frames if a node requires the transformation

```bash
ros2 run tf2_ros static_transform_publisher \
  --x -0.757 --y -0.016 --z 0.329 \
  --roll 2.357 --pitch -1.537 --yaw -2.351 \
  --frame-id LIDAR_TOP --child-frame-id CAM_FRONT
```