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
- Sensors mounted and electrically integrated - need the physical robot and the physical sensors
- Compute platform installed - need somewhere to put the Jetson
- Sensor interface wiring - need the chassis, sensors, and the power system to come together
- MCU system (physical prototype) - need motors mounted on a chassis to control

#### Depends on sensors integrated and producing data
- Camera perception pipeline (real hardware testing) - though initial model work can happen in simulation earlier
- LiDAR processing pipeline (real data) - same caveat, simulation first
- Pre-mapped campus map - need the robot physically driving around with working LiDAR and odometry

#### Depends on campus map existing
- Nav2 stack configured (real deployment) - needs a real map to plan on, though we can configure and test it entirely in simulation before that

#### Depends on Nav2 + perception + behavior logic all working
- Behavior tree (full integration) - this is where everything converges: perception feeds into the behavior tree, which commands Nav2, which sends velocity commands through the motor control bridge

#### Depends on pitch deck
- Cold email templates - hard to write outreach without knowing what you're pitching
- Sponsorship campaign - needs both the templates and the pitch deck

**Critical Path** - the longest chain of dependencies that determines our overall timeline 

Mechanical design to chassis fabrication to sensor integration to real sensor data to campus mapping to Nav2 deployment to full behavior integration to field testing

Everything else will wither feed into this chain or run parallel to it. This is important because it tells us that mechanical design is the single most urgent technical deliverable. Every week it slips, the entire downstream chain slips with it

There's also a completely independent parallel track: website to pitch deck to outreach to sponsorship. This work doesn't touch the critical path at all, which makes it perfect for team members who aren't doing mechanical or software design

Simulation track. Every software deliverable has a simulation first version that can be developed right now, independent of hardware. This is the software team's lifeline while the physical robot doesn't exist yet.

**Three parallel workstreams**
1. Hardware pipeline - mechanical design to electrical to fabrication to integration
2. Software in simulation - sim environemnt to perception prototyping to Nav2 config to behavior tree logic
3. Outreach and documentation - website, pitch deck, emails, capstone docs

### What's actionable right now?

Given our current state - no parts, no mechanical design, sensor stack selected, team just forming. We can go through each workstream and identify what has zero unmet dependencies today.

#### Hardware pipeline
Mechanical design is the critical path bottleneck, so it's the highest priority technical item. "Do the mechanical design" is too big. What can actually start as soon as possible?

- Decide on base platform strategy - designing from scratch, modifying an existing platform like a power wheelchair base or a Husky-style chassis, or buying a commercial frame and building around it? This decisions should unblock everything else in mechanical
- Begin chassis requirements spec - dimensions needed for sensor placement, weight budget, ground clearance for snow, wheel size and type for traction, weatherproofing approach. This can be written before any CAD work starts and it informs the CAD work
- Start the power budget - list every component, its voltage and current draw, compute peak versus continuous loads. This doesn't require having parts in hand, just datasheets. It feeds directly into battery selection and power distribution design
- Begin sensor procurement - stack is chosen, so the actual purchasing process can start. Lead times matter. If the Livox Mid-360 takes three weeks to ship, that's three weeks added to the critical path if we don't order soon

#### Software in Simulation
The whole track exists specifically because the software team has nothing physical to work with yet. This means everything here is actionable immediately.

- Set up the simulation environemnt - install ROS 2, Gazebo, get a basic robot model (placeholder URDF with right sensor locations) spawned in a world. A flat world with some obstacles is fine to start. Campus-like fidelity can come later.
- Get Nav2 running in simulation - once the sim exists, configure Nav2 with a simulated LiDAR and a simple map. This is well documented in the Nav2 tutorials. Getting a robot to plan and follow a path in Gazebo is a first meaningful milestone
- Start the perception pipeline in simulation - spawn a simulated camera, run a basic object detection model, confirm you can get bounding boxes in ROS 2 topics. Real world accuracy comes later, but the infrastructure and message plumbing should be built now

#### Outreach and documentation
Zero technical dependencies on any of this. It can start today.

- Build the project website - even a simple one-pager with project description, team, and a blog section. We want this to exist before we start emailing companies so we have something to link to
- Draft the pitch deck - what the project is, who the team is, what you're building, what you need (money, parts, mentorship), and what the sponsor gets in return (logo on robot, mentions in blog, capstone poster visibility)
- Write cold email templates - once the pitch deck draft exists, write 2-3 emails variants for different audiences: robotics/tech companies, local restaurants who might partner for delivery testing, and component manufacturers who might donate or discount parts
- Start the capstone proposal - whatever your program requires for the initial deliverable, it can be worked on now since we already know the project


#### Not actionable right now
Should not appear on anyone's task list yet:
- Any physical fabrication or assembly
- Sensor mounting or wiring
- MCU firmware development (no motors to control)
- Real-world SLAM or mapping
- Campus map creation
- Real-world Nav2 deployment
- Full behavior tree integration testing

### Break into specific ownable tasks
Each task will have a clear name, what "done" looks like, and a rough effort estimate

#### Hardware pipeline

**H1: Base platform decision**
Decide if we are designing from scratch, modifying an existing platform, or building around a commercial base. Done means we have written decision with reasoning - "we're going with X approach because Y." This unblocks all subsequent mechanical CAD work. Should be a group discussion, but someone needs to own researching the options and presenting them.

Effort: 3-5 days of research, one meeting to decide

**H2: Chassis requirements spec**
A document listing every physical constraint the chassis needs to satisfy. Overall dimensions (fit on a sidewalk, fit through doors if needed), weight budget with margin, ground clearance for snow and curbs, wheel configuration and size, where each sensor needs to be mounted and what sightlines it needs, weatherproofing requirements, and access panels for maintenance. Done means the mechanical designer can open CAD and start working without needing to ask "how big should this be."

Effort: ~1 week, dependent on H1 being resolved

**H3: Power budget spreadsheet**
A spreadsheet listing every electrical component - Jetson, Livox Mid-360, OAK-D Pro, fisheyes, RTK GPS, BNO085, MCU, motors - with voltage requirements, typical current draw, and peak current draw, all pulled from datasheets. Sum it up for continuous and peak system draw. Done means you have a total wattage number and voltage rail map that tells you what batter capacity you need and how many voltage regulators or converters are required.

Effort: 3-4 days, can start immediately since it only needs datasheets

**H4: Order sensors and compute**
Actually place purchase orders for the sensor stack and the Jetson Orin NX. Done means orders are placed and we have expected delivery dates. This is urgent specifically because lead times sit on the critical path - every day you delay ordering is a day added to the integration timeline.

Effort: 1-2 days of actual work, but may depend on budget once determined

**H5: Sensor interface specification**
For each sensor, document: what connector it uses, what protocol it speaks (USB3, Ethernet, SPI, I2C, UART), what voltage it needs, what ROS 2 driver package exists for it, and any known quirks. Done means the electrical team member has a clear picture of every cable, connector, and driver that needs to exist. This will directly feed into the wiring harness design later.

Effort: 3-4 days, can be done in parallel with H3 since it's also just datasheet work

#### Software in Simulation

**S1: ROS 2 and Gazebo Environment Setup**
Install ROS2 Humble, gazebo and all Nav2 dependencies on a development machine. Create a basic ROS 2 workspace for the project. Done means you can run `ros2 launch` and get Gazebo open with an empty world.

Effort: 1-2 days if no major installation issues

**S2: Robot URDF model**
Build a rough URDF or xacro model of the robot. It doesn't need to match the final chassis - it needs to have the right wheelbase approximately, and simulated sensor plugins for a LiDAR (match the Livox's FOV roughly), a front stereo camera, and an IMU. Done means you can spawn this robot in Gazebo and see sensor topics publishing in ROS 2

Effort: 3-5 days. Fiddly work but well documented in ROS 2 tutorials

**S3: Nav2 basic navigation in simulation**
Using the URDF robot in simple Gazebo world, configure Nav2 with a static map, get the robot planning a path from A to B and driving it. Done means you can click a goal pose in RViz and the robot navigates to it, avoiding static obstacles.

Effort: ~1 week, depends on S1 and S2 being done. Again, Nav2 tutorials cover this almost step by step.

**S4: Basic perception prototype**
Get a camera feed from the simulated robot into a ROS 2 node running a pretrained object detection model (YOLOv8 is fine). Done means bounding boxes are being published as ROS 2 messages and you can visualize them in RViz overlaid on the camera image. Accuracy and real-world performance don't matter yet - this is more about building the message pipeline


