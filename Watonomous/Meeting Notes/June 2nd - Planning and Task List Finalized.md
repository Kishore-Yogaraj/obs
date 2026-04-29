## Perception 
Goal of perception for the end of the term are as follows:
- **Get cameras publishing data (of correct encoding) to a ros2 topic**
	- We should be able to see camera visuals (see what the camera sees) on Rviz or foxglove
	- Docs on how to start the camera publishing node
- **Get lidars publishing data to a ros2 topic** 
	- We should be able to see lidar data on Rviz or Foxglove
	- Docs on how to run the lidar node
- **Get GPS publishing data to a ros2 topic**
	- We should be able to see GPS data (lat, lon, alt)
	- GPS (global positioning system) is one specific GNSS (Global Navigation Satellite System) that's developed and operated by the US
	- GNSS uses multiple satellite constellations from different countries to offer more reliable and accurate positioning than GPS alone due to its access to multiple systems
	- By default we get latitude, longitude and altitude, velocity (in 3D: North, East, Down components)
		- Ideally we should get all the data that the GPS can provide for us, **so this would mean latitude, longitude, altitude and velocity (in 3D: North, East, Down components)** 
		- Should be publishing as a ros message to a ros topic
		- Docs for how to run the GPS node should be written as well 
- **Object detection needs to be working and tested on the cameras on the car**
	- Docs on how to run this should be written as well at the package level
##### Priorities
- GPS data to publish ros2 messages
- Object detection working on car cameras
- Lidar outputs data as ros2 messages

## Localization 
Goals for localization for this term is as follows:
- Get localization working
	- This means that we are able to localize Eve accurately within a map in a real world environment (test track/parking lot)

**Task list in Order of Execution**
Everything can be don in Carla for localization from my research. This means we should be able to test everything in Sim and then test everything on the car when we are ready to do so

- **Get required data publishing from CARLA**
	- GPS
	- IMU
	- Wheel Odom
- **Find a way get ground truth data from CARLA** 
	- This is so that we can determine if we are localizing accurately
	- If our localization odom value is close to our ground truth value then we know we are localizing well
	- In CARLA you should be able to get the position of an object
- **Fuse data using the robot_localization package and perform localization using EKF**
	- After fusion we should be publishing odom readings that can be used by action to implement pure pursuit in sim 
	- A good way to test localization is to split it into 3 phases
	- **Phase 1:**
		- Set a known physical marker with precise GPS coordinates in CARLA
		- Initialize odom reading of starting position of car
		- Manually drive the car to the physical marker 
		- See if your localization output coordinate is the same as the actual coordinate or what the error percentage is
	- **Phase 2:**
		- Set 4-6 different physical markers with known GPS coordinates in a simple pattern like a square, straight line or triangle
		- Initialize localization system at a starting position 
		- Manually drive the waypoint sequence
		- At each waypoint, record what your localization system estimates in comparison to the actual physical marker coordinate
		- Here we are testing for accumulated error, turn accuracy, consistency, and path tracking
	- **Phase 3**
		- Full implementation with global planner and pure pursuit

##### Priorities
- Get localization working

## Mapping and Planning
Goal of Mapping and Planning for the end of the term is as follows:
- **Get the OSM file for the test track working**
	- The OSM file has the graph based representation of the test track
	- Lane let parses the OSM file and runs "router" which is a lane let method to create a global path 
	- The global path is a series of waypoints from the starting position of Eve to the end path
	- These waypoints should be GPS coordinates
	- Currently we have OSM files that can be used for testing of pure pursuit but we need the test track OSM file working 

##### Priorities
- Get the OSM file for the test track working
	- We should be able to generate global paths from one specified GPS coordinate (start position) to another specified GPS coordinate (end position)

## Action
Goal of Action for the end of the term is as follows:
- **Get pure pursuit controller working on the car**
	- This means integrating the pure pursuit algorithm to work on the waypoints provided by lane let
	- How do you plan to test this? Can you do it sim?
	- Do you need localization data to be able to do this or can you simulate localization as well?

##### Priorities
- Get pure pursuit controller working on the car



# Self-Driving Car Project Execution Plan

## Project Overview

This project aims to develop a complete autonomous vehicle system with four main subsystems: Perception, Localization, Mapping & Planning, and Action. The execution plan is organized by priority and dependencies.

---

## Phase 1: Foundation Systems (Weeks 1-4)

### Critical Path - High Priority Tasks

#### 1.1 GPS System Implementation

**Priority: CRITICAL** _(Required for localization and planning)_

**Execution Steps:**

1. Set up GPS hardware connection to ROS2
2. Create GPS publisher node
3. Implement message publishing for:
    - Latitude, longitude, altitude
    - 3D velocity (North, East, Down components)
4. Test GPS data accuracy and consistency
5. **Deliverable:** Complete documentation for GPS node operation

**Success Criteria:** GPS data visible in RViz/Foxglove with accurate positioning

---

#### 1.2 Camera System Setup

**Priority: HIGH** _(Foundation for object detection)_

**Execution Steps:**

1. Configure camera hardware with correct encoding
2. Create camera publisher node for ROS2
3. Verify camera feed quality and frame rate
4. Test camera visibility in RViz/Foxglove
5. **Deliverable:** Camera node documentation

**Success Criteria:** Clear camera visuals accessible through ROS2 topics

---

#### 1.3 LiDAR System Integration

**Priority: MEDIUM** _(Important for perception but not blocking other systems)_

**Execution Steps:**

1. Set up LiDAR hardware connection
2. Create LiDAR publisher node
3. Configure point cloud data publishing
4. Verify LiDAR visualization in RViz/Foxglove
5. **Deliverable:** LiDAR node documentation

**Success Criteria:** LiDAR point cloud data properly visualized

---

## Phase 2: Core Functionality (Weeks 5-8)

### 2.1 Object Detection Implementation

**Priority: CRITICAL** _(Essential for safe autonomous operation)_

**Dependencies:** Camera system (1.2) must be complete

**Execution Steps:**

1. Select and configure object detection model
2. Integrate detection algorithm with camera feed
3. Test detection accuracy on vehicle cameras
4. Optimize detection performance for real-time operation
5. **Deliverable:** Object detection package documentation

**Success Criteria:** Reliable object detection on car cameras with acceptable latency

---

### 2.2 CARLA Simulation Setup

**Priority: HIGH** _(Testing environment for localization)_

**Execution Steps:**

1. Install and configure CARLA simulator
2. Set up vehicle model in CARLA
3. Configure sensor suite in simulation:
    - GPS sensor
    - IMU sensor
    - Wheel odometry
4. Establish ground truth data extraction
5. Test sensor data publishing to ROS2

**Success Criteria:** Complete sensor suite operational in CARLA with ground truth reference

---

## Phase 3: Localization System (Weeks 9-12)

### 3.1 Data Fusion and EKF Implementation

**Priority: CRITICAL** _(Core localization capability)_

**Dependencies:** GPS (1.1) and CARLA setup (2.2) must be complete

**Execution Steps:**

#### Phase 3A: Basic Fusion

1. Install and configure robot_localization package
2. Set up Extended Kalman Filter (EKF) configuration
3. Fuse GPS, IMU, and wheel odometry data
4. Publish fused odometry data to ROS2 topic

#### Phase 3B: Single Point Validation

1. Set known GPS marker in CARLA
2. Initialize vehicle at starting position
3. Manually drive to marker location
4. Compare localization estimate vs. ground truth
5. Calculate and document error percentage

#### Phase 3C: Multi-Point Validation

1. Set 4-6 GPS markers in geometric pattern (square/triangle)
2. Drive waypoint sequence manually
3. Record localization estimates at each waypoint
4. Analyze accumulated error and consistency
5. Document turn accuracy and path tracking performance

**Success Criteria:** Localization accuracy within acceptable error margins for autonomous operation

---

## Phase 4: Planning System (Weeks 13-15)

### 4.1 OSM Integration and Path Planning

**Priority: HIGH** _(Required for autonomous navigation)_

**Dependencies:** Basic localization (3.1) should be functional

**Execution Steps:**

1. Obtain and validate test track OSM file
2. Configure Lanelet parser for OSM processing
3. Implement router functionality for global path generation
4. Test waypoint generation between GPS coordinates
5. Validate generated paths against track layout
6. **Deliverable:** OSM file and path planning documentation

**Success Criteria:** Reliable global path generation from start to end GPS coordinates

---

## Phase 5: Control System (Weeks 16-18)

### 5.1 Pure Pursuit Controller

**Priority: CRITICAL** _(Final integration for autonomous operation)_

**Dependencies:** Localization (3.1) and Planning (4.1) must be complete

**Execution Steps:**

#### Phase 5A: Simulation Testing

1. Implement pure pursuit algorithm
2. Integrate with Lanelet waypoints
3. Test controller in CARLA simulation
4. Tune controller parameters for smooth operation
5. Validate path following accuracy

#### Phase 5B: Vehicle Integration

1. Integrate pure pursuit with vehicle control systems
2. Test on controlled test track environment
3. Validate safety systems and emergency stops
4. Document controller performance and limitations

**Success Criteria:** Smooth autonomous navigation following planned paths

---

## Final Integration and Testing (Weeks 19-20)

### 6.1 System Integration

1. Complete end-to-end system testing
2. Performance validation in various scenarios
3. Safety system verification
4. Documentation completion
5. Final demonstration preparation

---

## Risk Mitigation and Contingencies

### High-Risk Items

- **Hardware Dependencies:** Ensure backup sensors and redundant systems
- **Weather Conditions:** Plan indoor testing alternatives
- **Integration Complexity:** Maintain modular approach for easier debugging

### Testing Strategy

- **Simulation First:** All components tested in CARLA before vehicle deployment
- **Incremental Testing:** Each phase builds on verified previous phases
- **Safety Protocols:** Emergency stop systems tested at each integration step

---

## Success Metrics

### Technical Metrics

- GPS accuracy: < 1 meter error
- Object detection: > 95% accuracy at 30fps
- Localization: < 0.5 meter cumulative error over 100m path
- Path following: < 0.3 meter lateral deviation from planned path

### Deliverables Checklist

- [ ] Complete ROS2 node documentation for all sensors
- [ ] Validated localization system with error analysis
- [ ] Working OSM file with path generation
- [ ] Functional pure pursuit controller
- [ ] End-to-end autonomous driving demonstration

---

## Timeline Summary

- **Weeks 1-4:** Foundation systems (GPS, cameras, LiDAR)
- **Weeks 5-8:** Object detection and simulation setup
- **Weeks 9-12:** Localization system development and validation
- **Weeks 13-15:** Path planning and OSM integration
- **Weeks 16-18:** Pure pursuit controller implementation
- **Weeks 19-20:** Final integration and testing



# Self-Driving Car Project Execution Plan

## Project Overview

This project aims to develop a complete autonomous vehicle system with four main subsystems: Perception, Localization, Mapping & Planning, and Action. The execution plan is organized by priority and dependencies.

## Team Structure
- **Perception Team:** Camera systems, LiDAR, GPS sensors, and object detection
- **Localization Team:** Data fusion, EKF implementation, and position estimation
- **Mapping & Planning Team:** OSM integration, path planning, and route generation
- **Action Team/VP:** Pure pursuit controller and vehicle control integration

---

## Phase 1: Foundation Systems (Weeks 1-4)

### Critical Path - High Priority Tasks

#### 1.1 GPS System Implementation

**Assigned Team: Perception**  
**Priority: CRITICAL** _(Required for localization and planning)_

**Execution Steps:**

1. Set up GPS hardware connection to ROS2
2. Create GPS publisher node
3. Implement message publishing for:
    - Latitude, longitude, altitude
    - 3D velocity (North, East, Down components)
4. Test GPS data accuracy and consistency
5. **Deliverable:** Complete documentation for GPS node operation

**Success Criteria:** GPS data visible in RViz/Foxglove with accurate positioning

---

#### 1.2 Camera System Setup

**Assigned Team: Perception**  
**Priority: HIGH** _(Foundation for object detection)_

**Execution Steps:**

1. Configure camera hardware with correct encoding
2. Create camera publisher node for ROS2
3. Verify camera feed quality and frame rate
4. Test camera visibility in RViz/Foxglove
5. **Deliverable:** Camera node documentation

**Success Criteria:** Clear camera visuals accessible through ROS2 topics

---

#### 1.3 LiDAR System Integration

**Assigned Team: Perception**  
**Priority: MEDIUM** _(Important for perception but not blocking other systems)_

**Execution Steps:**

1. Set up LiDAR hardware connection
2. Create LiDAR publisher node
3. Configure point cloud data publishing
4. Verify LiDAR visualization in RViz/Foxglove
5. **Deliverable:** LiDAR node documentation

**Success Criteria:** LiDAR point cloud data properly visualized

---

## Phase 2: Core Functionality (Weeks 5-8)

### 2.1 Object Detection Implementation

**Assigned Team: Perception**  
**Priority: CRITICAL** _(Essential for safe autonomous operation)_

**Dependencies:** Camera system (1.2) must be complete

**Execution Steps:**

1. Select and configure object detection model
2. Integrate detection algorithm with camera feed
3. Test detection accuracy on vehicle cameras
4. Optimize detection performance for real-time operation
5. **Deliverable:** Object detection package documentation

**Success Criteria:** Reliable object detection on car cameras with acceptable latency

---

### 2.2 CARLA Simulation Setup

**Assigned Team: Localization (Lead), with support from Perception**  
**Priority: HIGH** _(Testing environment for localization)_

**Execution Steps:**

1. Install and configure CARLA simulator
2. Set up vehicle model in CARLA
3. Configure sensor suite in simulation:
    - GPS sensor _(Perception team input)_
    - IMU sensor _(Localization team)_
    - Wheel odometry _(Localization team)_
4. Establish ground truth data extraction _(Localization team)_
5. Test sensor data publishing to ROS2 _(Both teams)_

**Success Criteria:** Complete sensor suite operational in CARLA with ground truth reference

---

## Phase 3: Localization System (Weeks 9-12)

### 3.1 Data Fusion and EKF Implementation

**Assigned Team: Localization**  
**Priority: CRITICAL** _(Core localization capability)_

**Dependencies:** GPS (1.1) and CARLA setup (2.2) must be complete

**Execution Steps:**

#### Phase 3A: Basic Fusion

1. Install and configure robot_localization package
2. Set up Extended Kalman Filter (EKF) configuration
3. Fuse GPS, IMU, and wheel odometry data
4. Publish fused odometry data to ROS2 topic

#### Phase 3B: Single Point Validation

1. Set known GPS marker in CARLA
2. Initialize vehicle at starting position
3. Manually drive to marker location
4. Compare localization estimate vs. ground truth
5. Calculate and document error percentage

#### Phase 3C: Multi-Point Validation

1. Set 4-6 GPS markers in geometric pattern (square/triangle)
2. Drive waypoint sequence manually
3. Record localization estimates at each waypoint
4. Analyze accumulated error and consistency
5. Document turn accuracy and path tracking performance

**Success Criteria:** Localization accuracy within acceptable error margins for autonomous operation

---

## Phase 4: Planning System (Weeks 13-15)

### 4.1 OSM Integration and Path Planning

**Assigned Team: Mapping & Planning**  
**Priority: HIGH** _(Required for autonomous navigation)_

**Dependencies:** Basic localization (3.1) should be functional

**Execution Steps:**

1. Obtain and validate test track OSM file
2. Configure Lanelet parser for OSM processing
3. Implement router functionality for global path generation
4. Test waypoint generation between GPS coordinates
5. Validate generated paths against track layout
6. **Deliverable:** OSM file and path planning documentation

**Success Criteria:** Reliable global path generation from start to end GPS coordinates

---

## Phase 5: Control System (Weeks 16-18)

### 5.1 Pure Pursuit Controller

**Assigned Team: Action (Lead), with support from Localization**  
**Priority: CRITICAL** _(Final integration for autonomous operation)_

**Dependencies:** Localization (3.1) and Planning (4.1) must be complete

**Execution Steps:**

#### Phase 5A: Simulation Testing

1. Implement pure pursuit algorithm _(Action team)_
2. Integrate with Lanelet waypoints _(Action + Mapping & Planning coordination)_
3. Test controller in CARLA simulation _(Action team)_
4. Tune controller parameters for smooth operation _(Action team)_
5. Validate path following accuracy _(Action + Localization coordination)_

#### Phase 5B: Vehicle Integration

1. Integrate pure pursuit with vehicle control systems _(Action team)_
2. Test on controlled test track environment _(Action team with safety oversight)_
3. Validate safety systems and emergency stops _(Action team)_
4. Document controller performance and limitations _(Action team)_

**Success Criteria:** Smooth autonomous navigation following planned paths

---

## Final Integration and Testing (Weeks 19-20)

### 6.1 System Integration

**Assigned Team: All teams (coordinated integration)**

**Team Responsibilities:**

- **Perception:** Ensure sensor data quality and object detection reliability
- **Localization:** Validate position accuracy during full system operation
- **Mapping & Planning:** Verify path generation under dynamic conditions
- **Action:** Lead integration testing and safety validation

**Execution Steps:**

1. Complete end-to-end system testing _(All teams)_
2. Performance validation in various scenarios _(All teams)_
3. Safety system verification _(VP, all teams support)_
4. Documentation completion _(All teams for their subsystems)_
5. Final demonstration preparation _(All teams)_

---

## Risk Mitigation and Contingencies

### High-Risk Items

- **Hardware Dependencies:** Ensure backup sensors and redundant systems
- **Weather Conditions:** Plan indoor testing alternatives
- **Integration Complexity:** Maintain modular approach for easier debugging

### Testing Strategy

- **Simulation First:** All components tested in CARLA before vehicle deployment
- **Incremental Testing:** Each phase builds on verified previous phases
- **Safety Protocols:** Emergency stop systems tested at each integration step

---

## Success Metrics

### Technical Metrics

- GPS accuracy: < 1 meter error
- Object detection: > 95% accuracy at 30fps
- Localization: < 0.5 meter cumulative error over 100m path
- Path following: < 0.3 meter lateral deviation from planned path

### Deliverables Checklist

- [ ] Complete ROS2 node documentation for all sensors
- [ ] Validated localization system with error analysis
- [ ] Working OSM file with path generation
- [ ] Functional pure pursuit controller
- [ ] End-to-end autonomous driving demonstration

---

## Timeline Summary

- **Weeks 1-4:** Foundation systems
    - _Perception:_ GPS, cameras, LiDAR setup
    - _Localization:_ CARLA simulation preparation
- **Weeks 5-8:** Core functionality development
    - _Perception:_ Object detection implementation
    - _Localization:_ CARLA sensor integration and ground truth setup
- **Weeks 9-12:** Localization system validation
    - _Localization:_ EKF implementation and multi-phase testing
    - _Mapping & Planning:_ Begin OSM file preparation
- **Weeks 13-15:** Path planning implementation
    - _Mapping & Planning:_ OSM integration and route generation
    - _Action:_ Pure pursuit algorithm development
- **Weeks 16-18:** Control system integration
    - _Action:_ Controller testing and vehicle integration
    - _All teams:_ Cross-system integration support
- **Weeks 19-20:** Final integration and testing
    - _All teams:_ Coordinated system validation and demonstration

## Team Coordination Points

### Weekly Sync Requirements

- **Weeks 1-4:** Perception and Localization weekly sync for CARLA integration
- **Weeks 9-12:** Localization and Mapping & Planning coordination for GPS waypoint validation
- **Weeks 13-18:** All teams weekly sync for integration planning
- **Weeks 16-20:** Daily standups for final integration phase

### Critical Handoff Points

1. **Week 4:** Perception → Localization (GPS data format and accuracy validation)
2. **Week 8:** Localization → Mapping & Planning (Coordinate system standardization)
3. **Week 12:** Localization → Action (Odometry data interface specification)
4. **Week 15:** Mapping & Planning → Action (Waypoint format and delivery method)

### Inter-team Dependencies
- **Perception ↔ Localization:** GPS data format, coordinate frames, sensor calibration
- **Localization ↔ Mapping & Planning:** Coordinate system alignment
- **Mapping & Planning ↔ Action:** Waypoint format, path update frequency, replanning triggers
- **All teams:** ROS2 message standards, and coordinate frame conventions