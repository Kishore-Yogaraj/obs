
### Error with Docker not being able to access metadata for the specified base image which in our case is **ghcr.io/watonomous/wato_monorepo/base:humble-ubuntu22.04**

```       
 => ERROR [tracking internal] load metadata for ghcr.io/watonomous/wato_monorepo/base:humble-ubuntu22.04  
                          
failed to solve: ghcr.io/watonomous/wato_monorepo/base:humble-ubuntu22.04: failed to resolve source metadata for ghcr.io/watonomous/wato_monorepo/base:humble-ubuntu22.04: failed to authorize: failed to fetch oauth token: unexpected status from GET request to https://ghcr.io/token?scope=repository%3Awatonomous%2Fwato_monorepo%2Fbase%3Apull&service=ghcr.io: 403 Forbidden
```

### **Solution**
- The problem was that I wasn't authenticated to use certain images or repositories
- GHCR (Github Container Registry) requires authentication for private images and repositories from all users in the watonomous organization
	- All I had to do was reauthenticate myself and then I was able to pull the base image
- There are two different commands you can use to reauthenticate yourself
- Command 1
	- **I got an error with this command because you can't perform an interactive login from a non TTY device**
		- This basically means I attempted to login to docker with an environment that expects inputs like password entry but it doesn't have access to TTY (interactive terminal)
		- Docker tried to ask me for a password but it wasn't running in a regular command line window where you can type responses (this is a TTY or terminal)
		- Using the below command doesn't work because it doesn't let docker know I'm already using a normal terminal window
			- Instead we can simply specify the token so docker doesn't have to ask us for the specific inputs
```bash
echo $GITHUB_TOKEN | docker login ghcr.io -u <your_github_username> --password-stdin
```

- Command 2 (worked)
``` bash
docker login ghcr.io -u <your_github_username> -p <your_github_token>
```

### Error with ROS2 Commands not running in the Dev Container. Getting the ModuleNotFoundError: No module named 'tracking' Error

```bash
Traceback (most recent call last):
  File "/home/bolty/ament_ws/src/install/tracking/lib/tracking/detection_publisher", line 33, in <module>
    sys.exit(load_entry_point('tracking==0.0.0', 'console_scripts', 'detection_publisher')())
  File "/home/bolty/ament_ws/src/install/tracking/lib/tracking/detection_publisher", line 25, in importlib_load_entry_point
    return next(matches).load()
  File "/usr/lib/python3.10/importlib/metadata/__init__.py", line 171, in load
    module = import_module(match.group('module'))
  File "/usr/lib/python3.10/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1050, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1027, in _find_and_load
  File "<frozen importlib._bootstrap>", line 992, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "<frozen importlib._bootstrap>", line 1050, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1027, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1004, in _find_and_load_unlocked
ModuleNotFoundError: No module named 'tracking'
[ros2run]: Process exited with failure 1
```

### **Solution**
- The module couldn't be found because the tracking folder (code) didn't have a proper init.py folder
```python
__init__.py #proper formatting of init.py
```
- The init.py file was needed in all folder that contained some type of code so this includes the core folder the utils folder and the tracking folder (that contains the code)

> [!Question] Why do we need an init.py file
> - The init.py file is what helps python identify that the folder can contain other files (modules) you might want to import in your code
> - Without this file in your folder, python will ignore the folder making it hard to use files from it in your import
> - For example in the tracker.py file we have several imports from custom modules that we created (core and utils)
> - Each one of these folder (package) contain an init.py file which tells python that this folder contains modules which are just a single .py file that contains code that you can use anywhere in your code. For example we have a utils folder which contains the init.py file which contains the module ros_util.py which is used in tracker.py
> 	- Within each of these modules will be code which will have several functions that can be called within your main code via the syntax: {module}.{function_name}
> 	- For example: ros_utils.obstacle_to_bbox
> - A python package is a collection of related modules that are grouped into one folder
> 	- This is the folder that contains the init.py file which tells python that its a package
> - In the code you'll often see the package being called and have it import a module from the package in the header of the code
> 	- For example: from core import ros_utils (just an example)
> 	- If working with a package thats in the same directory as the main python file you would do: from .core import ros_utils
> 	- If working with a package (utils) that is inside another package (core) in which that package is inside the directory with the main python file you would write:
> 		- from .core.utils import ros_utils
> - init.py file can be used to clean up code with import statements
> - print statements
> - controlled imports

### Error with no module named tf_transformations

```bash
Traceback (most recent call last):
ModuleNotFoundError: No module named 'tf_transformations'
[ros2run]: Process exited with failure 1
```

### **Solution**
- tf_transformations is a dependency for ros2 which can be installed using the ROS package manager
- This package is used for ROS 3D transformations 
- This is a package that needs to be installed from the system's package repository instead of as a python package because it isn't a python package
- Instead of pip we use apt (advanced package tool) which usually requires sudo privileges
	- However when creating our dev container we are given sudo privileges when building so we must specify we want to apt install something by specifying it in the docker file
- The tf_transformation package is already pre-compiled for the ROS humble environment
- When we run the command sudo apt install ros-humble-tf-transformations apt will fetch the package from the official ros repo and install it
- The ros repo is installed when you install ros2
- The package manager keeps track of where the packages are installed and what other packages they depend on
- With a package manager you don't need to manually download, compile, or keep track of dependencies or where packages are located because it automates this process
- In the docker file we added this line to install the package:

```dockerfile
# Install Rosdep requirements
COPY --from=source /tmp/colcon_install_list /tmp/colcon_install_list
RUN apt-fast install -qq -y --no-install-recommends $(cat /tmp/colcon_install_list)

#-y means to say yes to every question that is prompted
RUN apt install ros-$ROS_DISTRO-tf-transformations -y #Line that was added
```

### Error with ability to increase storage of slurm node when building
- I was getting an error where there wasn't enough space to build the camera_object_detection container
- I tried changing the temp disk space in the session_config.sh file but the changes weren't reflected when I used the command:
```bash
df -ah | grep /tmp #Shows you how much disk space you have and how much is avail
```

### **Solution**
- The solution to this was changing the value in line 19 of the session_config.sh file which is as shown below:
```shell
# SLURM tooling configuration
export UPDATE_WATO_ASD_TOOLING=0 # Set to 1 if you want to update ASD tooling on remote hosts
```

- Any time you make changes to the slurm dev node you're building you must set this value to 1 in order to make any changes
- **Jimmy said to just leave it as 1**

### Error with rebuilding image after closing docker container: failed to solve: stat /tmp/docker/overlay2/77o60zwb8dfywu5d67e0ye8w9: no such file or directory

- Had an error when building the camera object detection container again 
- The error we saw that states: stat /tmp/docker/overlay2/77o60zwb8dfywu5d67e0ye8w9: no such file or directory usually indicates there was a problem with docker's chacing system or a missing directory in the file system

### **Solution**
- The solution to this was to clear the docker build cache
	- The build cache can get corrupted sometimes and the reason for this might be because I closed the dev container improperly

> [!Question] What is Docker Cache System?
> - Docker's caching system is a mechanism that optimizes the image building process by reusing layers that haven't changed between builds
> 	- This will speed up the system build, reduce resource consumption and ensure consistency across builds
> - Docker images are composed of layers where each layer represents a specific instruction on how to build the image which is specified in the docker file
> - Each layer is a snapshot of the filesystem after the instruction is executed
> - After a layer is created it doesn't change which means layers can be used across different images and builds because it is immutable
> - When an image is built, docker evaluates each instruction in the docker file, checks to see if a cached layer (cache is a layer in memory) exists that corresponds to the instruction and the context at that point

- Basically docker was attempting to use a cached layer but couldn't find it which could be caused for a variety of reasons
	- file system error
	- improper shutdowns
	- outdated versions
	- The list could go on
- Docker uses storage drivers like **overlay2** or **aufs** to manage the different layers that are stored
	- If this storage driver encounters issues it may be unable to retrieve specific cache layers which can lead to errors
	- Forced reboots or shutdowns while docker is actively buidling or accessing layers can result in corruption within the overlay or system
	- In our error message we can see **overlay2** as part of the message which means there was an error with the storage system
		- This was most likely because of our forced shutdown

**Commands to use**
```bash 
docker builder prune #Cleans up all unused build cache data from Docker 
# Frees up disk space
# Resets cache to a clean state

docker build <name of dockerfile> --no-cache # Rebuilds each layer from scratch and doesn't use any cahced layers
```


### Error with Camera Object Detection node with performing detections on a video with a different YAML file than the one it was configured for

```bash
[WARN] [1731281859.211126430] [camera_object_detection_node]: **New publisher discovered on topic '/front_camera/image_color', offering incompatible QoS. No messages will be received from it. Last incompatible policy: RELIABILITY**
```

**What is happening in each the self.subscription call back**
- **Line 1 creates a subscription topic in ROS2**
	- self.create_subscription is a method provided by ROS2s node class which the CameraDetectionNode inherits from
	- It sets up a connection to a specific topic so the node can receive messages published to this topic
	- self.subscription will hold the reference to this subscription, allowing the node to manage it (keeps it active for the node's lifetime)
- **Line 2 determines the message type that the subscription expects**
	- sensor_msgs.msg.Image is used if the images are uncompressed, while sensor_msgs.msg.CompressedImage is used for compressed images
	- This selection is controlled by the self.compressed parameter in the node, which should be True if the camera feed is published in a compressed format
- **Line 3 is the name of the topic that the node will subscribe to**
	- The camera_topic is defined by a parameter, so it can be dynamically configured
- **Line 4 is the callback function that will get called every time a message is received**
	- The image_callback function processes each incoming image and performs object detection on it
	- Every time a new image is published to the topic, self.image_callback will be triggered with the image message as an argument
- **Line 5 defines the Quality of Service (QoS) profile for the subscription**
	- QoS setting control different communication parameters between publishers and subscribers like reliability, durability, and message queue depth
	- ROS2 uses QoS policies to handle varying network conditions and ensure message delivery as per the node's requirements
- **Line 6 - 8**
	- QoSProfile specifies individual QoS policies that should match between the publisher and subscriber for successful communication 
		- This profile will make sure that the message received by the node are set to the correct communication parameters because the message sent could have been sent with different configurations
	- **reliability=QoSReliabilityPolicy.BEST_EFFORT**
		- This defines how strict the subscription is about receiving messages
		- **BEST_EFFORT** means that the subscriber may miss messages if the network connection is poor
			- This is used when occasional data loss is acceptable
		- The alternative to this is **RELIABLE** where ROS2 will attempt to ensure all messages are delivered
		- In the case of our error, it occurred because the publisher and subscriber were set with mismatched reliability policies
		- **Check YAML file for compatibility**
			- offered_qos_profiles: "- history: 1\n  depth: 5\n  reliability: 2\n  durability: 2\n  deadline:\n    sec: 9223372036\n    nsec: 854775807\n  lifespan:\n    sec: 9223372036\n    nsec: 854775807\n  liveliness: 1\n  liveliness_lease_duration:\n    sec: 9223372036\n    nsec: 854775807\n  avoid_ros_namespace_conventions: false"
			- beside **reliability: 2** we can see that the key is set to 2. In ROS2 **1** represents BEST_EFFORT and **2** represents RELIABLE
	- **durability=QoSDurabilityPolicy.TRANSIENT_LOCAL**
		- Controls if the subscriber should receive messages from the publisher before it started
		- **TRANSIET_LOCAL** means the subscriber will receive the latest message even if it was published before the subscriber joined
			- **Here’s a step-by-step explanation:**
				- The buffer stores messages up to its limit (e.g., messages 1 to 5 if the limit is 5).
				- When the subscriber connects during the publishing of message 3, it doesn’t process messages 1 to 4.
				- Instead, the subscriber starts with the latest message in the buffer at the time of connection — which is message 5.
				- This ensures that the subscriber only works with the most up to date information to make up for subscribing to the publisher late
		- The alternative is **VOLATILE** where the subscriber only receives messages published after it connects
		- **Example**
			- With **transient local**:
				- If the subscriber joins immediately after message 3 is published, it will still get message 3 (the latest message in the buffer at the time it connects) and continue processing from there.
			- With **volatile**:
				- If the subscriber joins after message 3 has already been published, it will start with the _next_ message published (message 4 in this case) and won't have access to message 3 or any prior messages.

This distinction is key: **transient local** lets the subscriber "catch up" to the latest available message, while **volatile** only provides messages published _after_ the subscriber connects.
	- **history=QoSHistoryPolicy.KEEP_LAST**
		- **KEEP_LAST** which is represented as 1 in the YAML file means only the last few messages in the queue up to the specified depth will be kept
			- If new messages arrive and the buffer is full, the oldest messages are dropped to make room for the latest ones
			- This is commonly used when only most recent data is relevant like real-time sensor data feeds
		- **KEEP_ALL** represented as 2 in the YAML file retains all messages in the queue without discarding any until the subscriber processes them
			- Useful if you want to ensure no messages are missed but requires more memory and can lead to delays
			- Its good for logging, debugging, or cases where every single message matters 
	- **depth = 5**
		- Specifies the size of the message queue (how many messages to keep in memory if the arrive faster than they are processed)
		- A depth of 5 means up to 5 messages will be queued before older messages start to get dropped
```python
QoSProfile(reliability=QoSReliabilityPolicy.BEST_EFFORT, durability=QoSDurabilityPolicy.TRANSIENT_LOCAL, depth=5)
```

### **Solution**
- The error we got was because the node was expecting different communication parameters for the QoS profile for reliability, and depth
	- We initially had a reliability of RELIABLE and a depth of 10 even though the publisher was publishing with a depth of 5 and BEST_EFFORT
	- Bellow is the code with the correct parameters
### **Notes**
- **History**
	- 1 = KEEP_LAST
	- 2 = KEEP_ALL
- **Reliability**
	- 1 = BEST_EFFORT
	- 2 = RELIABLE
- **Durability**
	- 1 = VOLATILE
	- 2 = TRANSIENT_LOCAL
- **It's important for the communication parameters of the publisher to match the communication parameters of the subscriber**
- **Buffer is only available for certain QoS settings. Volatile durability for example will not have a buffer because it publishes messages as its received. This means the subscribers must process messages faster than published other wise messages will be lost**
- **Buffer Enabled**: `TRANSIENT_LOCAL` durability, `KEEP_LAST` with queue depth > 1 `KEEP_ALL`
- **Buffer Disabled**: `VOLATILE` durability, `KEEP_ALL` with `VOLATILE` durability 

```python
 self.subscription = self.create_subscription(
            Image if not self.compressed else CompressedImage,
            self.camera_topic,
            self.image_callback,
            qos_profile=QoSProfile(
                reliability=QoSReliabilityPolicy.BEST_EFFORT,
                history=QoSHistoryPolicy.KEEP_LAST,
                depth=5,
            ),

        )
```

### Combinations and Use Cases of different QoS profiles
![[Pasted image 20241112133353.png]]


### Warning that message queue for the playback player is starved which resulted in messages being delayed

```bash
[WARN] [1731481565.041895234] [rosbag2_player]: Message queue starved. Messages will be delayed. Consider increasing the --read-ahead-queue-size option.
```

- **What does this mean**
	- This means that the buffer in which messages are sent to by the publisher are being processed faster by the subscriber than the publisher is publishing them 
	- This results in the message queue to be exhausted which results in the subscriber not having any messages to publish and often results in choppy playback of the videos 
### **Solution**
- The solution to this is to increase the read ahead queue size which gives the rosbag2_player more capacity to pre-load messages, which can help maintain a smooth playback rate
- Below is the command to increase the read ahead queue size

```bash
./watod run data_stream ros2 bag play ./nuscenes/NuScenes-v1.0-mini-scene-0655/NuScenes-v1.0-mini-scene-0655_0.mcap --read-ahead-queue-size 1000
```

### Error using object detection with compressed image messages rather than regular sensor images 

- We ran into an issue where object detection was attempting to get the width and height from the compressed image message (sensor_msgs/CompressedImage.msg) however it wasn't able to because the compressed image message doesn't have width and height attribute like the regular image message does sensor_msgs/Image.msg
- The issue with the original function was that we were attempting to set the width to self.orig_image_width right away instead of first checking to see if the images was compressed first
	- If we attempt to pull the width attribute right away we are going to get an error because compressed image messages do not have this attribute
- In the changes we made we checked to see if the image is compressed first before proceeding with any other processing steps

### **Solution**
- We needed a way to get the width and height from compressed image messages as they do not have a width and height attribute
- We do this by decoding the image using a cv2 function:
```python
 # Check if the image is compressed or not
if self.compressed:
	# Decode the compressed image
	np_arr = np.frombuffer(msg.data, np.uint8)
	cv_image = cv2.imdecode(np_arr, cv2.IMREAD_COLOR)
	# Set original image dimensions if not already set
	if self.orig_image_width is None:
		self.orig_image_height, self.orig_image_width = cv_image.shape[:2]
```


> [!Question] What is a compressed image
> - A compressed image is an image file file that has been reduced in file size by using an image compression algorithm (lossy or lossless)
> - The goal of compression is to make the file smaller to save storage space and reduce bandwidth when transmitting the image which is often at the cost of quality or processing time
> - Lossy compression
> 	- Common formats: JPEG, WebP
> 	- Reduces file size significantly by discarding some image data, which may result in a slight or noticeable quality loss
> 	- This is often used for phots or images where perfect accuracy isn't crucial
> - Lossless Compression
> 	- Common formats: PNG, GIF
> 	- Reduces file size without losing any data or quality, preserving the original image exactly
> 	- Commonly used for images that require transparency or need to retain high quality


> [!Question] Why Use Compressed Images
> - Reduced storage: compressed images take up less space on disk
> - Faster Transmission: smaller files are faster to send over networks, which is crucial for web and mobile applications
> - Better performance: For some applications, reduced file size can improve performance by requiring less memory and processing power
> - sensor_msgs/CompressedImage is a compressed ROS message type which is specifically designed to handle images that are compressed to save bandwidth when transmitting over ROS topics
> 	- The format is usually in JPEG (lossy compression) or PNG (lossless compression)


> [!Question] Why not Use PNG images always instead of sensor_msgs/Image
> - PNG files are typically used for static images where files storage or transmission needs are balance with quality
> - sensor_msgs/Image is preferred in ROS for real-time streaming and processing of image data as it avoids the computational cost of encoding and decoding
> 	- The makes it especially useful for high speed and frequency data in robotics applications where quick processing is essential


> [!NOTE] My understanding
> - Nuscenes sends compressed images because it is assumed you are going to use it to visualize using software like Rviz or foxglove
> - Because we are sending image data over a network it is better to use a compressed format like JPEG or PNG because smaller files (compressed images) are faster to send over networks which is crucial for web and mobile applications



> [!Question] What does it mean to decode an image
> - Decoding an image means to convert the compressed image data (JPEG or PNG) back an uncompressed image (raw format) that can be easily accessed and processed in memory, typically as an array of pixel values
> - Decoding is necessary because most image processing tasks require direct access to the pixel data 
> 	- Compressed images like JPEG or PNG store pixel data in a compressed, encoded format that isn't directly usable for operations like filtering, color analysis, or machine learning inference
> - When an image is compressed only essential data is retained and redundancies are reduced resulting in a smaller file
> 	- Color and intensity
> 	- Sharp edges and texture
> 	- High frequency (rapid changes in color or brightness) vs low frequency (smooth, gradual color transitions like sky or walls
> - Compressed images are in a binary compressed format and decoding converts this binary data back to a structured accessible format like a 2D or 3D array of pixels

```python 
compressed_data = b'\xff\xd8\xff\xe0\x00\x10JFIF\x00\x01\x01\x01\x00H\x00H\x00\x00\xff\xdb\x00C\x00...'

# After conversion
array([[[123, 117, 114], [130, 121, 115], ..., [255, 254, 253]],
       [[125, 120, 118], [135, 125, 120], ..., [254, 253, 252]],
       ...,
       [[130, 125, 122], [128, 123, 120], ..., [253, 252, 251]]], dtype=uint8)

```

### Faced an Error where recorded ros bag wouldn't play bag when running the ros2 bag play command

- We ran into multiple issues trying to create this ros bag
	- File permission issues
	- Corrupted file issues
	- Transferring file issues
- **Corrupt files**
	- We got this issue because we attempted to play the ros bag before it completed downloading 
	- database disk image is malformed
	- To fix this we just had to record the ros bag again and upload it to the remote machine in a more efficient way which is shown here: [[Copying Folders or Files from Local Machine to Remote Machine]]
	- The error we got is as shown below:
```bash
[ERROR] [1731531067.656791330] [rosbag2_storage]: Could not open './rosbags2/camera_object_detection/detections/detections_0.db3' with 'sqlite3'. Error: Failed to setup storage. Error: Error when processing SQL statement. SQLite error (11): database disk image is malformed [ERROR] [1731531067.656868460] [rosbag2_storage]: Could not load/open plugin with storage id 'sqlite3' No storage could be initialized from the inputs.
```

- **File permission issues**
	- This was the bigger issue we faced and took a long time to figure out
	- When you first upload a ros bag to your desired folder the permissions are tailored only for you as the user who uploaded it and not anyone else who wants to use
		- This means that containers do not have access to run the ros bag you recorded
- **Solution**
	- What we had to first was to check the file permission by using the command below:
```bash
ls -l ./rosbags2/camera_object_detection/2d_detections/2d_detections_0.db3
# or for general use case
ls -l <full path to file> 
```

- At first we had -rw-r----- 1 kishoreyogaraj kishoreyogaraj 1874399232 when executing the command 
- This means that the owner kishoreyogaraj had read and write permissions (rw-)
- The group kishoreyogaraj has read only permission (r--)
- Others have no permission
- We need the group and other to have read permissions other wise the db3 file can not be read in order for it to be played
	- Instead of the permissions we had which are (-rw-r----) we needed (-rw-r--r--)
- **To change this we used the command:**
```bash
chmod 644 /mnt/wato-drive2/rosbags2/camera_object_detection/2d_detections/2d_detections_0.db3
```

- After this the permissions were set correctly


> [!question] What is group when setting permissions? What are the different permissions available?
> - Owner
> 	- The user who created the file or directory. They have full control over it by default unless permissions are changed
> - Group
> 	- A set of users who are assigned to a specific group. The group permission defines what actions the members of this group can perform on the file. Multiple users can be part of the same group, and this can be useful in collaborative environments where multiple people need shared access to specific files
> - Others
> 	- Anyone who is not the file's owner or a member of the group. This includes all other users on the systems
> - Read : Grants the ability to read or view the contents of a file. For directories, it allows the user to list the contents.
> - Write: Grants the ability to modify or delete the contents of a file. For directories, it allows the user to create, delete, or rename files within the directory.
> - Execute: Grants the ability to execute a file as a program or script. For directories, it allows the user to access or enter the directory (necessary for navigating into the directory).


### Numeric Permission Codes and Assignments
- Permissions are represented by numbers as follows
- **Read (r)** = 4
- **Write (w)** = 2
- **Execute (x)** = 1

By adding these numbers, you can set various combinations of permissions. For example:
- `7` (4+2+1) = Read, Write, and Execute (`rwx`)
- `6` (4+2) = Read and Write (`rw-`)
- `5` (4+1) = Read and Execute (`r-x`)
- `4` = Read-only (`r--`)

### Setting Permissions with `chmod`
Permissions are assigned in sets of three for **owner**, **group**, and **others**. For example:

- `chmod 755 filename` means:
    - **Owner**: Read, write, and execute (`7 = rwx`)
    - **Group**: Read and execute (`5 = r-x`)
    - **Others**: Read and execute (`5 = r-x`)
### Examples of Common Permission Settings
- **644**: Owner can read and write; group and others can read only (`rw-r--r--`)
- **755**: Owner can read, write, and execute; group and others can read and execute (`rwxr-xr-x`)
- **700**: Owner can read, write, and execute; group and others have no permissions (`rwx------`)
- **777**: Everyone has read, write, and execute permissions (`rwxrwxrwx`)

### Error with installing scipy package with docker and with additional system dependencies (legacy-install-failure)

- Legacy install failure error typically appears when you're trying to install a python package that doesn't have a pre-built binary (wheel) for the operating system or Python version
	- This often happens with packages that require compilations from source especially on systems that lack the necessary build tools or dependencies
- Ran into an error where I was trying to build a docker container with scipy as one of the packages but I kept getting an error that scipy couldn't build
- The first reason it couldn't build was because scipy relies on other system level packages in order for it be built 

> [!Question] Why does scipy rely on other dependencies to be installed first?
> - Scipy is a library built on top of lower level libraries that handle specific computational tasks
> - Scipy performs heavy numerical calculations (linear algebra, optimizations, and Fourier transformations) that benefit from optimized, compiled code
> 	- Libraries like BLAS (Basic Linear Algebra Subprograms) and LAPACK provide routines optimized for high performance on CPUs, speeding up operations on large datasets
> - The dependencies allow scipy to run a lot faster than it normally would


### **Solution**
- The first solution was to ensure that the correct dependencies were being installed in the docker file as shown below:
	- build-essential
	- gcc
	- gfortran
	- libopenblas-dev
	- liblapack-dev

```docker
# Install pip
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    ffmpeg libsm6 libxext6 wget \
    build-essential \
    gcc \
    gfortran \
    libopenblas-dev \
    liblapack-dev
```

### **Second Solution**
- Another weird error I got was that despite having the right dependencies scipy wouldn't install from the docker file so I had to run a separate command in the docker file for it as shown below:
```docker
# Install pip

RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    ffmpeg libsm6 libxext6 wget \
    build-essential \
    gcc \
    gfortran \
    libopenblas-dev \
    liblapack-dev

  
# Install scipy separately
RUN python3 -m pip install scipy

  
# Install python packages
COPY src/perception/tracking/requirements.txt requirements.txt
RUN python3 -m pip install -r requirements.txt
RUN rm requirements.txt
```

- The reason this fix worked is because by installing scipy separately we ensured:
	- Dependencies were installed first
		- When scipy was included in the requirements.txt file the installation process for all Python packages attempted to run as a single step
		- This means that if any of the scipy dependencies were not yet installed at that time, its build process would fail which leads to the **legacy-install-failure**
		- Installing python packages via pip install -r requirements.txt happens in Python's virtual environment 
		- We need scipy to be installed at the system level so that docker will revaluate to ensure that all dependencies for that package are installed before continuing
	- Simply because we are attempting to install a system dependent package because it needs to be built by source as its not a wheel file (a pre-compiled binary distribution of the package that are available if a compatible file exists between your platform and python version) we must install it as its own command so that system level dependencies are all evaluated carefully instead of bulk installing