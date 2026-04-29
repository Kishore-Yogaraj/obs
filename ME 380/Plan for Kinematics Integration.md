My plan for today is to get the inverse kinematics integrated into the robot system.

### What success looks like for this
I want to be able to identify a point in the workspace that I want the robot to take the end effector (specify a an x and y position in mm), then I want to see the robot get to that point. I have the code for the inverse kinematics I just have to find it again in chat gpt.

I also have the working firmware code to get the robot arm to move, I just have to understand a bit better with the help of AI. 

Once we get these pieces fitted together we should have the inverse kinematics working for the robot. 

### Plan
- Find inverse kinematic code document and create a github repo for it
- Go through the firmware code and understand how it works
- Create your own platform.io directory that you can work with and start from scratch but add files from the old project as needed
	- Get one motor to spin
	- Get two motors to spin at the same time
	- Create a python file that will send a command to get both motors to spin
	- Integrate the inverse kinematics into this python file and everything should go as planned