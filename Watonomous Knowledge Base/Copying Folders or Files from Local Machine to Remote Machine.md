- Below is the command to copy folders/files from your local machine to a remote machine
- Do this command in git bash and make sure that the folder you want to move is in your home folder so in our case the folder "kisho"
	- I'm not sure how true this is but as of now I think this is the right way
	
```bash
scp -r c:/users/kisho/2d_detections kishoreyogaraj@tr-ubuntu3:/mnt/wato-drive2/rosbags2/camera_object_detection

scp -r </path/to/folder/local_machine kishoreyogaraj@tr-ubuntu: <path/to/folder/on/remote_machine>

#File transfer from remote machine to local machine
 scp -r -T kishoreyogaraj@tr-ubuntu3:/home/kishoreyogaraj/wato_monorepo/src/perception/camera_object_detection/detections_2d c:/users/kisho

#File transfer from host machine to remote machine
 scp -r -T c:/users/kisho/detections_2d kishoreyogaraj@tr-ubuntu3:/mnt/wato-drive2/rosbags2/camera_object_detection

# The -T option prevents scp from interpreting the output from the remote machine as part of the file transfer. When scp connects to the remote machine any unexpected output (banner or shell outputs) can confuse scp and cause errors like "Received message too long"

# _t tells scp to treat the transfer as "non-interactive" by ignoring any initial output from the remote machine's shell. This can help prevent any errors if the remote machine is configured to display banner messages or other prompts that could interfere with the scp file transfer process
```


### Copying folder from docker dev container to remote machine

```bash
docker cp watod_kishoreyogaraj-tracking-1:/home/rosbag2_2024_11_18-04_53_08 /mnt/wato-drive2/rosbags2/camera_object_detection

docker cp <container name>:<path to folder on container> <path to folder on remote machine>
```