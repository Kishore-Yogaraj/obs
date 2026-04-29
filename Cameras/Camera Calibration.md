### What is Camera Calibration?
- **Formal Definition:** Camera calibration is the process of determining specific camera parameters in order to complete operations with specified performance measurements.
- A **process** to determine different parameters that can be used in various computer vision tasks
- A **camera** is a device that converts the 3D world into a 2D image
- The following **equation** can be used to represent the mathematics of a camera (3D world point to 2D point):
$$
  x = PX
$$
- x = 2D image point
- P = camera matrix (projection matrix)
- X = 3D world point
#### Vector Representation 
![[Pasted image 20240801211649.png#center|500]]

> [!NOTE] Understanding Point
> If we wanted to use computer vision to take an image of an object and estimate the height of it, we run into a problem where the computer vision algorithm will measure the height of the image in the 2D image plane and not in 3D (this is the correct and more accurate measurement). Camera calibration is crucial in this scenario. Knowing the intrinsic matrix and extrinsic matrix will allow for the conversion of 2D image measurements into real world measurements. This helps in determining the scale and perspective effects caused by the camera sensors, lens, etc. 

#### Intrinsic matrix
 - This matrix is a key component in the mathematical model of a camera, representing its internal parameters that affect how 3D point in the camera's coordinate system are projected onto a 2D image plane
- This matrix is essential for understanding how the camera converts 3D camera coordinates into image coordinates and is central to the process of camera calibration
- The intrinsic matrix which is often denoted as *K* is a 3 x 3 matrix which is represented as:
![[Pasted image 20240802011053.png#center|400]]
- fx and fy are focal lengths in terms of pixels along the x and y axes, respectively
- These values define the scale at which the camera projects 3D points onto the image plane
	- This refers to how the camera maps the size and position of objects in the real world onto the image captured by the camera which is primarily influenced by the focal length, which affects how much of the scene is captured and how large objects appear on the image sensor
- They are derived from the physical focal length of the camera lens and size of the camera's sensor pixels
- **Definition of physical focal length:** The physical focal length is the distance between the camera lens and the image sensor when the lens is focused on a distant object
- **Impact on Image:**
	- A **shorter focal length (zooming out)** results in a wider field of view (capturing more of the scene) but smaller image details (less magnification).
	- A **longer focal length (zooming in)** provides a narrower field of view (capturing less of the scene) but larger image details (more magnification).

> [!tip] Something to note
> - Zooming in and zooming out physically changes the distances between the lens and the image sensor which results in the change in focal length
> - Cameras have a fixed sensor resolution which is the width and height of every image captured in pixels
### Calculating Pixel Focal Length
The pixel focal length can be calculated using the following formulas:
$$
fx = F *W/w
$$
$$
fy = F *W/w
$$
Where:
- F is the physical focal length (in millimeters).
- W and H are the width and height of the sensor (in millimeters).
- w and h are the width and height of the image in pixels.

fx and fy are just a way of representing what the focal length is in terms of pixels which we can refer to as **"pixel focal length"**

##### Skew Coefficient
- S is the skew coefficient, which accounts for any non-orthogonality between the x and y pixel axes. In most modern cameras, the skew is negligible and the pixels axes are orthogonal making s usually zero
- X and Y pixel axes in a digital camera sensors are typically orthogonal however, due to manufacturing imperfections or specific design choices, or camera lens not being perfectly aligned with the sensor, the axes may not be perfectly perpendicular. This lack of orthogonality is what the skew coefficient accounts for 
- The skew coefficient quantifies the deviation from orthogonality between the pixel axes. It represents the shear transformation needed to correct this misalignment

##### Principal Point (Optical Center)
- Cx and Cy represent the coordinates of the principal point (or optical center) on the image sensors
- The principal point is the point on the image sensors where the optical axis of the lens intersects the image plane. Ideally, this is the center of the image, but due to manufacturing tolerances and lens alignment, it can be slightly off-center
- The optical axis is an imaginary line that passes through the center of the lens (or system of lenses) and is perpendicular to the lens surfaces
- Cx and Cy are the coordinates of the principal point which is the intersection of the optical axis with the image plane
- In an ideal scenario, the principal point should be at the geometric center of the image. However, due to lens alignment and manufacturing tolerances, it may be slightly offset
- The principal point is the "actual center" of the image and is what is used in projections and calculations
- The geometric center is simply the midpoint of the image's pixel dimensions
- It represents the physical center of the image based on the resolution but does not account for the lens's optical properties 

> [!tip] Something to note
> Camera calibration isn't always needed for your tasks. For example for basic object detection to detect different things in the frame like an apple or an orange there's no need for camera calibration. Camera calibration is useful when you need to do things like account for distortion so when working with a fish eye camera or attempting to back project which is projecting the 2d image point back to a 3d point which is useful in robotics (back projecting is when we start using extrinsic parameters and transforms to begin switching from frame to frame). For the behavior cloning project the car will be in a very controlled environment and following very simple behavior cloning


> [!question] How does camera calibration give us improved image quality for a task like behavior cloning?
> - Camera calibration can correct lens distortions (radial distortions - causes straight lines to appear curved and tangential distortion - occurs when lens is not perfectly aligned with the image sensor which leads to image skew)
> - By calibrating the camera, you ensure that the images fed to your model have consistent geometric properties, which can help the model learn more effectively
> - Calibration ensures that the perspective in the images is consistent, making it easier for the model to learn spatial relationships and react appropriately to changes in the scene


> [!question] How can we use calibration results in our code?
> - Use the checkerboard method to get our calibration results (intrinsic matrix and extrinsic matrix)
> - Use the intrinsic matrix and distortion coefficients to undistort each image before feeding it to the behavior cloning model
#### Example Workflow in Behavior Cloning
- **Image Capture:**
    - Capture images using the camera during the data collection phase.
- **Undistortion:**
    - Before feeding each image to your behavior cloning model, undistort the image using the calibration parameters.
- **Model Training:**
    - Use the undistorted images to train your model, ensuring that it learns from geometrically accurate and consistent data.
- **Deployment:**
    - During inference, continue to undistort incoming images before passing them to the model to ensure consistency between training and real-world operation.
#### Extrinsic Parameters
- **Formal Definition:** The extrinsic parameters of a camera describe its position and orientation relative to a fixed world coordinate system. These parameters are crucial for understanding how the camera is situated in space and how it views the scene from its perspective
	- My understanding of this definition is that the camera views points differently than how it looks in the real world
	- Its a different pov then what the actual pov should be
	- Think about someone on top of a car viewing an object vs someone directly in front of the car viewing that same object
	- We want to perceive objects in the way that they are represented in the world and not how they are represented in the camera because this isn't accurate
	- In order to do this we use the **extrinsic parameters** which we find during calibration
- When we perform calibration we end up with a rotation matrix (R) which is a 3 x 3 matrix that represents the orientation of the camera in the world coordinate system
- It describes how the camera's coordinate system is rotated relative to the world coordinate system
- We also get a translation vector (T) which is a 3 x 1 vector that represents the position of the camera's optical center in the world coordinate system
- It describes the shift or translation of the camera from the origin of the world coordinate system 

> [!question] How are we able to build an accurate world coordinate frame from a camera frame with just a known pattern like a checkerboard?
> - Since we provide the measurement for the checkerboard geometry (size of the squares in mm) every object detected will be in reference to this measurement since this measurement is what is seen in the "real world". Now all objects can be accurately sized in the 3d world after object detection
> - Typically one corner of the checkerboard is chosen as the origin (0,0,0) of the world coordinate system. This means that everything projected back onto the world frame from the camera frame will be relative to this origin 
> - The camera then captures images of the checkerboard from different angles. By detecting the corners of the squares in these images, we obtain the 2d image coordinates corresponding to the known 3D world points
> 	-  The reason we use multiple angles and positions is because we want to be able to capture distortions at all parts of the image sensor
> 	-  Different angles help in determining the camera's position and orientation relative to the word coordinate system for each image
> 	-  We basically need to expose the checker board to all parts of the image sensor so that we're able to capture all distortions, skews, etc.
> - The calibration algorithm uses the relationship between these 2D image coordinates (pixel coordinates) and the known 3D world points (points based on the origin and size of each square) to solve for the camera parameters (intrinsic and extrinsic). It aims to minimize the reprojection error, which is the difference between the observed 3d points and the projected 3D points
> 
#### Projection Matrix
- Projection Matrix P is a 3 x 4 matrix that combines both intrinsic and extrinsic parameters to project 3D points in the world coordinate system into 2d points in the image plane
- This can be identified using this equation:
![[Pasted image 20240802074447.png#center]]
- K is the intrinsic matrix containing the camera's focal length, principal point and skew coefficient
- {R|t} is the extrinsic matrix compose of the rotation matrix and the translation matrix

> [!tip] Something to note
> The rotation matrix and translation matrix is how the camera is oriented and positioned in reference to the origin in the world frame which is determined during calibration

> [!question] What is the difference between Maps 3D points to 2D image plane (projection matrix) and conver 3D points into 2d image coordinates (intrinsic matrix)
> - The intrinsic matrix converts 3D points from the camera's local coordinate system into 2D image coordinates
> - The projection matrix directly maps 3d world points to 2d image plane points incorporating the camera's position and orientation
> - This is essentially dependent on your use case and whether you want to use a 2 step process or a 1 step process

##### 1-Step Process
![[Pasted image 20240802075643.png#center|400]]

##### 2-Step Process
![[Pasted image 20240802075737.png#center|400]]
![[Pasted image 20240802075751.png#center|400]]
### In A Nutshell
#### Intrinsic Matrix
**Purpose:** The intrinsic matrix relates the camera's internal parameters, such as the focal length and principal point, to how the camera projects 3D points in the camera coordinate system to 2D image coordinates on the sensor.
**Function:** Converts 3D points in the camera's local coordinate system to 2D image points.
**Structure:** 
![[Pasted image 20240802082741.png|300]]
**Usage:** Primarily used when transforming 3D points in the camera coordinate frame to 2D image coordinates, without considering the camera’s position or orientation in the world.

#### Extrinsic Parameters
**Purpose:** Describe the camera's position and orientation in the world coordinate system.
**Function:** Transform 3D points from the world coordinate system to the camera coordinate system.
**Components:** 
- **Rotation Matrix (R):** Describes how the camera is oriented relative to the world frame.
- **Translation Vector (t):** Describes the camera's position in the world frame.
**Usage:** Used to convert 3D points from the world frame into the camera frame before they are projected into 2D using the intrinsic matrix.

#### Projection Matrix
**Purpose:** Combines both intrinsic and extrinsic parameters to provide a single transformation that maps 3D world coordinates directly to 2D image coordinates.
**Function:** Maps 3D points in the world coordinate system directly to 2D points on the image plane.
**Structure:** 
![[Pasted image 20240802083342.png]]
**Usage:** Used when you want to perform the entire transformation from 3D world points to 2D image points in one step, incorporating both the camera’s internal settings and its position/orientation.

#### Summary
**Intrinsic Matrix (K):** Handles the conversion of 3D points from the camera frame to the 2D image plane.
**Extrinsic Parameters ([R∣t]):** Transform 3D points from the world frame to the camera frame, accounting for the camera's pose.
**Projection Matrix (P):** Combines both intrinsic and extrinsic transformations to map 3D world coordinates directly to 2D image coordinates, allowing you to handle both the internal camera settings and its spatial positioning in one transformation


> [!NOTE] Final Thoughts
> - My final understanding of camera calibration is that in many basic pure computer vision applications the most you'll need calibration for is to find the distortion coefficients so that you have a more refined image that's processed through your models. The intrinsic matrix, extrinsic matrix and projection matrix is more useful in robotics where you want to take an object that was detected in 2d and represent back in the 3d real world. I'm not too concerned about other applications like augmented reality
> - So the only practical application of these matrices are to project the 2d detections back into the world coordinate system so that the objects are shown in the "real world" and not in the camera frame which is especially useful for tasks like world modelling or for picking up an object with a robot arm (need to know where object is after detection relevant to the robot itself)
> - From my understanding it looks like the purpose of the intrinsic and extrinsic matrices are to be able to create the projection matrix which can then be used for the points above (reprojection)