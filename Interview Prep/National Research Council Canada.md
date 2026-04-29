### Tell Me About Yourself
Hi, I'm Kishore Yogaraj, currently an Engineering student at the University of Waterloo. I would say my passion lies in robotics, computer vision, and machine learning, with hands-on experience in mobile robotics and 3D computer vision.

Currently, I'm Co-Captain at Watonomous, our university's robotics team, where I lead a team developing various robotic systems including self-driving cars, humanoids, and autonomous RC cars. One of my key contributions has been developing a 3D Multi-Object Tracking algorithm using Kalman filters integrated with ROS2 for real-time tracking of vehicles, pedestrians, and traffic signs.

Earlier this year, I worked as a Machine Learning and Robotics Research Assistant at the Technical University of Hamburg in Germany, where I developed a complete vision-based navigation system. This involved using Neural Radiance Fields to generate occupancy grids, implementing A* pathfinding with custom costmaps, and deploying everything within a containerized ROS2 framework for autonomous robot navigation.

I also have industry experience from my internship at Eon Media, where I deployed AI pipelines for brand recognition and facial recognition systems, processing over 500,000 videos and improving detection accuracy significantly.

What excites me most about this opportunity at NRC is the chance to apply my robotics and AI skills to support healthy aging - it's meaningful research that can have real societal impact. My experience with human-robot interaction through developing autonomous systems, combined with my technical skills in deep learning frameworks like PyTorch and TensorFlow, positions me well to contribute the intelligent robotics project.

### Hamburg Internship
During my research assistant position at the Technical University of Hamburg, I worked on developing a complete vision-based navigation system for autonomous robots using only RGB camera inputs - essentially creating a navigation stack that didn't rely on expensive sensors like LiDAR.

**The Core Project:** My main focus was leveraging **Neural Radiance Fields (NeRFs)** to generate detailed 3D environmental reconstructions from RGB images, then converting these into practical navigation maps. I developed a pipeline that could take camera data, create NeRF representations, generate occupancy grids from those 3D models, and perform ground segmentation using Z-thresholding to identify navigable surfaces.

**Technical Implementation:** I built a complete navigation stack that integrated **A-star pathfinding** with custom costmaps. The key challenge was bridging the gap between the beautiful 3D NeRF reconstructions and the 2D representations that navigation algorithms actually need. I developed methods to convert the NeRF-derived 3D maps into 2D costmaps while preserving critical obstacle information.

**Systems Integration:** Everything was deployed within the **ROS2 framework** and containerized with **Docker** for reproducibility and easy deployment. I designed the control pipeline to generate smooth navigation trajectories for a differential drive robot, all running in simulation initially.

**Key Achievement:** The most significant accomplishment was creating a working end-to-end system that could take raw RGB camera data and produce reliable autonomous navigation. This was particularly valuable because it demonstrated how cutting-edge computer vision techniques like NeRFs could be integrated into practical robotics applications, potentially reducing the sensor costs for autonomous systems while maintaining navigation capabilities.

The project gave me deep experience in combining state-of-the-art AI research with practical robotics engineering - exactly the kind of cross-disciplinary work that's essential in modern autonomous systems research.

### How would you decide what sensors are needed for a robotics project/application?
Great question! Sensor selection is really critical and something I've had to think through carefully in my projects. My approach is systematic and starts with understanding the core requirements.

First, I always begin by clearly defining what the robot needs to perceive and accomplish. For example, when I was working on the 3D Multi-Object Tracking system at Watonomous, we needed to track cars, pedestrians, and traffic signs in real-time, so that immediately told us we needed sensors capable of spatial awareness and object classification.

I then consider the environmental constraints. In my work in Germany on the vision-based navigation system, we were operating indoors in controlled lighting, so RGB cameras were sufficient. But for the autonomous vehicle work, we needed sensors that could handle varying weather, lighting conditions, and longer ranges - which is why we used multiple cameras with different focal lengths and fields of view.

The technical requirements are equally important. For real-time applications like our autonomous RC car project, I had to balance sensor accuracy with processing speed. We chose cameras that could provide sufficient resolution for steering angle prediction while still allowing real-time inference on the NVIDIA Jetson platform.

I also always consider sensor fusion opportunities. In the Multi-Object Tracking project, we used six cameras to get comprehensive coverage, but I've learned that combining different sensor modalities - like cameras with LiDAR or IMUs - often gives more robust results than relying on a single sensor type.

Cost and power consumption matter too, especially for mobile robots. And I always think about redundancy for critical functions - if a sensor fails, can the system still operate safely?

Finally, I prototype and test early. Even with careful planning, you often discover limitations only when you start collecting real data, so I build in flexibility to adapt the sensor suite based on initial results.

### 3D Multi Object Tracking
One project I’m particularly proud of is the 3D multi-object tracking system I built, which was designed for tracking vehicles, pedestrians, and traffic signs in a self-driving car environment. The goal was to create a robust tracking system that could handle multiple objects in real-time, predict their movements, and account for situations where objects were temporarily occluded.

I used a combination of the **Kalman Filter** and the **Hungarian Algorithm** to solve this. The **Kalman Filter** is a mathematical model that helps predict an object’s future position based on its current state (like position, velocity) and update it as new data comes in. This was crucial for keeping track of moving objects even when they weren’t detected for a frame or two. The **Hungarian Algorithm** was used for data association—it matches detected objects in each frame with objects that were being tracked, ensuring that the system accurately tracks the same objects over time.

One of the most challenging parts of the project was handling situations where multiple objects were close together or when an object temporarily disappeared behind another (like a car passing behind a truck). The Kalman Filter allowed us to predict where the object would likely reappear, while the Hungarian Algorithm helped reassign detections when they re-emerged.

What I learned from this project is how crucial **data association** is when tracking multiple objects in real-world scenarios. You can't rely on the detection system alone because objects move, they might be missed in certain frames, or multiple objects can appear close together. It taught me the importance of combining different algorithms—like the Kalman Filter for prediction and the Hungarian Algorithm for matching—to create a more reliable system.

Additionally, I learned about the importance of real-time performance optimization. While the theory behind these algorithms is powerful, implementing them efficiently, so they run in real-time on self-driving cars, was a challenge that required a lot of testing and tuning.

#### How did the Kalman Filter and Hungarian Algorithm work in your 3D tracking node?
In my 3D tracking node, I used the Kalman Filter and Hungarian Algorithm to track objects like cars and pedestrians from sensor data in real-time. These two algorithms work together to maintain accurate tracking, even when objects temporarily disappear from the sensor’s view or overlap.

1. **Kalman Filter**: The Kalman Filter is a recursive state estimation algorithm used to predict the future state of an object, given noisy observations. In my tracking node, each object’s state was defined by its position and velocity in 3D space. The filter has two steps:
    
    - **Prediction Step**: Based on the current state and velocity of an object, the Kalman Filter predicts the object’s next position in the following frame. This helps in situations where an object might not be detected in every frame, as we can estimate where it should be based on its previous state.
    - **Update Step**: When new measurements (like a new detection from a LiDAR or camera sensor) become available, the Kalman Filter updates its prediction by incorporating this measurement to correct the state estimate. This balancing act between prediction and correction allows for more robust tracking even with noisy sensor data.
    
    Mathematically, the filter uses a set of equations that include a **prediction step** based on the object’s current state and a **correction step** when new measurements come in, all while considering the noise in both the process and the measurements.
    
2. **Hungarian Algorithm**: The Hungarian Algorithm solves the **data association problem**, which is crucial when tracking multiple objects. In every new frame, we receive multiple detections (potential objects), and we need to associate these new detections with the existing tracked objects from the previous frame.
    
    The Hungarian Algorithm works by minimizing the cost of matching these detections with the tracked objects. The cost is typically defined as the distance between the predicted position of the tracked object (using the Kalman Filter) and the new detection. It formulates this matching as an assignment problem and finds the optimal set of matches that minimizes the overall cost. Once the new detections are associated with the correct objects, we can update the object’s state with the new measurement.
    
    In practice, the Kalman Filter helps smooth the tracking of each object, while the Hungarian Algorithm ensures the correct correspondence between detected objects across frames, even when objects temporarily disappear from view or overlap with each other.

#### Issues I ran into
One particularly frustrating issue I ran into was the **data association** problem when multiple objects were close together or when they temporarily overlapped. The tracking system would sometimes struggle to reassign detections accurately, especially when objects passed behind each other, like a car moving behind a truck. This led to the system occasionally switching the identities of the objects or losing track of them altogether, which is a major problem for reliable tracking in self-driving vehicles.

The root of the problem was that the **Hungarian Algorithm**, which I was using for data association, struggled when the distance between detected objects and their predicted positions (from the **Kalman Filter**) became too small or indistinguishable. This caused the system to get confused about which detection matched which object.

To solve this, I fine-tuned the **cost function** in the Hungarian Algorithm, which determines how the detections are matched to the tracked objects. Instead of just relying on the distance between predicted and detected positions, I incorporated additional information like the size and velocity of the objects into the cost function. This gave the algorithm more context when making its matching decisions, making it less likely to switch object identities when they were close together.

This adjustment significantly improved the accuracy of the tracking system. It taught me that even small changes in how you handle data association can make a big difference, and that combining multiple features—not just position—can help make the system more robust.


### Nerf Navigation
One project I'm particularly excited about is the vision-based navigation system I developed using Neural Radiance Fields (NeRFs) during my research at the Technical University of Hamburg. The goal was to create a complete autonomous navigation stack that could take RGB camera inputs and enable a robot to navigate safely through indoor environments without requiring pre-built maps or expensive sensors like LiDAR.

I used **Neural Radiance Fields** as the core technology to solve the mapping and localization challenge. NeRFs are powerful because they can reconstruct detailed 3D representations of environments from just a collection of 2D RGB images taken from different viewpoints. What made this particularly interesting was that I then converted these rich 3D NeRF representations into occupancy grids and performed ground segmentation using Z-thresholding to identify navigable surfaces versus obstacles.

The most challenging part was bridging the gap between the beautiful 3D reconstructions that NeRFs generate and the practical 2D representations that navigation algorithms need. I had to develop a pipeline that could convert the NeRF-derived 3D maps into 2D costmaps that work with traditional path planning algorithms like **A-star**. This involved careful consideration of how to project 3D occupancy information down to 2D while preserving critical obstacle information and ensuring the robot could distinguish between navigable floor space and walls or furniture.

What I learned from this project is how powerful it can be to combine cutting-edge computer vision techniques with established robotics frameworks. NeRFs gave us incredibly detailed environmental understanding from just camera data, but the real value came from integrating this into a complete navigation stack within ROS2. I also discovered the importance of **containerization with Docker** - it made deploying and testing the entire pipeline much more reliable and reproducible.

Additionally, I learned about the computational trade-offs involved in real-time robotics applications. While NeRFs can produce stunning 3D reconstructions, optimizing them for real-time navigation required careful consideration of inference speed and memory usage. This experience taught me to always think about the entire pipeline from perception to action, not just individual algorithms in isolation.

#### Issues You ran into and how you overcame it in your nerf navigation project
One of the biggest challenges I faced with the NeRF navigation project was the **computational bottleneck** between generating high-quality 3D reconstructions and achieving the real-time performance needed for actual robot navigation.

**The Problem:** Initially, I was getting beautiful, detailed NeRF reconstructions from my RGB camera inputs, but the inference time was way too slow for practical navigation. The NeRF model was taking several seconds to render novel views and generate the occupancy information I needed, which is completely unusable when a robot needs to make navigation decisions in real-time. I was essentially stuck with this trade-off between reconstruction quality and speed.

**The Investigation:** I spent time profiling where the computational bottlenecks were occurring. The issue was twofold - first, the NeRF network itself was computationally expensive for each query point in 3D space, and second, I was trying to do too much processing on the full 3D representation when I really only needed specific 2D projections for navigation.

**The Solution:** I tackled this by implementing **ONNX optimization** for the NeRF inference pipeline. ONNX allowed me to optimize the neural network for inference speed while maintaining accuracy. But the real breakthrough came from rethinking my approach to the 3D-to-2D conversion process.

Instead of generating dense 3D occupancy grids from the entire NeRF representation, I implemented a more targeted approach. I focused on generating only the specific 2D costmap slices I needed for navigation, using strategic sampling of the NeRF at the appropriate height levels. This dramatically reduced the computational load while still giving me the navigation-relevant information.

**Key Insight:** The biggest lesson was learning to **optimize for the end application**, not just the intermediate representation. The NeRF reconstruction quality was impressive, but what mattered for navigation was having reliable obstacle detection and free space identification in real-time. By focusing on generating just the 2D costmaps efficiently rather than perfect 3D reconstructions, I could maintain the core functionality while achieving the performance needed for practical deployment.

This experience taught me that in robotics applications, you often need to balance theoretical elegance with practical constraints, and sometimes the best solution isn't the most sophisticated one, but the one that works reliably in real-world conditions.


### What is a recent paper you read and what is something you learned from it
One paper I recently read that really caught my attention is "A review of high-definition map creation methods for autonomous driving" by Bao et al. This comprehensive survey paper provided an excellent overview of the current state of HD map generation techniques, which is directly relevant to my work in autonomous systems.

What I found most valuable was learning about the **three-layer HD map structure** that's commonly adopted in the industry - the Road Model, Lane Model, and Localization Model layers. This gave me a much clearer understanding of how different types of information are organized hierarchically in HD maps. The Road Model handles topology and navigation, the Lane Model deals with lane-level features for decision making, and the Localization Model contains landmarks and road furniture for precise vehicle positioning.

The section on **point cloud map generation methods** was particularly insightful for my work. I learned about the different approaches to sensor fusion - from LiDAR-only methods like LOAM and LeGO-LOAM, to multi-sensor fusion techniques like LIO-SAM that combine LiDAR, IMU, and GPS data. This helped me understand the trade-offs between computational complexity, accuracy, and robustness when designing mapping systems.

What really struck me was the comparison between online and offline mapping approaches. In my NeRF navigation project, I was essentially doing offline mapping - collecting all the RGB data first, then processing it to create the 3D representation. But seeing the real-time capabilities of systems like FAST-LIO2 that can handle LiDAR rates over 100 Hz made me think about how my approach could be adapted for real-time applications.

The paper also reinforced something I've experienced in my own projects - the critical importance of **sensor fusion**. My multi-object tracking work used multiple cameras for comprehensive coverage, and this paper showed how combining different sensor modalities like LiDAR, cameras, and IMU leads to much more robust mapping systems than relying on any single sensor type.

This survey really helped me see how my individual projects fit into the broader landscape of autonomous systems and gave me ideas for future research directions.

### How do you train and evaluate machine learning models
My general approach to training and evaluating models follows a systematic pipeline that I've refined through experience.

**Data Preparation:** I start by thoroughly understanding my dataset - checking for class imbalances, missing values, and data quality issues. I always split the data early into train/validation/test sets, typically using an 80/10/10 or 70/15/15 split depending on dataset size. The test set gets locked away and only used for final evaluation. I implement appropriate preprocessing like normalization, handling categorical variables, and data augmentation where relevant.

**Model Selection and Architecture:** I choose the model architecture based on the problem type and data characteristics. For tabular data, I might start with simpler models like Random Forest or XGBoost before moving to neural networks. For images, I'll use CNNs, and for sequential data, RNNs or Transformers. I always start with a baseline model to establish a performance floor.

**Training Process:** I use techniques like cross-validation during development to get robust performance estimates. I implement early stopping based on validation loss to prevent overfitting, and I monitor multiple metrics during training - not just loss, but accuracy, precision, recall, or domain-specific metrics. Learning rate scheduling and regularization techniques like dropout or L2 regularization are standard parts of my training pipeline.

**Evaluation Strategy:** Beyond standard metrics, I focus on understanding model behavior through error analysis, confusion matrices, and feature importance analysis. I test for robustness by evaluating on different data distributions or adversarial examples. For critical applications, I also assess calibration - whether the model's confidence scores are meaningful.

**Validation and Iteration:** I always perform final evaluation on the held-out test set only once. If performance isn't satisfactory, I go back to feature engineering, architecture changes, or data collection rather than optimizing on the test set. I document all experiments to track what works and what doesn't, which helps inform future modeling decisions.

### How would you design a machine learning vision model to help monitor and understand changes in food quality over time?
**Problem Decomposition:** I'd break this down into several key tasks: detecting visual indicators of spoilage (color changes, mold growth, texture deterioration), tracking these changes over time, and potentially predicting remaining shelf life. Each food type would likely need specialized attention since spoilage patterns vary significantly between fruits, vegetables, dairy, meat, etc.

**Data Collection Strategy:** I'd design a controlled data collection setup with consistent lighting, camera angles, and background conditions. The key would be capturing time-lapse sequences of various food items as they degrade under different storage conditions - temperature, humidity, exposure to air. I'd need to label not just the current state but also time-to-spoilage information to enable predictive capabilities.

**Model Architecture:** I'd likely use a hybrid approach combining convolutional neural networks for spatial feature extraction with recurrent components for temporal modeling. A CNN backbone could extract features related to color, texture, and morphological changes, while an LSTM or Transformer could model how these features evolve over time. For real-time applications, I might implement this as a two-stage system - a lightweight detection model for identifying regions of interest, followed by a more complex temporal analysis model.

**Feature Engineering:** Beyond standard RGB features, I'd incorporate color space transformations that might be more sensitive to spoilage indicators - HSV for better color change detection, or even specialized color indices used in agricultural applications. Texture analysis through methods like Local Binary Patterns could help detect surface changes that indicate spoilage.

**Training and Evaluation:** I'd train using sequences of varying lengths to handle different monitoring periods. The evaluation would need to consider both accuracy in detecting current spoilage state and precision in predicting future deterioration. I'd use metrics like time-to-spoilage prediction error and early spoilage detection rates. Cross-validation would need to account for the temporal nature - ensuring test sequences come from different time periods or food batches than training data.

**Practical Considerations:** For deployment, I'd need to handle varying lighting conditions, different camera qualities, and potentially integrate with IoT sensors for environmental monitoring. The model would need to be robust enough to work across different food types while being computationally efficient for continuous monitoring applications.

### What is a diffusion model
A diffusion model is a type of generative AI model that learns to create new data by reversing a gradual noise-adding process. It's become one of the most powerful approaches for generating high-quality images, and it's the technology behind systems like DALL-E 2, Midjourney, and Stable Diffusion.

**The Core Concept:** The key idea is based on a two-step process. First, you take real data (like images) and gradually add random noise to them over many steps until they become pure noise - this is called the "forward diffusion process." Then, you train a neural network to reverse this process - learning to gradually remove noise and recover the original data structure.

**How Training Works:** During training, you show the model images at various stages of this noise-adding process and teach it to predict what the less noisy version should look like. The model learns the patterns and structures that exist in real data by understanding how to "denoise" at each step. This is different from GANs, which learn through adversarial training, or VAEs, which learn by encoding and decoding through a latent space.

**The Generation Process:** To generate new content, you start with pure random noise and repeatedly apply the trained denoising network. At each step, the model removes a small amount of noise while preserving and enhancing the emerging structure. After many iterations, you end up with a high-quality, realistic image that follows the patterns the model learned from the training data.

**Why They Work So Well:** Diffusion models have several advantages. The gradual, iterative process allows for very stable training compared to GANs. They can generate extremely high-quality, diverse outputs. They're also very controllable - you can guide the generation process with text prompts or other conditioning information. The step-by-step nature means you can also interrupt or modify the process partway through.

**Practical Applications:** Beyond image generation, diffusion models are being applied to video generation, audio synthesis, 3D model creation, and even protein folding prediction. They're particularly powerful when combined with conditioning mechanisms that let you control what gets generated through text descriptions or other inputs.

### What projects have you used pytorch in?
I used PyTorch in my LSTM-based soil deformation analysis project to predict sequences of structural deformations over time, moving beyond single-point predictions to forecasting entire deformation series.

I built the architecture using PyTorch's **nn.LSTM** module for the sequence-to-sequence model. What made PyTorch perfect for this was its dynamic computation graph, which easily handled variable-length sequences since different soil monitoring periods had different durations.

I implemented custom **DataLoader** classes for the sequential data and used PyTorch's **autograd** for backpropagation through time. I experimented with different loss functions beyond MSE - creating custom losses that penalized larger prediction errors more heavily since catastrophic deformation predictions were more critical.

The **ReduceLROnPlateau** scheduler helped with the complex soil deformation patterns by adapting the learning rate during training. PyTorch's flexibility was crucial - I could easily modify the LSTM architecture, add attention mechanisms, and hook into hidden states to visualize what time steps the model was focusing on for predictions.

The eager execution made debugging intuitive, and the seamless NumPy integration was essential since I was working with numerical soil mechanics data that needed extensive preprocessing alongside the deep learning components.


### What is the aging in place challenge
This is a challenge created by the National Research Council of Canada to help support older Canadians who want to remain in their homes and communities as they age by developing technologies and innovations instead of having to move to a retirement home or hospital because they aren't able to take care of themselves anymore. This is a 7 year challenge which launched in April 2021 with the goal of increasing the number of older adults who can live in their chosen homes and communities by 2031.

### Questions
- How do robots play a role in your research for human motion generation for older adults using diffusion models?
- What excites you the most about working at NRC?
- How would you define success in this role? What would be the key indicators to you that you hired the right person?

hand tracking
VR controlled headset
Haptic 
