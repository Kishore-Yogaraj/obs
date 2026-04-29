I was able to get COLMAP working on our gazebo environment. It looks like the main issue was ensuring the gazebo environment looked realistic enough to work with.

I thought gazebo didn't have a good physics engine but it actually has a pretty good one with great shadows so that's what I ended up using. The COLMAP output came out really nice.

I have a very ambitious goal to get localization done today. Things are looking good so far. I believe the biggest issue was the the transforms.json I create just wasn't accurate at all with the camera poses. **Inaccurate camera poses are a huge reason why nerfs may result in noisy values.**

So now our big task is localization and just scaling in general. The good thing about using the simulation is that we know the radius we used to record the dataset. We used a radius of 1 meter around the entire environment. 

The whole purpose of COLMAP in our workflow is to be able to get camera poses from the real world because in the real world we have no idea what pose a camera might be at. COLMAP helps us in this case by taking all the camera poses and normalizing it to a unit of 4.0 units. **This unit is unknown, it's simply 4.0 units**

These are the units and poses that are used by the NeRF during training. So we have **NeRF/COLMAP coordinates** and we have **real world coordinates.**

**The first step in localizing is being able to query the nerf from a pose.** Now this pose is from the real world coordinate system. In the real world we might be at one position but in the NeRF coordinate system this is a different position.

So how do we take a coordinate at the real word coordinate and query the nerf at that coordinate? If we input the real world coordinate we will get some random query but if we are able to transform the real world coordinate to the nerf coordinate system we would get an accurate transformation of the real world coordinate camera pose to the nerf coordinate system.

We fucking did it bro. Holy shit. Here are all the steps:
```python
# Take around 300 images in a dome shape
# Run it through colmap using:
python3 scripts/colmap2nerf.py --images data/my_scene/images --run_colmap --estimate_affine_shape

# Then run training
python3 main_nerf.py data/my_scene --workspae colmap_ws -O --bound 4.0 --scale 1.0 --dt_gamma 0.0 --iters 40000 --density_thresh 10.0
```

Removing the lock on a folder:
```python
sudo chown -R user:user <path to folder>
```

Okay so things are beautiful right now. Make sure you **change the domain of the grid coordinates accordingly** in the occupancy grid node so that the entire scene is represented as the voxel grid in foxglove. 

Okay now we can actually move on to localization. I'm going to make python script to do this and then we can work on turning this into a ros2 node.

First let's understand what kind of transformations need to be applied in order to be able to do this.

### Applying transformations for querying the nerf from real world coordinates

When we set the scale to 1 when running training, we are telling the nerf model to not apply any additional scaling to the poses loaded from the transforms.json. This means that all camera poses will be true to their coordinates that were set during COLMAP. In the real world the camera poses are all in a radius of 1 meter but in COLMAP and nerf coordinates all poses have a unit of 4.0 units.

What we have right now is that the nerf model is trained to understand that **1 gazebo meter corresponds approximately to 4 NeRF units.**

Now the nerf model operates entirely in these **"NeRF units"**. The robot will be estimated its pose in gazebo's meter based coordinate system. Now if we want to use the NeRF map for localization (by rendering expected views) we can't directly use the gazebo pose to query the NeRF. **We need to convert the gazebo pose into the NeRF coordinate system first**

So how do we find this transformation?

We need to find the scale and translation between the gazebo world coordinate system and the NeRF coordinate system establish by the colmap script.

As we discussed previously, the target average camera distance is 4.0. If the input data collection radius was 1.0 m, the scale factor would approximately be 4.0.

**Scaling**
```python
NeRF_unit = S * gazebo_meters ->
NeRF_units = 4.0 * gazebo_meters

gazebo_meters = (1/S) * NeRF_units
gazebo_meters = (1/S) * NeRF 
```

**Translation**
The colmap script calculated a center point based on the ray intersections and effectively set this point as the origin in the NeRF coordinate system. We need to know the coordinates of this NeRF origin **within the original Gazebo coordinate system**. We can call this `O_nerf_in_gz`

Unfortunately, we don't actually know what this offset value is.

Luckily we have some ways of finding what this translation is.

### Method 1 - Unknown Real World Camera Poses
In this method, we use known points and landmarks. 

You want to identify 3-4 distinct points in your environment whose exact world coordinates are known. These could be corners of objects, markers, etc.

You would then find the corresponding coordinates of these points within the trained NeRF model which will be a very involved step:

- You can render the NeRF from different viewpoints and manually identify the landmarks
- If you used colmap GUI you might be able to identify the sparse points corresponding to your landmarks and get their coordinates before the colmap scaling/translation but this requires you to relate colmap initial coords to gazebo's. An easier way is to query the final nerf density field.

With pairs of corresponding points `P_gz_i` and `P_nerf_i` you can solve for the transformation by applying:

`P_nerf_i = S * (P_gz_i - O_nerf_in_gz)`

This is a standard problem called **Absolute Orientation** or **Point Set Registration**. You can solve for the scale S and the translation using **least squares**. The scale should come out close to 4.0

All you would do for this method is set something as an origin in the real world. And then determine where the landmark is in units of meters and then attempt to query the nerf until you find it. Not ideal but it's one of the difficulties of working with NeRFs for navigation.

### Method 2 - Known Camera Poses (Recommended for Our Use Case)
If you know the precise Gazebo world pose (`Pose_gz_i`) for one or more of the camera images used to train NeRF and you have the corresponding NeRF pose (`Pose_nerf_i`) in transforms.json.

We can compare these poses. The translation parts are related by:

`T_nerf_i = S * (T_gz_i - O_nerf_in_gz)`

The rotation parts should ideally be the same. Using multiple pose pairs can help solve for S and `O_nerf_in_gz` where S should be close to 4.0.


### Applying The Transformation
Once we determine S which should be approximately 0 and `O_nerf_in_gz` we can begin working on the applying these transformations which are as follows:

**Gazebo Pose to NeRF Pose**
Translation:
`P_intermediate = P_gz - O_nerf_in_gz`
Scale:
`P_nerf = S * P_intermediate`
Keep rotation the same assuming axis alignment

**NeRF Pose to Gazebo Pose**
Un scale NeRF position:
`P_intermediate = P_nerf/S`
Un translation NeRF position:
`P_gz = P_intermediate + O_nerf_in_gz`
Keep rotation the same

### Steps
We now know how the transformation (scaling + translation) should work when we query a point using a real world coordinate and then viewing in the nerf environment.

Before we do this we have to **calculate the scaling factor and find the coordinate of the NeRF origin in the Gazebo environment**. These two values will help us with our transformations and queries. 

Since we are going with method 2 what we need right now are the camera poses in the NeRF coordinate system which can be found using the **transforms.json** and we need the corresponding coordinate in the real world which we have to find by trial and error essentially. **How many pairs of poses do we have to use**

Then we need to write a script that will calculate the scaling factor and calculate what the NeRF coordinate origin is in the Gazebo environment.

Then we can use these two values to query any point in the NeRF using the real world coordinates.

### Capturing Pairs of Poses
We are going to find 4 poses to compare with.

These 4 images should be spread out along the environment to get the most robust average for the scaling and translation. 

The goal is to get 4 different poses and average out the results we get for the scale factor and `O_nerf_in_gz`

There are different conventions for extracting Euler angles from rotation matrices. Values provided use the Z-Y-X convention (Yaw around Z axis, Pitch around Y axis, Roll around X axis)

### Accounting for Rotation

COLMAP has an arbitrary coordinate system when it established (origin and axis orientation. This depends on which images were processed first and how the reconstruction was initialized. It generally will not align perfectly with Gazebo's world axes (X forward, Y left, Z up).

NeRF models typically expect a coordinate system where the Z-axis points up relative to the scene.

The colmap script has an alignment step to address this.

It reads the initial COLMAP poses, it calculates the average "up" direction of all camera poses based on the COLMAP poses, then it calculates a rotation matrix in the script that will rotate this average "up" vector to align perfectly with the z-axis. **It applies this same rotation matrix R to all the camera poses before saving them to the transforms.json file**

This means that the orientation will not be the same for our gazebo camera pose and NeRF camera pose when collecting image pairs.

This means that our implication for using method 2 involves the alignment rotation as well.

Rotation:
`R_nerf = R_align_up * R_gz`

Translation:
`t_nerf = S * R_align_up * (t_gz - O_nerf_in_gz)`

Our current plan still works but we need to account for this rotation because we are no longer just solving for S and translation `O_nerf_in_gz`.

We need to find the **rigid transformation** (Rotation `R_align_up`) + Translation `O_nerf_in_gz` plus a scale that best aligns the set of gazebo poses with the set of nerf poses

### How do we find R, S and O:
This is the standard problem known as **Absolute Orientation** or **Point Set Registration** (finding a similarity transformation). Given our pair of poses we need to:

Extract the translation vectors `t_gz_i` and `t_nerf_i` for each pair `i`

Align translations by using an algorithm like Horn's method or methods based on SVD. This algorithm will take the set of points `{t_gz_i}` and `{t_nerf_i}` and find the optimal scale S, rotation `R_align_up` and translation `t_offset` such that:
`t_nerf_i - S * R_align_up * t_gz_i + t_offset`

Then we solve for `O_nerf_in_gz` where the translation found in the previous step can be used as such:
`O_nerf_in_gz = -(1/S) * R_align_up.inv() * t_offset`

As a sanity check the rotation part `R_align_up` found from aligning the translation vectors should also align the rotation components of your poses so:
`R_nerf_i = R_align_up * R_gz_i`

Our steps are to still gather 4 pairs of poses but our subsequent calculations should need to solve for rotation R, scale S and the offset O using an appropriate alignment algorithm. 

**Pair 1 - image 1 (ngp_ep0142_002_rgb.png)**
Gazebo:
X = 1.500
Y = -0.4500
Z = 0.0500
Roll = 0.000
Pitch = 0.000
Yaw = 3.1400
Nerf:
X = 2.24655
Y = 3.45463
Z = 0.05240
Roll = -1.550
Pitch = 0.5230
Yaw = 3.1200

**Pair 2 - image 97 (ngp_ep0142_0041_rgb.png)**
Gazebo:
X = 0.4463
Y = -0.2849
Z = 1.0348
Roll = 0.000
Pitch = 1.4222
Yaw = -1.2566
Nerf:
X = -0.4891
Y = 0.2798
Z = 3.8666
Roll = -0.0221
Pitch = -0.1559880
Yaw = -1.7823

**Pair 3 - image 120 (ngp_ep0142_0004_rgb.png)**
Gazebo:
X = -0.1691
Y = 0.2931
Z = 0.0500
Roll = 0.000
Pitch = 0.000
Yaw = -0.8378
Nerf:
X = -4.07101
Y = -0.64845
Z = -0.05606
Roll = 1.6303
Pitch = -1.3607
Yaw = 0.05137

### Calculating the Transformation Parameter (scale S, Rotation R_align_up and offset O_nerf_in_gz)

Okay in calculating the transformation parameters we are running into problems. The positions seem to be matching correctly except the one problem is that COLMAP applies rotations to all the coordinates.

When COLMAP determines its coordinates it is in the format of **X right, Y down and Z forward** but it performs conversions to the transforms.json before uploading it to the coordinate system that the NeRF expects which is **X right, Y up and Z backward**

Something new I learned is that there are multiple transformations that I have to worry about and not just one. First we have to find out how to transform the gazebo coordinate to the colmap coordinate system. After this, we need to apply all the transformations that colmap applies to its own camera poses to get the nerf coordinate system - **This is the easy part because colmap already has all the code.**

The hard part is converting from gazebo to colmap. This is useful to know because we are trying to convert from gazebo to colmap to nerf all in one step. When in reality, we should make it simple for ourselves and convert from gazebo to colmap first using colmap poses and not the nerf poses.

**Apparently, COLMAP outputs an images.txt which actually holds all the COLMAP poses before being transformed for the nerf.**

This images.txt holds the **world to camera** coordinates. The COLMAP script actually converts the these poses to **camera to world** before applying transformations for the nerf. Camera to world are the coordinates that represent the pose of the center of the camera in the COLMAP coordinate system. 

**We need to get these coordinates to calculate the transformation between gazebo and COLMAP**

What exactly are we computing?
We are computing exactly what we were computing before just with the correct poses now. These poses are the COLMAP c2w poses. We will still be solving for the point set registration problem and calculating the **rotation and translation**

##### In the future lets have the colmap script output the c2w values

We got all of camera to world transformation matrices for each camera pose in the COLMAP coordinate system. Now we we need to find the **rigid transformation** between gazebo and COLMAP which means to find the **rotation and translation** using potentially the same method we used yesterday.

I was able to get a transformation matrix that transforms gazebo points to colmap coordinates. The key here was to use a similarity transformation like umeyama's algorithm which will compute a scale factor between the colmap coordinate system as well as the gazebo coordinate system. In our case it found a scale of 4.37 which means that COLMAP's arbitrary scale is roughly 4.37 times that of gazebo's metric scale. 

**The flow for transformations is to apply rotation to the gazebo coordinates and then you multiply the gazebo coordinates by this scale factor and then you add the translation and then you will get COLMAP's coordinate.** 

Our next step would be to apply the transformations in the colmap script to get the nerf coordinate. And then render the nerf and compare our captured image with our nerf image.


These are the positive directions

**Gazebo coordinate system:**
X points forward
Y points left
Z points up
Extrinsic because rotations are applied around the world axis and not a central axis

**COLMAP coordinate system (suspected)**
X points right
Y points down
Z points forward

**NeRF Coordinate System**
X right
Y up
Z backward

We are so stuck right now. Here is the problem. Since we use colmap as the tool to determine our camera poses, and our real world is simulated in gazebo, we need a way to view the nerf rendered image using the gazebo poses. 

What we have to do first is find out how do we transform our gazebo poses to colmap poses. This is the hardest step right now because we have no idea how to do this. 

Here are the steps I'm going to take today:

First, I need to research how transformation/pose matrices work. My thought right now is that it might be more robust to compare transformation matrices rather than euler angles. What I do right now is I derive the euler angles from the transformation matrix and then use that for comparison.

And then in the script, I actually convert the euler angles into a rotation matrix again and this step is prone to error. 

When I derive the euler angles I also need to know the correct convention to use because if the colmap convention is different from the gazebo convention then we can run into a lot of errors.

I'm hoping that using a transformation matrix from the start would be the best approach.

Steps:
Find out if its easier to work with transformation matrices rather than using euler angles. 

Find out how to convert gazebo euler angles to transformation matrices

Determine our transformation matrices

Pray that our errors are low

**Okay so I found out that the euler angles used in gazebo aren't actually euler angles.** The gazebo gui uses euler angles but internally gazebo is using quaternions. This could be a huge reason why our rotations are off. The command to find the current camera position in the gazebo world coordinate frame is this:

```python
ign topic -e --topic /world/default/pose/info | grep -A12 'name: "camera"'
```

This means we have to compare the transformation matrix from quaternions of gazebo to the transformation matrix from the quaternions of colmap. One thing I noticed is that the convention gazebo uses for quaternions is: `x, y, z, w` and colmap uses `w, x, y, z` I wonder if this is a problem and I wonder what the w represents. 

Quaternions themselves are unitless mathematical entities that represent orientation with the 4 components not having physical units but they are derived from angle axis representations where the angle is in radians

To convert between different quaternion formats you simply reorder the scalar and vector values. 

I'm trying to figure out which part of the colmap script returns the poses of each camera in the world coordinate system and not the colmap camera coordinate system.

c2w in line 256 of the colmap script is the camera pose in the world coordinate frame however, a negative quaternion is used instead of a positive one (original one). This means that we aren't actually comparing the gazebo camera pose directly to the colmap camera pose. 

After printing out two different c2w json files, I noticed that the transformation matrix or each pose (one for negative quaternion and one for positive) doesn't really change anything. So we can keep using the script the way it is. 

In mathematics of quaternions, q and -q actually represent the same rotation. When you invert a transformation matrix built with either quaternions, you'll get the same final result because they encode the same rotation. 

Our next step is to take make a transformation matrix from the quaternion values and translation values in gazebo and use that for comparison with the transformation matrix from colmap. 

Here are our mappings
1.png = 0001
106.png = 0002
120.png = 0004

Here's the plan right now. Using transformation matrices instead of euler angles didn't work. So this leads me to believe its something wrong with the algorithm or I'm just overthinking this completely. 

I'm going to try to learn some things and apply it today. If we don't get things working today, I'm going to build a controller and call it a day. Here we go

First of all I need to understand how Colmap converts its coordinate system into a coordinate system that the nerf understands. 

Apparently its as simple as moving around some axes. This leads me to believe that I would simply have to move the axes from the gazebo coordinate system into the colmap coordinate system.

My steps today are to figure out how COLMAP actually converts its coordinates into the nerf coordinate system and then figure out what the up vector means after running colmap and how we can maybe use this. 

After figuring this out we are going to take a known pose from gazebo and a known pose from colmap and see if transforming that pose based on axes will work. We will then compare with all three axes and see. 

### How to transform from colmap to nerf