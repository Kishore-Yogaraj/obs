I want to dockerize the nodes I'm developing for the NeRF navigation project. 

Why do I want to do this?
I want to do this so that the code can be ran on any computer without the need to worry about dependencies. This makes it easier for multiple people to work on the project as well. I need to be able to allow my teammate to contribute to this project on his side as well and dockerization makes this easy to do.

Some things that need to be noted/questions I have
The plan is to create a docker file for each of the packages I have so that I can run them individually using docker. Think of docker as an alternative way to run the node. When you spin up the container you are essentially running the ros2 run command is what you should think of docker as. We currently use ros2 run for each of the packages and the nodes within the packages which means that we should have 3 different docker files. We will also be using a docker compose yaml file which will be able to orchestrate all 3 docker files. This means that when we run the docker compose file we should be able to run all 3 docker files or just one docker file that is in the docker compose file. 

Think about docker files as if you were starting up your own virtual machine. In order to run a node on this virtual machine what do you need?

- You would need ROS2 installed on the virtual machine
- You would need CUDA installed on the virtual machine
- You would need all your dependencies installed on the virtual machine
- Do you need Linux on your virtual machine?
- Do you need the model on the virtual machine?

Here's the plan we are going to execute:
Let's first work on creating a docker file the correct way for the occupancy package. This will be the hardest package to do it for because it has the model weights, the bindings and its what needs CUDA. 

Base images are created and saved in the docker hub which can be used for larger installs such as ROS2 and Cuda (I believe). Base images provide a solid starting point that includes any of the fundamental components you may need for your virtual machine. For example, you would need linux, ros2 and python in a virtual machine in order to do basic development in. **I'm wondering if its better to develop inside a container or outside the container and use volume mounts?** Base images also significantly reduce the development time to build an image and start up a container because you're not building it completely from scratch. Think about the structure of a house. You wouldn't build a house from scratch every time using mixing concrete, pipes, electrical work. Similarly you don't want to build your entire container from 0.

Another thing to note is that some of your directories are currently hard coded in file paths, when you dockerize this and create your virtual machine, you would need to create a new directory path that relates to the virtual machine file path. Docker also uses layering which means that each instruction forms a layer for that image. The base image from the bottom layer rarely changes which means docker can cache the unchanged layers and focus on application specific dependencies which are constantly changing.

You're going to end up creating a docker file of the workspace with the src directory and one of the packages in each docker file. 

How much do you actually have to have copied over into the virtual machine?

What is the difference between developing inside of a container vs outside of a container?
Developing outside containers with bind mounts is a generally more common and often preferred approach. What this means is that you write your code on your local machine using your familiar IDE and tools while using bind mounts to sync your code into a running container. (Does that mean you always have to be running your container before your code on your local machine?)

You can think of this like having your workspace on your desk (local machine) but being bale to instantly teleport your work into a testing environment (container) whenever you want to see how it runs. This means that any changes you make to files on your local machine are immediately reflected in the container. This provides a really fast feedback loop so you can edit code with your preferred editor and instantly test it in the containerized environment.

Developing inside containers is definitely possible but not preferred for these reasons:
- You need to set up your entire dev environment like an editor, debugging tools, git config, etc.)
- The container needs to be rebuilt whenever you add new development dependencies
- You might lose work if you remove the container without committing it
- IDE features like code completion and debugging will be more complicated to set up

We have developed a docker file for the occupancy package but lets go over what exactly it does:
First we use the nvidia cuda devel image to ensure we have the compilers for the .cu file

Then we set the basic locale environment variables. This basically just makes sure that the environment variables are encoded with UTF-8 because ROS2 depends on this encoding for proper handling of strings, filenames and messages. Without UTF-8 we might run into errors related to character encoding.

Then we install the proper OS packages so that we can actually build ROS2.

We then set locale

We then add the apt repo for ROS Humble and then install it as we would if we were installing on our local machine.

We then install our other python dependencies.

We then create the ros2 workspace called NERF_ws and we created the src directory inside of this workspace. We then copy over the occupancy_package directory into the container

We then source and build the workspace in the docker container.

Then we set our environment variables.

The entry point script is like your bashrc file. Every time the container runs (new shell is opened) it will run those commands. This is set in the next step. We also set `chmod +x` which adds execute permissions to the script which allows it to be run as a command in the container when the container starts up

`ENTRYPOINT ["/entrypoint/sh"]` set the script as the **entrypoint** for the container. The purpose of this is to be a set of instructions that defines the default command that will be executed when the container starts. Any arguments passed to docker run will be appended to this command.

Bind mounts:
The code on the local machine will be mounted at run time to the docker container. This means you have to start the container first and then make changes to the code and then you can run commands in the integrated environment that vscode provides.

Bind mounts are typically only setup in the docker compose file because bind mounts are a run time configuration and not a way to define the image (installing dependencies, setting up the environment, copying files, etc.). This means that it should only be set up in run time processes like running docker compose

When someone starts up the container, will the container run the ros2 node right away or will they have to type the command?

### Docker commands

Tells docker where to find the docker file while in the NERF_ws directory (that's what the -f flag does) and -t assigns a name to the built image and the . at the end sets the build context to the current directory so docker can access everything it needs
```bash
docker build -f docker/Dockerfile -t occupancy_package_image .
```

Permission denied error
- Run the below command and then reboot the system
```bash
sudo usermod -aG docker $USER
```

Command to remove images
```bash
docker rmi $(sudo docker images -q)
```

Command to remove containers
```bash
docker rm $(docker ps -aq)
```

Building a docker image using docker compose
```bash
docker-compose build <insert service name>
```

Starting up a docker container using docker compose
```bash
docker-compose up <insert service name>
```

Deleting a specific docker image
```bash
docker image rm <image id>
```



We have two issues right now:
GPU is running out of memory in the docker container. And every time we start the container the c code and cuda code recompiles which takes a really long time.

The flow I want is to be able to up the containers without running the program. Then you go into each of the containers and run your ros2 commands in there.

This means we should have a docker file for each ros2 package and as well as a service for each package to be run

And then for production, you can run all the containers using the docker compose file

connect to foxglove
create bash rc to stop sourcing everytime
Add requirements.txt