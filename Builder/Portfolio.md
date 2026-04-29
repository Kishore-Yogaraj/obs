### Nerf Navigation: Vision-Only Autonomous Robot Navigation

This project demonstrates a complete system for autonomous robot navigation using only an RGB camera as the primary sensor. Leveraging the power of Neural Radiance Fields (NeRF), specifically the efficient Instant-NGP implementation, the system achieves mapping, localization, and path planning in previously unseen environments without traditional sensors like LiDAR. Simulation tools like Gazebo were used for realistic testing, and visualization tools like Foxglove aided in development and debugging.

---

#### **Project Overview & Goal**

_Introduction: The primary objective of this project was to overcome the limitations of traditional navigation systems by enabling robust autonomy using only visual information._

The core goal was to build and implement a robotic system capable of navigating autonomously in an environment using solely an RGB camera. This involved creating a map of the surroundings, accurately determining the robot's position and orientation (localization) within that map, and planning collision-free paths to desired goal locations – all based on visual input processed through a Neural Radiance Field (NeRF). This approach explores the potential of NeRFs not just for novel view synthesis, but as a compact and effective world representation for robotic tasks.

---

#### **Approach: Leveraging Neural Radiance Fields (NeRF)**

_Introduction: To achieve vision-only navigation, this project employed Neural Radiance Fields as the fundamental representation of the robot's environment, handling mapping, localization, and planning within this framework._

The system architecture revolves around using Instant-NGP (`torch-ngp`), a highly efficient NeRF implementation, for environmental representation.

- **Mapping:** The environment is mapped by training an Instant-NGP model using a sequence of RGB images captured by the robot's camera along with their corresponding poses (obtained via SLAM or ground truth during a mapping phase). This trained NeRF implicitly encodes the 3D geometry and appearance of the scene, serving as the robot's map.
- **Localization:** Once the NeRF map is built, the robot localizes itself within this map using only incoming RGB images. This is achieved by optimizing the camera pose to find the position and orientation that best reproduces the observed view according to the learned NeRF model. Essentially, it answers the question: "Where must the camera be to see the current image, according to the NeRF map?" Particle filters or gradient-based optimization techniques are typically used for this pose estimation.
- **Path Planning:** Collision-free paths are planned directly using the NeRF representation. The density field learned by the NeRF provides information about occupied space. Path planning algorithms, such as Rapidly-exploring Random Trees (RRT*), can query this density field to check for potential collisions, allowing the generation of safe trajectories from the robot's current estimated pose to a target goal pose. The project includes implementations for sampling-based planners like RRT* adapted for use with the NeRF representation.

---

#### **Implementation Details, Simulation & Technologies**

_Introduction: The system was implemented using a combination of robotics middleware, deep learning frameworks, simulation environments, and visualization tools, integrating several key components._

The project is built primarily using Python and integrates several key technologies and libraries:

- **Robotics Framework:** ROS (Robot Operating System) is utilized for managing different software modules (nodes), handling communication between them (topics, services), and integrating with potential robot hardware. Key nodes likely include modules for NeRF training, localization, path planning, and robot control interface.
- **NeRF Implementation:** The `torch-ngp` library, a PyTorch-based implementation of Instant-NGP, is central to the project for efficient NeRF training and rendering.
- **Deep Learning:** PyTorch is used as the underlying deep learning framework for the NeRF model.
- **Path Planning:** Implementations of path planning algorithms like RRT* are included, adapted to query the NeRF's density field for collision checking.
- **Simulation:** **Gazebo** was employed as the primary simulation environment. It provided a realistic physics-based platform to simulate the robot's dynamics and sensor inputs (specifically the RGB camera). Gazebo was crucial for generating the image sequences required for training the NeRF model of the simulated environment and for testing the entire navigation stack (localization, planning, control) in a controlled, repeatable manner before potential hardware deployment.
- **Visualization & Debugging:** **Foxglove Studio** was used extensively for visualization and debugging. It allowed for real-time inspection of various system components, including visualizing the occupancy grid derived from the NeRF, monitoring the path generated by the RRT* planner, displaying cost maps used for navigation, and tracking the robot's estimated pose within the environment. This was invaluable for understanding system behavior and diagnosing issues.
- **Configuration:** The repository includes configuration files (`*.yaml`, `*.json`) for setting parameters related to NeRF training (learning rates, resolution), localization (particle counts, optimization steps), and path planning (step sizes, goal thresholds).
- **Code Structure:** The codebase is organized into logical components, likely including directories for NeRF-related functions (`nerf/`), planning algorithms (`planning/`), ROS nodes (`ros/` or `scripts/`), launch files (`launch/`), and configuration (`config/`).

---

#### **Challenges & Key Learnings**

_Introduction: Developing a vision-only navigation system based on NeRF presented unique challenges and offered significant learning opportunities in integrating deep learning with robotics, simulation, and visualization._

Implementing this system involved tackling several challenges inherent to NeRF-based navigation:

- Computational Cost: While Instant-NGP is efficient, training NeRFs and performing localization can still be computationally demanding.
- Dynamic Environments: NeRFs typically assume static scenes, making dynamic elements challenging.
- Localization Robustness: Accuracy can be sensitive to lighting changes or textureless areas.
- NeRF to Occupancy Conversion: Efficiently extracting reliable occupancy data for planning requires careful design.
- Simulation Fidelity: Ensuring the simulation accurately reflects real-world sensor noise and environmental properties is crucial for effective sim-to-real transfer.

Through this project, I gained significant experience in: Applying cutting-edge deep learning models (NeRF) to robotics problems, ROS development and integration, Simulation using Gazebo, Data visualization and debugging using Foxglove, Computer Vision techniques for localization, Path planning algorithms in non-traditional map representations, and System integration combining perception, planning, and control using only visual data.

---

#### **Outcome & Significance**

_Introduction: This project successfully demonstrated the feasibility of using NeRFs as a primary representation for robotic navigation tasks, relying solely on an RGB camera and validated through simulation._

The project successfully implemented and tested (in simulation) a pipeline capable of performing mapping, localization, and path planning using NeRF and visual data. It showcases the potential of implicit neural representations as a compact alternative to traditional maps, especially where sensors like LiDAR are impractical. Effective use of simulation (Gazebo) and visualization (Foxglove) was key to the development and validation process. The work builds upon research in NeRF-based navigation (citing relevant papers like `nerf-nav` and foundation models like `Instant-NGP`), contributing a practical implementation within a ROS framework.


### Behaviour Cloning RC Car: Autonomous Driving via Imitation Learning

This project demonstrates the development and implementation of a behaviour cloning system for an RC car, enabling it to autonomously navigate a track by mimicking human driving behaviours. Utilizing NVIDIA Jetson technology and advanced machine learning techniques, the system maps human-driven steering and throttle commands to visual input, allowing the car to autonomously replicate the trained behaviours in real-time.

---

#### **Project Overview & Goal**

**Introduction:** The main objective of this project was to create an RC car that could autonomously navigate a predefined path by imitating human driving behaviours captured during training sessions.

**Core Goals:**

- Train a neural network to predict accurate steering angles and throttle values based on camera images captured during manual driving.
    
- Implement real-time inference on the RC car using the NVIDIA Jetson architecture to autonomously control steering and throttle.
    
- Utilize data augmentation to ensure robustness and generalizability of the model.
    

---

#### **Approach: Behaviour Cloning & Deep Learning**

**Introduction:** This project leveraged behaviour cloning, a form of imitation learning, to replicate human driving strategies using convolutional neural networks (CNNs).

**Model Training:**

- **Data Collection:** A dataset was created by manually driving the RC car around a track, simultaneously capturing synchronized camera images and corresponding steering/throttle commands.
    
- **Data Augmentation:** Techniques such as zoom, pan, brightness adjustment, and horizontal flipping were applied to increase data variability and robustness.
    
- **Model Architecture:** Implemented using a ResNet-18 CNN model with convolutional layers and ELU activation functions to effectively learn and predict driving commands from visual data.
    

**Real-time Prediction:**

- Batch processing and real-time inference techniques were used to allow immediate response and accurate predictions, enabling smooth autonomous navigation.
    

---

#### **Implementation Details & Technologies**

**Hardware Components:**

- **RC Car Platform:** NVIDIA JetRacer kit
    
- **Computing Unit:** NVIDIA Jetson Nano
    
- **Camera:** CSI Camera module for capturing real-time RGB images
    

**Software & Tools:**

- **Programming Language:** Python
    
- **Machine Learning Framework:** PyTorch and torchvision
    
- **Optimization & Inference:** NVIDIA TensorRT (using `torch2trt` for optimized inference)
    
- **Interactive Development:** Jupyter Notebooks with ipywidgets for interactive data collection and model evaluation
    
- **Data Handling:** Custom dataset (`XYDataset`) to manage labeled image-coordinate pairs
    

**Robotics & Control:**

- **JetRacer Library:** Custom steering and throttle control calibrated through gain and bias adjustments
    
- **ROS2 integration** (potentially for extended control and telemetry, though not explicitly described)
    

---

#### **Challenges & Key Learnings**

Implementing the behaviour cloning RC car system involved overcoming several challenges:

- **Real-time Responsiveness:** Ensuring predictions were computed quickly enough for real-time autonomous driving.
    
- **Model Robustness:** Addressing overfitting and ensuring generalization through comprehensive data augmentation.
    
- **Steering & Throttle Calibration:** Fine-tuning steering gains and biases was critical to achieving smooth autonomous control.
    

Through this project, I significantly expanded my expertise in:

- Applying CNN architectures to practical robotics tasks.
    
- Real-time model inference optimization (TensorRT).
    
- Interactive machine learning development processes.
    
- Hardware and software integration for robotics applications.
    

---

#### **Outcome & Significance**

This project successfully demonstrated the feasibility and practicality of autonomous driving using behaviour cloning techniques. The RC car autonomously replicated human driving behaviours, validating the effectiveness of deep learning models trained purely on visual data. The implementation showcases significant potential applications in fields such as autonomous vehicles, robotics, and automated navigation systems, especially where traditional sensor-based approaches may be limited or expensive.

The experience gained from this project directly contributes to ongoing research and development in imitation learning, computer vision, and robotics integration, emphasizing the powerful capabilities of embedded AI systems such as NVIDIA Jetson.

### 3D Multi-Object Tracking: ROS2 Integration for Autonomous Vehicle Perception

This project involved developing and deploying a robust 3D multi-object tracking algorithm using the Kalman filter and integrating it into a ROS2 environment. Specifically designed for autonomous vehicle applications, this system tracks cars, pedestrians, and traffic signs in real-time, significantly enhancing the perception capabilities of self-driving systems.

---

#### **Project Overview & Goal**

**Introduction:** The primary aim was to enhance autonomous vehicle perception by accurately tracking multiple objects (vehicles, pedestrians, and traffic signs) in real-time using sensor data.

**Core Goals:**

- Implement a robust multi-object tracking algorithm suitable for 3D environments.
    
- Integrate the tracking system within ROS2 to facilitate real-time vehicle perception.
    
- Leverage sensor fusion to reliably track multiple classes of objects simultaneously.
    

---

#### **Approach: Kalman Filter & Data Association**

**Introduction:** This project employed an Adaptive 3D Bounding Box Multi-Object Tracking (AB3DMOT) algorithm, leveraging the Kalman filter for state estimation and the Hungarian algorithm for data association.

**Tracking Algorithm:**

- **Kalman Filter:** Used to predict and update the state (position, velocity, dimensions, and orientation) of objects based on noisy sensor measurements, providing smooth and accurate object trajectories.
    
- **Data Association:** The Hungarian matching algorithm associated detected objects to existing tracks, improving consistency in object identification across sensor frames.
    

**Sensor Integration:**

- Real-time object detections from camera and lidar sensors were utilized, fused, and processed for reliable tracking.
    

---

#### **Implementation Details & Technologies**

**Software & Tools:**

- **ROS2 Middleware:** Managed the communication between sensor inputs, tracking algorithm, and other system components.
    
- **Programming Languages:** Python, leveraging various scientific libraries for computational efficiency.
    
- **Tracking Library:** Custom-built implementation of AB3DMOT algorithm integrated directly with ROS2 nodes.
    
- **Visualization & Debugging:** Integrated debugging tools for real-time tracking visualization within the ROS ecosystem.
    

**Configuration & Tuning:**

- YAML-based configuration for flexible adjustment of parameters such as covariance models, matching thresholds, and prediction parameters.
    
- Easily tunable parameters to optimize tracking performance across various scenarios and object classes.
    

---

#### **Challenges & Key Learnings**

Developing this 3D tracking system posed several unique challenges:

- **Real-Time Performance:** Optimizing the algorithm for real-time processing was critical to maintaining accurate tracking in dynamic environments.
    
- **Sensor Fusion Accuracy:** Ensuring accurate data association between sensor types (camera and lidar) required careful calibration and validation.
    
- **Occlusion Handling:** Implementing robust mechanisms to maintain tracking accuracy even during partial or temporary occlusions.
    

Key skills developed through this project include:

- Advanced understanding of Kalman filters for dynamic state estimation.
    
- Expertise in data association algorithms, particularly the Hungarian method.
    
- Proficiency with ROS2 for integrating complex systems in autonomous vehicles.
    

---

#### **Outcome & Significance**

This project successfully demonstrated the integration of an advanced 3D tracking system within a ROS2 framework, providing significant improvements in the perception accuracy of autonomous vehicles. Real-time tracking of multiple object classes was reliably achieved, greatly enhancing the decision-making capabilities of autonomous systems. This implementation contributes valuable insights and practical solutions to ongoing research and development in autonomous vehicle perception, sensor fusion, and tracking technology.


### Custom Drone Build: Just for Fun

This was a fun and casual project where I designed and built a custom drone from scratch. It was primarily an opportunity to get hands-on experience with hardware, soldering, and assembling electronic components. Plus, it was a nice break from my usual software-focused projects!

Currently, the drone is grounded and waiting for its first real-world flight. In the meantime, I'm practicing flying virtually using FPV Skydive, a realistic drone flight simulator. This approach allows me to fine-tune my piloting skills without the risk of crashing my custom build.

---

#### **Why I Built It**

- To gain practical hardware experience.
    
- To improve my soldering and electronics assembly skills.
    
- Because drones are awesome and building things yourself is rewarding.
    

#### **What's Next**

- Continue honing my flying skills through FPV Skydive simulator.
    
- Prepare the drone for its first real-world flight when ready.
    

It's been a refreshing project, and I'm excited to eventually see it in action!