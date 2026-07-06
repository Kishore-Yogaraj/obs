- Make a website for pantheon
	- Blog section
	- About us section
	- Update the website with information we have so far
- Initial sketches
- Mechanical Design
- Electrical Task Brief
- Source parts
- Agree on sensor selection
- Set up environment for for simulation
- Build pitch deck
- Cold email template


### How to Plan
Start from deliverables, not activities
- Brainstorming tasks bottom up is how you end up with an infinite list ("what could we work on"?)
- Find out what are the concrete things that need to exist for this robot to drive across campus to delivery food
	- Mechanical chassis
	- Wired and tested sensor suite
	- Working navigations stack
	- Project website 
	- Funding
- Deliverables are finite

Apply dependency logic
- Ask which ones block other ones?
- Can't integrate sensors until chassis exists and sensors are purchased
- Can't run SLAM until sensors are mounted and powered
- You can write a pitch deck before you have a chassis
- You can prototype ROS2 nodes in simulation before any hardware arrives

Distinguish between what's actionable now versus what's blocked
- A task is actionable right now if and only if it has zero unmet dependencies
- No parts, no mechanical design, sensor stack chosen
- Can't build the robot, but you can design it
- Can't test sensors but you can order them
- Can't run real SLAM but can get simulation environment running
- Can't cold email without a pitch deck but you can build a pitch deck

Size tasks to be completable, not just desirable
- Built the robot is a deliverable, not a task
- "Design the chassis mounting plate for the Livox Mid-360 in CAD" is a task and has a clear done state, one person can own it and you can estimate how long it takes
- If a task takes more than two weeks of work for one person its actually a group of tasks hiding 
- Only break down the stuff that's actionable in the near term
- Tasks three months out can stay coarse grained for now

Separate workstreams that can run in parallel
- Mechanical and electrical is bottle necked on designing
- Software is bottlenecked by not having hardware
- Outreach, website, and pitch deck have no dependencies

Apply a time horizon
- Don't plan the whole project at task level granularity right now
- Plan the next 3-4 weeks in detail (specific tasks, owners, deadlines), the next 2-3 months at the deliverable level (what milestones do we need to hit), and the rest of the project at the goal level (what does the final system look like)
- Planning horizon or rolling wave approach

Sequence summary
1. List your major deliverables (the things that need to exist)
2. Map dependencies between them (what blocks what)
3. Identify what's actionable today given your current state
4. Break only those near-term items into specific, ownable tasks
5. Assign based on parallel workstreams so no one is idle
6. Leave future work coarse - refine it as we go

### What needs to exist?
Not tasks yet. Just the things that need to exist for the robot to work and the project to success. Grouped by domain.

#### Mechanical
- Mechanical design (full CAD model of the chassis, enclosure, mounting)
- Fabricated and assembled chassis (the physical robot platform)

#### Electrical 
- Power system
	- Battery selection
	- Power distribution board
	- Charing solution
- Sensor interface wiring
	- Cables
	- Connectors
	- Voltage regulation between sensors and compute
- MCU system
	- Arduino Mega or STM32 running motor control
	- Encoders
	- Communicating with Jetson over ROS2
- Compute Platform
	- Jetson Orin NX Physically installed
	- OS configured
	- ROS 2 environment set up

#### Sensors
- Sensors procured (everything from stack actually purchased and in hand)
- Sensors mounted and electrically integrated (physically attached to chassis, wired, powered, producing data)

#### Software - Perception
- Camera perception pipeline (stereo + fisheye feeds into object detection, pedestrian detection, crosswalk/traffic signal recognition, all fused into a shared 3D object map)
- LiDAR processing pipeline (point cloud preprocessing, ground segmentation, obstacle extraction)

#### Software - Mapping and Localization
- Pre-mapped campus map (teleoperated SLAM run producing usable occupancy grid and 3D map, with keep out zones, sidewalk/road disambiguation)
- Localization system (EKF fusing LiDAR-inertial odometry, RTK GPS, wheel odometry for real-time pose estimation)

#### Software - Navigation and Decision Making
- Nav2 stack configured (global planner, local planner via DWA, costmap layers including keep out filter)
- Behavior tree (mission-level logic - route to destination, handle crosswalks, recovery behaviors, delivery state machine)

#### Software - Low level Control
- Motor control bridge (MCU firmware handling PID for wheel velocities, publishing encoder odometry, ROS2 micro-ROS or serial bridge to Jetson)
- Simulation environment (Gazebo or similar, with a campus like world, simulated sensor feeds, and Nav2 running in the loop so we can develop and test before hardware exists)

#### Non-Technical
- Project website (public-facing site with project description, team bios, blog for progress updates)
- Pitch deck (polished slide deck explaining the project, team, and what you're asking for, aimed at potential sponsors)
- Cold email templates (outreach copy tailored to different company types — robotics companies, local businesses, component manufacturers)
- Sponsorship/outreach campaign (actually identifying targets, sending emails, tracking responses)
- Capstone documentation (whatever your program requires — proposals, progress reports, final report, poster, demo)

### Dependency Mapping
Figure out what blocks what. Prioritization falls out here.

#### No dependencies
- Mechanical design — needs nothing but CAD software and decisions
- Simulation environment — needs nothing but a computer and ROS 2 installed
- Project website — needs nothing but someone willing to build it
- Pitch deck — needs nothing but knowledge of the project and a design tool
- Cold email templates — needs nothing but the pitch deck being at least drafted
- Capstone documentation (early stages like proposals) — can start now
- Sensors procured — depends only on money/budget approval, not on other deliverables
- Power system research and design — can begin the analysis, battery selection, and power budget now before the chassis exists

#### Depends on Mechanical Design 
- Fabricated/assembled chassis - can't build what hasn't been designed
- Sensor mounting plan - you need to know where things go on the chassis, though we can rough this our during mechanical design

#### Depends on chassis + sensors procured
- 
