### Monday
- Today we fixed a compilation error with the the code for nerf navigation in ros2
- Raymarching is a technique used to query the nerf faster with the use of GPU instead of using pytorch
- However, to build this extension, we actually compile this code from source as it is written in c, c++ and cuda
	- To fix this we just had to specify the cuda and c++ version in the backend.py script in the raymarching directory
	- The code actually compiles this from source every time we use our ros2 run command so its better to change the backend.py script which starts up at run time
	- You can also change the setup.py script in the raymarching directory and run a command to build the extension and the run the node but either or works
- We also ran into an issue where the folder that has these files for building the extension wasn't being copied over to the install directory for ros2 to build
	- To fix this we just had to specify the directory in our setup.py file in the ros2 workspace and add an init.py file to the directory that had these files
- I also learned that ros2 was pointing to the system python interpreter the entire time and not in the conda env
	- For some reason everything still seems to work well with python 3.10
	- The conda env is running torch 1.10.2
	- The sys env is using torch 2.6.0 which are the biggest differences between the two envs
	- I think because torch is only really important for training it doesn't matter that much so we'll keep going with this until a bigger issue arises
	- Otherwise we can simply run our node with python 3.10 (3.9) won't work with ros2 humble anyways
	- The node is using everything in the system:
		- torch 2.6.0 and cuda 12.x
		- The conda env is torch 1.10 and cuda 11.3

### Tuesday
- Fixed an error where the compiler was using syntax that's part of an older version of cuda which is 11.3 
	- We are using the system's version now so we need to adjust it to use cuda 12.6 instead
	- This was for gridencoder.py
- Fixed an error where get encoder function wasn't able to read one of the types of encodings
	- Realized the get encoder function is called multiple times and I didn't include one of the parameters 
- Added a custom function to account for argument parsers which was required by the trainer class
- Loaded the nerf model in the ros2 node
- Worked on the user journeys for the user facing side of the app

### Wednesday
- Skipped work today to put some more work into savor
- I was able to finish the user journeys and got it uploaded to the google drive






