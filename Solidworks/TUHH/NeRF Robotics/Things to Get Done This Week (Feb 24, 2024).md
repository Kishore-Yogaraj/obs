- Need to move all nodes into one docker container so that it's easier to maintain
	- The path planning node and the segmentation node don't have a lot of dependencies so it would be easier to include everything in one container rather than have a container for each node
	- This would also prevent the networking issue we were getting before
	- Also need to find out how to allow foxglove to receive data from a container
		- This should be as simple as exposing the port when starting the container
- **Need to use Isaac Sim as a dataset to train the NeRF model**
	- Big thing to note here is the scaling
	- When we create the NeRF model of the stone hedge, we don't know how far anything is or what the scale is currently
	- For example if we have a coordinate at (1,0,0) we don't know if that 1 is 1 meter, 1 cm, etc.
	- How do we determine a scale when training the NeRF
		- A better questions would be how do we ensure colmap sets coordinates with relation to the real world environment?
- After we get our new NeRF model then we need to write our MPC with photometric loss
	- We should study the MPC code in NeRF navigation and then integrate it into a ROS2 node
- You can also look into Rosdeps for easier containerization


Right now I'm trying to figure out how to get the node to display on foxglove and I'm dealing with this weird error where the terminal disappears in the container every time I input a command

After I get visuals on foxglove and I stop the container command error, I'll try to create an occupancy grid with the custom environment in foxglove. 
- I fixed the issue by doing docker system prune and docker volume prune

Make sure to publish a transform along with the nerf
```bash
ros2 run tf2_ros static_transform_publisher 0 0 0 0 0 0 map base
```


### Problems We Are Running Into
The problem I have right now is that the NeRF model we get from training isn't the greatest model. The 3D occupancy grid will only be as good as the trained model itself. I have me doubts that the NeRF model actually wasn't trained right in the first place and is why we're seeing the results we get. 

I think that the iPhone camera is way too complex of a model inorder to train a NeRF model with it and is why we're actually not able to see results that are nearly as good as the one in Blender. I honestly hate how pessimistic Mustafa is and his attitude is honestly terrible. I don't understand how you can possibly take credit for the work of others, be so incredibly proud of minimal work, and completely give up the moment you get a problem you can't solve. But as soon as I figure it out you hop on the bandwagon which makes no sense to me.

Anyways, I'm going to look more into this issue and see the best approach we can take to building a better NeRF model, because surely something is wrong with the way we are training are model to get such a significant difference in results.

Here are the steps we're going to take:
- First lets find a way to add more space to my disk so that I can actually install the docker container
	- I could also just use my personal laptop to train the model and see what happens
- Then we need to find out how to train the NeRF model properly
	- The angle of the pictures may be wrong
	- The camera model we use might have been too complex
	- **These are the possibilities that I can list off the top of my head for now**
- I realized that I need to do a lot of testing in order to understand how the nerf navigation paper actually does training. I have feeling that it might be too configured for the blender environment which means we will have to make some tweaks. There are so many parameters involved which leads me to believe that we aren't setting our parameters correctly. I think the type of camera we use may be a problem as well since iPhone cameras are more complex. Here are the things that I think need to be tested and understood:
	- Test training with nerf navigation 
		- Use blender scenes
		- Use custom images
			- I believe this is where the nerf navigation paper is tailored to the blender environments too much -> go to next point
		- Record the differences and see which performs better
		- Also refer to the video we saw on youtube: https://www.youtube.com/watch?v=VsFHX8IgX1o
			- You should review this video I'm not exactly sure what it does
	- Test torch-ngp on custom images to see if there are any performance differences between using nerf navigation to train and using torch ngp to train
		- If there is a clear difference then you know that something is wrong with the training for nerf navigation

### Things that got done today
One of the problems that was figured out today is that the model has to be queried the same way it was trained. We used different parameters to train the model, so all those parameters were added to the model config file to make sure that it was queried properly. 

With this fix it looked like we got a much more accurate and detailed density grid and we were able to get a 2D occupancy grid from it as well which is good.
- The 2D occupancy grid had these white grid lines on it for some reason which can really affect the path planner
- The ply model of the nerf is still rotated so we need a way to make sure it doesn't rotate after being trained

### Moving forward
Tomorrow, will still be a training day but I should focus on finding how to ensure the model doesn't rotate during training. I think this might be a blender specific issue, so it would definitely help to see how blender images are processed and how custom images are processed during training. There has to be a reason why only custom environments rotate and why blender environments don't rotate.
- **Find out how blender images are processed and then find out how custom envs are processed** - We're doing this to see why the custom environment's come out rotated
- If we figure this out then we make pretty good progress. It would be awesome to have the custom environment come out without any rotations applied to it

I'm also strongly convinced that we're training the NeRF model incorrectly. Although we're able to get a pretty good render of the image itself and the NeRF model, I'm really struggling to understand why the mesh of the NeRF model comes out so poorly. **I don't know if the mesh comes out poorly in general, we'll have to check and see the mesh of the blender images to see if there's any difference between the two.**
- **First thing tomorrow, let's compare the mesh of the custom environment and the blender environment and see the quality of the two**
- **Then begin training a new custom environment. The new one should be trained with videos and pictures to see which one yields a better result that can be used**

I feel that an iPhone camera is far too complex and that the camera parameters are constantly changing automatically in order to get the highest quality pictures. This might be good for the NeRF render itself but it could be a reason why the mesh of model comes out so poorly. COLMAP could be terrible at estimating the intrinsics and we might have to input them manually for a better render of the mesh model. 

**We should aim to complete all training with the nerf navigation training because once training completes, it might output the correct files that we want and need to use. They may have also included new parameters that build on top of the training done by torch-ngp. You should full understand how nerf navigation trains vs how torch ngp trains and see what works better. There is a possibility that nerf navigation actually performs better and is better suited for a blender environment rather than a custom environment. With that said, if we are planning to take a testing of models approach this is what we should be comparing:**
- Nerf navigation training on blender images
- Nerf navigation training on custom images
- Torch ngp training on blender images
- Torch ngp training on custom images
- **We should be comparing the results of the mesh's that are produced by all 4 training methods to see which ones produce the best mesh for custom images and why**
	- Before we do that, let's dive into how the training works for nerf navigation

### My Actual Plan for Tomorrow LOL
- Let's clone the nerf navigation repo
- Record a dataset with proper technique (spherical form)
- Run colmap with feature extraction
- Check the sparse clouds
	- Check this before investing time into training the NeRF model
	- If this comes out well, then the mesh should come out nicely as well
	- If we notice errors here some things we can do are:
		- Improve feature detection
			- Add textures to featureless areas (place more objects)
			- Ensure better lighting to create more visible features
			- Try different colmap feature extraction settings
		- Improve camera coverage
			- Capture more images focusing on problematic areas
			- Ensure multiple views of each surface from different angels
		- Adjust COLMAP parameters
			- Use --estimate_affine_shape for better feature matching
			- Lower the matching threshold for difficult scenes
- Then run training


   python main_nerf.py data/my_scene --workspace my_scene_nerf -O --bound 4.0 --scale 0.5 --dt_gamma 0.02
   python main_nerf.py data/my_scene --workspace my_scene_nerf -O --bound 4.0 --scale 0.5 --iters 30000
  python main_nerf.py data/my_scene --workspace my_scene_nerf -O --bound 4.0 --scale 0.5 --density_thresh 5.0