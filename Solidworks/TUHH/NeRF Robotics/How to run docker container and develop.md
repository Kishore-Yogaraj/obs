The docker containers are set up to allow you to develop inside the docker container as well as not worry about any dependencies when running the container. This allows for quick start up and easy development amongst shared users.

The first step is to clone the repo into your local machine:
```bash
git clone https://github.com/Kishore-Yogaraj/NERF_ws.git
```

Make sure you're in the docker directory to run docker commands
```bash
cd NERF_ws/docker
```

Once in this directory you need to build all the containers using this command
```bash
docker compose build
```

After this you can run the bellow command to start up the container
```bash
docker compose up
```

Once in the container you will need to cd into the NERF_ws
Then you will need to source ros2 using this command:
```bash
source /opt/ros/humble/setup.bash
```

You can also add this to the bashrc inside the docker container so you don't have to source every time

After that you will need to build and install
```bash
colcon build
source install/setup.bash
```

And then you can run your node:
```bash
ros2 run occupancy_package node_occupancy
```
When you run this node the first time it will take a long time to start up 

If you comment out the `command` line in the docker compose file then you will be able
Or you can start up a specific service by specifying the service name