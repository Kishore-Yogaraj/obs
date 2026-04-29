Here's the workflow for how we train our models for NeRFs

The first step is to clone the torch-ngp-container repo

Second step is to build and start the container

Attach visual studio to the container

Outside of the container input your images as shown:
- The data folder should be inside of the torch_ngp folder

```
data/
└── my_scene/
    └── images/
        ├── image001.jpg
        ├── image002.jpg
        └── ...
```

Run your colmap command:
- You run this command while you're inside the torch_ngp directory not the torch-ngp directory

```
python scripts/colmap2nerf.py --images data/my_scene/images --run_colmap --estimate_affine_shape
```

Before you run your training command there is one more crucial step:
- You will end up with 3 JSON files which are train, test and val
- Delete the other two JSON files and change the name of the train file to be transforms.json

Run your training command:
- Do this also while you're in the torch_ngp directory

```
python main_nerf.py data/my_scene --workspace <insert name of folder you want results to go to> -O --bound 2.0 --scale 0.5 --dt_gamma 0.0 --iters 4 0000 --density_thresh 10.0
```

```bash
docker system prune -a --volumes

docker container prune

docker image prune

sudo chown -R user:user <folder path>

iters/dataset = number of epochs
```

### Great validation 
```python
python3 main_nerf.py data/my_scene --workspaec real_ws -O --bound 3.0 --scale 1.0 dt_gamma 0.0 --iters 40000 --density_thresh 10.0
```