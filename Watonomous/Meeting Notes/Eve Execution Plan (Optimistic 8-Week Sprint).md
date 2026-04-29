## Project Overview
This project aims to develop a complete autonomous vehicle system with four main subsystems: Perception, Localization, Mapping & Planning, and Action. The execution plan is organized by priority and dependencies within an aggressive 8-week timeline.

## Team Structure
- **Perception Team:** Camera systems, LiDAR, GPS sensors, and object detection
- **Localization Team:** Data fusion, EKF implementation, and position estimation
- **Mapping & Planning Team:** OSM integration, path planning, and route generation
- **Action Team/VP:** Pure pursuit controller and vehicle control integration

---

## Week 1-2: Foundation Systems & Critical Path Setup

### Week 1: Core Sensor Integration
**Focus: Parallel development of critical foundation systems**

#### GPS System Implementation (Priority 1)
**Assigned Team: Perception**  
**Execution Steps:**
- Set up GPS hardware connection to ROS2
- Create GPS publisher node with full data output (lat, lon, alt, 3D velocity)
- Test GPS data accuracy and basic functionality
- **Deliverable:** GPS node operational with ROS2 publishing

#### CARLA Simulation Setup (Priority 1) **This should be set up in monorepo**
**Assigned Team: Localization (Lead)  
**Execution Steps:**
- Install and configure CARLA simulator
- Set up basic vehicle model and sensor suite (GPS, IMU, wheel odom)
- Establish ground truth data extraction capability
- **Deliverable:** CARLA environment ready for testing

### Week 2: Sensor Suite Completion
**Focus: Complete all sensor systems for perception pipeline**

#### Camera & LiDAR Integration
**Assigned Team: Perception**  
**Execution Steps:**
- Configure camera hardware with correct encoding
- Set up LiDAR publisher node and point cloud data
- Verify all sensor visibility in RViz/Foxglove
- **Deliverable:** Complete sensor documentation and operational nodes

#### Object Detection Setup
**Assigned Team: Perception**  
**Execution Steps:**
- Select and begin integration of object detection model
- Test detection algorithm with camera feed
- Initial performance optimization
- **Deliverable:** Basic object detection operational

---

## Week 3-4: Core Functionality Development
### Week 3: Localization Foundation
**Focus: Data fusion and basic localization capability**

#### EKF Implementation (Phase 1)
**Assigned Team: Localization**  
**Dependencies:** GPS system and CARLA setup complete 
**Execution Steps:**
- Install and configure robot_localization package
- Set up Extended Kalman Filter configuration
- Begin GPS, IMU, and wheel odometry data fusion
- **Deliverable:** Basic EKF publishing fused odometry data

#### Object Detection Optimization
**Assigned Team: Perception**  
**Execution Steps:**
- Optimize detection performance for real-time operation
- Test detection accuracy on vehicle cameras
- Integration with ROS2 message publishing
- **Deliverable:** Production-ready object detection system

### Week 4: Localization Validation
**Focus: Single-point and basic multi-point localization testing**

#### Localization Testing (Phases 2A & 2B)
**Assigned Team: Localization**  
**Execution Steps:**
- Single point validation: Set known GPS marker in CARLA, test accuracy by manually driving to known GPS marker in CARLA
- Begin multi-point validation with 3-4 waypoints
- Document error analysis and accuracy metrics
- **Deliverable:** Validated localization system with documented accuracy

#### OSM File Preparation
**Assigned Team: Mapping & Planning**  
**Execution Steps:**
- Obtain and begin validation of test track OSM file
- Start Lanelet parser configuration
- **Deliverable:** OSM file ready for integration

---

## Week 5-6: Advanced Integration & Planning

### Week 5: Path Planning Implementation
**Focus: Complete mapping and planning capability**

#### OSM Integration & Route Generation
**Assigned Team: Mapping & Planning**  
**Dependencies:** Basic localization functional 
**Execution Steps:**
- Complete Lanelet parser configuration for OSM processing
- Implement router functionality for global path generation
- Test waypoint generation between GPS coordinates
- **Deliverable:** Functional path planning system

#### Pure Pursuit Algorithm Development
**Assigned Team: Action**  
**Execution Steps:**
- Implement core pure pursuit algorithm
- Begin integration planning with Lanelet waypoints
- **Deliverable:** Pure pursuit algorithm ready for testing

### Week 6: Controller Integration & Testing
**Focus: Control system integration and simulation testing**

#### Pure Pursuit Simulation Testing
**Assigned Team: Action (Lead), with Localization support**  
**Dependencies:** Localization and Planning systems functional 
**Execution Steps:**
- Integrate pure pursuit with Lanelet waypoints
- Test controller in CARLA simulation
- Initial parameter tuning for smooth operation
- **Deliverable:** Controller operational in simulation

#### Multi-Point Localization Completion
**Assigned Team: Localization**  
**Execution Steps:**
- Complete multi-point validation (4-6 waypoints)
- Analyze accumulated error and path tracking performance
- Final localization system validation
- **Deliverable:** Production-ready localization system

---

## Week 7-8: System Integration & Validation

### Week 7: Vehicle Integration
**Focus: Real vehicle testing and system integration**

#### Vehicle Controller Integration
**Assigned Team: VP and Action, All teams support**  
**Execution Steps:**
- Integrate pure pursuit with vehicle control systems
- Initial controlled test track testing
- Safety system validation and emergency stops
- **Deliverable:** Vehicle-ready control system

#### End-to-End System Testing
**Assigned Team: All teams coordinated**  
**Execution Steps:**
- Complete sensor-to-action pipeline testing
- Cross-system integration validation
- Performance testing in controlled scenarios
- **Deliverable:** Integrated autonomous system

### Week 8: Final Validation & Demonstration
**Focus: Performance validation and demonstration preparation**
#### System Validation & Documentation
**Assigned Team: All teams**  
**Execution Steps:**
- Comprehensive system performance validation
- Safety system verification and stress testing
- Complete documentation for all subsystems
- Final demonstration preparation and rehearsal
- **Deliverable:** Complete autonomous driving system ready for demonstration

---
## Compressed Timeline Strategy

### Parallel Development Approach
- **Weeks 1-2:** Maximum parallelization - all teams work independently on foundation systems
- **Weeks 3-4:** Begin integration while continuing parallel development
- **Weeks 5-6:** Heavy integration focus with coordinated development
- **Weeks 7-8:** Full integration and validation mode

### Risk Mitigation for Accelerated Timeline
- **Rapid Prototyping:** Focus on working solutions over perfect implementations
- **Continuous Integration:** Test integration points as soon as components are ready
- **Fallback Options:** Maintain simulation-only demonstration as backup plan

---
## Critical Success Factors

### Week-by-Week Milestones
- **Week 1:** GPS operational, CARLA ready
- **Week 2:** All sensors operational, basic object detection working
- **Week 3:** EKF fusion working, object detection production-ready
- **Week 4:** Localization validated, OSM file ready
- **Week 5:** Path planning operational, pure pursuit algorithm complete
- **Week 6:** Controller working in simulation, localization fully validated
- **Week 7:** Vehicle integration complete, end-to-end testing successful
- **Week 8:** System validated and demonstration-ready

### Success Metrics (Adjusted for 8-week timeline)
- **GPS accuracy:** < 2 meter error (relaxed for speed)
- **Object detection:** > 90% accuracy at 25-30fps (balanced performance/speed)
- **Localization:** < 1 meter cumulative error over 50m path (practical target)
- **Path following:** < 0.5 meter lateral deviation (achievable target)

### Inter-team Dependencies
- **Perception ↔ Localization:** GPS data format, coordinate frames, sensor calibration
- **Localization ↔ Mapping & Planning:** Coordinate system alignment
- **Mapping & Planning ↔ Action:** Waypoint format, path update frequency, replanning triggers
- **All teams:** ROS2 message standards, and coordinate frame conventions
