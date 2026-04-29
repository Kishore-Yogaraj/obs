### Purpose of the Repo
- What problems does this repo solve and why they're relevant to VCycne

### Explore the structure of the code base
- README - quick start and use cases
- congifs - different model architectures
- tools - training, inference scripts
- docs - tutorials and explanations
- Provides a feel for how modular, extensible and production ready the repo is

### Run the Examples and Demos
- Clone the rep, set up the environment (requirements.txt)
- Run the sample inference scripts to see outputs on test images
- Observer how inputs/outputs are formatted
- Understand practical usability and what kind of data pipelines you might need

### Check the APIs and Extensibility
- Find out how easy it is to plug in your own dataset (food waste images)
- Understand the dataset class structure, training configs and evaluation pipeline
- Check how results are returned (raw mask, probability map, post processed output)

### Goals
- Learn how FoodSAM can be used to segment compost food items
- Become comfortable enough with repos to be able to prototype, debug or extend them


## FoodSAM Breakdown

### Purpose
- The segment anything model (SAM) is really good on a variety of segmentation benchmarks
- It has zero shot transfer capabilities on 23 different segmentation datasets
- SAM lacks the class specific information for each mask
	- If you feed SAM an image of a plate of rice, chicken, and broccoli it might output a mask of the rice, chicken or broccoli
	- **It only provides the object boundaries and not the class label**
- FoodSAM uses coarse semantic segmentation (a weaker model trained to recognize broad food categories) with SAM's high quality masks
	- SAM provides precise mask boundaries
	- SAM can cut out objects very precisely but doesn't know what the objects are
	- Class specific segmentation allows for not only the ability to cut the food out but also tell you what food is cut out
	- FoodSAM combine's the segmentation ability of SAM with a food aware model to get both precision and semantic labels
	- **Coarse segmentation means that there are large blobs that cover each class without much details (would not be able to do pixel by pixel segmentation)**
- FoodSAM contains three basic models: SAM, semantic segmenter and object detector
	- SAM creates the class agnostic binary masks
	- Semantic segmenter provides food category labels via mask category match
	- Object detector provides the non food class for background masks
	- Semantic mask is enhances using merge strategy and produces instance and panoptic results