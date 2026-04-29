### Goal
The goal here is to try to get the coordinates of an object given by the camera to be represented in the world frame so the robot has real coordinates it can actually travel to

### Core Idea
We are trying to develop mapping. The affine transform handles:
- Translation (camera isn't centered over the robot origin)
- Rotation (Camera may be rotated relative to the robot)
- Scale (Pixels to mm)
- Shear (if the camera is slightly tilted)

![[Pasted image 20260322232447.png]]

a, b, c and d encode the rotation and scale and tx, ty encode the translation. We have 6 unknowns which means we need a minimum of 3 point correspondences in which each give you 2 equations. **In practice we aim for 6-9 points spread across the workspace for robustness**

### Calibration Process

#### Step 1
Place known points in the robot's workspace at positions you know in the robot frame. We can use the checkerboard pattern laid flat in the workspace where we have measured the grid spacing precisely using a ruler. We have to make sure to get the negative area as well so the the entire grid can be transformed even if we don't end up using it. An example of this is this:
![[Pasted image 20260322232854.png]]

#### Step 2
With the camera fixed in place we are going to capture an image of the grid and manually click on each of the known points to find the pixel coordinates at that point. Now we will have a set of pixel coordinates and their matching real world coordinates

#### Step 3
We can then use the **cv2.findHomography()** function to find the matrix we need. Once we have the matrix we need, all we do is apply the matrix to any new pixel detection as such
`pt_robot = M @ [u, v, 1]`

### Summary of Flow After Getting the Matrix
![[Pasted image 20260322233347.png]]

### Get 4 Aruco markers
![[Pasted image 20260323012600.png]]