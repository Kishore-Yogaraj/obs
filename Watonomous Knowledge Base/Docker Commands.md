```bash
docker ps #Lists current running containers 
docker ps -a #Lists all containers on the system 
docker images #Lists all images on the system that have been downloaded 
docker build --tag <tag name> . #Gives the built image a tag to call it by 
docker run <tag name> docker run <image id> #Can run images using the image id which can be found by doing 
docker images docker stop <container id> #Stops container 
docker run -it <tag name> bash #Runs it interactively so you can run things in an environment. "Give me a terminal that I can type in" 
newgrp - docker docker image pull <registry name> #To download images from registry 
docker image rm -f <image name> #To force delete. Usually won't allow you to delete if it ran a container 
docker container stop <container name> #Container name will be on the far left for this command with some random generated name 
docker container start -i <container name> #Runs interactively. Once a container has been created and then stopped. You can just run the container instead of running the image docker container prune #Delete all containers 
docker container rm <container name> #Delete specific container 
docker run --name <container name> #Allows you to give it a name instead of a random one 
docker exec -it <container name> #Allows you to have a second terminal in one container 
docker exec -it crazy_rhodes ls #Allows you to run a single command within a container 
docker image build -t <image name> <path to docker file> #Build docker iamge with name and path to docker file 
docker run -it -v $PWD/source:/my_source_code my_image #Share files from host computer to the container 
docker run -it -v <absolute_path_on_host>:<absolute_path_in_container> #PWD is 'print working directory' 
docker rmi -f $(docker images -q) #Force delete all images 
docker rmi <image id> <image id> #Delete specific images 
docker-compose up 
docker-compose up <service name> #Start up specific docker container in a docker compose file 
docker-compose down 
docker-compose -f <path to docker compose file> up <service name> 
docker stop $(docker ps -q) #Stop running containers 
docker stop <container_id> or <container_name>
docker-compose up -d or ./watod up -d #continue working in same terminal (detached mode)

docker exec -it <container_name> /bin/bash #go to dev container terminal 
exit #exits the dev container terminal

# To build without cached layers
docker-compose build --no-cache <name of docker file>


```

Hey all the
Testing oo0adf

Working with 

wato_asd_tooling is currently set up tr ubunut 3 
That will break 