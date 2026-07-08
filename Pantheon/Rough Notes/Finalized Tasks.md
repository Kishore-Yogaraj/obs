### Difference Between Deliverables, Sub-Deliverables, Work Packages and Tasks

#### Deliverables
A deliverable is a major, high-level outcome or complete system that must be produced to achieve the project's objective. It represents a significant milestone or concrete chunk of the final project. 

#### Sub Deliverables
This is a smaller, more manageable component of a parent deliverable. It is used to break down complex systems into distinct subsystems or logical parts. Under the navigation deliverable the compute vision pipeline would would a sub deliverable. It defines a specific subsection of work but is still too large to easily estimate for cost and time, or to assign to a single person without breaking it down further.

#### Work Package
A work package is the lower element of a work breakdown structure branch. The absolute smallest unit of scope that you track. Its a specific, verifiable end-product.

#### Task
A task is a specific action item required to build a work package. Tasks do not belong in the WBS, instead they live in the project schedule, Gantt chart, or daily Kanban board. For our use case I wanted something all in one place so we will be able to access tasks in the WBS. Tasks are also time based. Until we have more time on our hands we will use this until we can make a Gantt chart.

**What is being built? (Scope)**
- Deliverable: The complete vehicle powertrain
- Sub-Deliverable: The battery management system
- Work Package: The low-voltage wiring harness

**How is it being built? (Execution)**
- Task 1: Pin out the connectors
- Task 2: Crimp the wire terminals
- Heat-shrink the insulation


### Main Deliverables
**Root: Autonomous Food Delivery Robot**
Bullet points are just the scope of what's needed for the project 

---

**1. Mechanical Platform**

The physical robot — everything structural.

- 1.1 Chassis Design (full CAD model)
    - Frame geometry, dimensions, weight capacity, ground clearance
    - Wheel and drivetrain mounting (motor brackets, axle housings)
    - Suspension design (if any — at minimum, vibration damping strategy)
    - Sensor mounting points (LiDAR, cameras, GPS antenna, ultrasonics)
    - Compute and electronics enclosure (weatherproof housing for Jetson, MCU, power board)
    - Food payload compartment (insulated bay, locking mechanism or lid)
    - Cable routing paths and access panels
    - Weatherproofing and drainage strategy (seals, gaskets, IP rating targets)
- 1.2 Chassis Fabrication & Assembly
    - Material sourcing and parts procurement
    - Frame fabrication (cutting, welding/fastening)
    - Drivetrain assembly (motors, wheels, belts/chains if applicable)
    - Enclosure fabrication and fitment
    - Payload compartment build-out

---

**2. Electrical & Power Systems**

Everything that carries current, minus the sensors themselves.

- 2.1 Power System
    - System-wide power budget (every consumer itemized with peak and nominal draw)
    - Battery selection and procurement (chemistry, capacity, voltage, form factor)
    - Power distribution board (design or select — fused outputs per subsystem)
    - Charging solution (connector type, charger spec, charge management)
    - Emergency stop (e-stop) circuit (hardwired kill switch cutting motor power)
- 2.2 Sensor & Compute Wiring
    - Cable and connector specification (pinouts, cable types, lengths)
    - Voltage regulation (level shifting / regulators between sensors and compute)
    - Wiring harness fabrication and installation
- 2.3 MCU Hardware
    - MCU selection finalized (Arduino Mega vs STM32)
    - MCU physically mounted and wired to motor drivers, encoders, ultrasonics
    - Communication link to Jetson (USB serial or Ethernet, physical cable)
- 2.4 Compute Platform Hardware
    - Jetson Orin NX physically installed in enclosure
    - NVMe SSD installed
    - All peripheral connections (USB, Ethernet, GPIO) routed and terminated

---

**3. Sensor Suite**

Procuring every sensor and getting them physically onto the robot and producing data.

- 3.1 Sensor Procurement
    - Final sensor BOM with exact part numbers, quantities, vendors
    - Purchase and receive all sensors (Livox Mid-360, OAK-D Pro, fisheyes, ZED-F9P, BNO085, ultrasonics, encoders)
- 3.2 Sensor Mounting & Electrical Integration
    - Mounting brackets designed and fabricated (or printed)
    - All sensors physically mounted to chassis
    - All sensors wired, powered, and confirmed producing raw data to Jetson

---

**4. Onboard Software Environment**

The software foundation that everything else runs on. Separating this out because it's a prerequisite for all software deliverables on hardware.

- 4.1 Jetson OS & ROS 2 Environment
    - JetPack / Ubuntu flashed and configured
    - ROS 2 installed with all required packages
    - Sensor drivers installed and verified (LiDAR, cameras, IMU, GPS)
    - Network configuration (SSH access, remote monitoring)
- 4.2 Robot Description (URDF/Xacro)
    - Full URDF model of the robot (links, joints, sensor frames, collision geometry)
    - TF tree verified (every sensor frame correctly defined relative to base_link)

---

**5. Simulation Environment**

The virtual testing platform that lets software development proceed without hardware.

- 5.1 Simulated Robot Model
    - URDF/SDF model in Gazebo (matching the real robot's geometry and sensor placement)
    - Simulated sensor plugins (LiDAR, stereo camera, fisheye cameras, IMU, GPS, encoders)
    - Simulated differential drive controller
- 5.2 Simulated World
    - Campus-like environment (sidewalks, roads, crosswalks, buildings, curbs)
    - Dynamic actors (pedestrians, cyclists, vehicles) for perception and planning testing
- 5.3 Full-Loop Sim Integration
    - Nav2 running in sim with simulated sensor feeds
    - End-to-end mission testing (spawn robot, give goal, robot navigates autonomously)

---

**6. Perception Pipeline**

All software that turns raw sensor data into an understanding of the environment.

- 6.1 LiDAR Processing
    - Point cloud preprocessing (filtering, downsampling, denoising)
    - Ground plane segmentation
    - Obstacle detection and clustering (bounding boxes for non-ground objects)
- 6.2 Camera Perception
    - Stereo depth processing (OAK-D Pro depth map generation)
    - Object detection model (pedestrians, vehicles, cyclists — inference on Jetson)
    - Traffic signal and crosswalk recognition (classification of walk/don't-walk signals, crosswalk line detection)
    - Fisheye distortion handling (undistortion or adapted detection for side cameras)
- 6.3 Sensor Fusion for Perception
    - 3D object map merging LiDAR obstacles with camera detections (matched, classified, tracked objects in a unified representation)

---

**7. Mapping & Localization**

Building the map the robot navigates on, and knowing where it is on that map in real time.

- 7.1 SLAM Pipeline
    - LIO-SAM or FAST-LIO configured and tuned
    - Teleoperated mapping procedure defined (drive routes, overlap strategy, loop closures)
    - Execute mapping runs covering the operational area
- 7.2 Map Post-Processing
    - Generate 2D occupancy grid from 3D point cloud
    - Annotate keepout zones (roads the robot shouldn't enter, restricted areas)
    - Annotate sidewalk vs road regions and crosswalk locations
    - Seasonal map refresh procedure documented
- 7.3 Localization System
    - EKF configured fusing LiDAR-inertial odometry, RTK-GPS, and wheel odometry
    - Localization validated against ground truth (accuracy and drift characterization)

---

**8. Navigation & Decision-Making**

Getting from A to B safely, and handling everything that happens along the way.

- 8.1 Nav2 Stack Configuration
    - Global planner (A*/Dijkstra on occupancy grid)
    - Local planner (DWA tuned for robot's velocity and footprint)
    - Costmap layers: static map, obstacle layer (from LiDAR), inflation layer, keepout filter
    - Recovery behaviors (spin, backup, wait, clear costmap)
- 8.2 Behavior Tree
    - Top-level mission logic (receive delivery request → plan route → navigate → arrive → deliver → return)
    - Crosswalk behavior subtree (stop at crossing, check for traffic via perception, proceed when safe)
    - Obstacle and stuck recovery subtrees
    - Delivery state machine (payload lock/unlock, user notification, confirmation)

---

**9. Low-Level Motor Control**

The firmware layer bridging the Jetson's velocity commands to physical wheel motion.

- 9.1 MCU Firmware
    - PID controllers for individual wheel velocity targets
    - Encoder reading and wheel odometry computation
    - Ultrasonic sensor polling and data publishing
    - E-stop monitoring and motor cutoff logic
- 9.2 ROS 2 Communication Bridge
    - micro-ROS or serial bridge between MCU and Jetson
    - cmd_vel subscriber (receiving velocity commands from Nav2)
    - Odometry and ultrasonic data publishers (sending back to Jetson)

---

**10. Non-Technical**

Everything that isn't on the robot but is required for the project to succeed.

- 10.1 Sponsorship & Outreach
    - Pitch deck (project overview, team, value proposition, ask)
    - Cold email templates (tailored for robotics companies, local businesses, component manufacturers)
    - Sponsor target list and outreach campaign (identify, send, track)
- 10.2 Project Website
    - Public-facing site (project description, team bios, progress blog)
- 10.3 Capstone Documentation
    - Project proposal
    - Progress reports (per your program's schedule)
    - Final report
    - Poster and/or demo preparation