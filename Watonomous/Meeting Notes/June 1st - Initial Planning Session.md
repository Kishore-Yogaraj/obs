https://github.com/kuzen/Awesome-Self-Driving
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

#### Novatel PwrPak7 GNSS Technical Specifications
![[Pasted image 20250601200908.png]]

### 🔹 RTK Accuracy: `1 cm + 1 ppm`
**What it means:**  
RTK (Real-Time Kinematic) is a way for the GPS to get super accurate.
- Normally, GPS is off by a few meters (like a car parked in the wrong spot).
- **With RTK**, the GPS can be accurate **within 1 centimeter**—almost as exact as your fingernail!
- The `1 ppm` part just means: for every 1,000 meters (1 km) between you and a base station, add 1 more millimeter of possible error.
- **Initial time** < 10 seconds: It locks in quickly.
- **Initial reliability** > 99.9%: It’s super trustworthy once locked in.

### 🔹 GPS: `L1 C/A, L1C, L2C, L2P, L5`
**What it means:**  
These are the **frequencies** or "radio channels" GPS satellites use to talk to your device.
- Think of it like your GPS having the ability to listen to **more radio stations**.
- L1, L2, and L5 are just different stations.
- More signals = better accuracy and less chance of losing track.
- “C/A” and “P” are types of messages. C/A is for civilians, P is more precise.

### 🔹 GLONASS: `L1 C/A, L2C, L2P, L3, L5`
**What it means:**  
GLONASS is Russia’s version of GPS.
- It also uses L1, L2, etc.—just like GPS, but on different frequencies.
- This GPS listens to American satellites AND Russian satellites. That gives it a better chance of always knowing where you are.

### 🔹 BeiDou: `B1, B2, B3`
**What it means:**  
BeiDou is China’s satellite system.
- B1, B2, B3 are their special signals (like different radio channels too).
- More satellites = better sky coverage and more accurate fixes.

### 🔹 Galileo: `E1, E5 AltBOC, E5a, E5b, E6`
**What it means:**  
Galileo is Europe’s GPS system.
- E1, E5, and E6 are like the “L1, L2” but from European satellites.
- “AltBOC” is a fancy way of sending more data in one signal, like HD radio.

### 🔹 QZSS: `L1 C/A, L1C, L2C, L5, L6`
**What it means:**  
QZSS is Japan’s satellite system.
- It’s really good at making GPS work better in places like cities with tall buildings.
- Same idea: more signals from more places = better GPS.

### 🔹 Single Point L1: `1.5 m`
**What it means:**  
If your GPS is using only **one signal** (L1) from satellites, it will usually be off by about **1.5 meters**. That’s like being off by the length of a big dining table.

### 🔹 Single Point L1/L2: `1.2 m`
**What it means:**  
If your GPS uses **two signals** (L1 and L2), it gets better at cutting through bad weather and reflections, and it improves accuracy to **1.2 meters**. Still off a bit, but better!

## Localization 
Goals for localization for this term is as follows:
- Get localization working
	- This means that we are able to localize ourselves accurately within a map in a real world environment
**Task List in Order of Execution**
Everything can be done in Carla for localization. This means we should be able to test everything in Sim and then test everything on the car when we are ready to do so. 
- **Get required data publishing from CARLA**
	- GPS
	- IMU
	- Wheel Odom
- **Find a way get ground truth data from CARLA** 
	- This is so that we can determine if we are localizing accurately
	- If our localization odom value is close to our ground truth value then we know we are localizing well
- **Fuse data using the robot_localization package and perform localization using EKF**
	- After fusion we should be publishing odom readings that can be used by action to implement pure pursuit 
- **Get data from all sensors required for localization as ros messages
	- GPS data
	- IMU data
	- Wheel odometry data
- **Fuse data from all sensors using the robot_localization package and perform localization using EKF with real sensors**
	- We should get some odom reading from the localization output from this package 
- **Testing localization in a real environment**

## Test plan breakdown for localization in the real world with Eve

### Phase 1
**Setup:**
1. Use RTK GPS to mark a precise target coordinate in parking lot
2. Start robot/car at different location
3. Initialize localization system with onboard GPS reading
4. Manually drive to the RTK-marked target location
5. Compare: What does your localization system think vs. RTK truth

**Easy extensions**
- **Multiple targets**: Drive to 3-4 RTK-marked points in sequence
- **Different distances**: Test both near (50m) and far (200m) targets
- **Different starting orientations**: Face different directions when initializing

### Phase 2
**Setup:**
1. Use RTK GPS to survey and mark 4-6 waypoints in a simple pattern (square, triangle, or straight line)
2. Space waypoints 20-50 meters apart
3. Create waypoint file with RTK-surveyed coordinates

**Execution:**
1. Initialize localization system at starting position
2. **Manually drive** the waypoint sequence (still with driver)
3. At each waypoint, stop and record:
    - What your localization system estimates
    - RTK GPS ground truth position
4. Complete the full sequence and return to start

### What You're Testing
- **Accumulated error**: Does error grow as you drive longer distances?
- **Turn accuracy**: How well does the system handle direction changes?
- **Consistency**: Do you get similar results when repeating the route?
- **Path tracking**: Is the estimated path reasonable between waypoints?

### Phase 3
**Setup:**
- Use the same RTK-surveyed waypoints from Phase 2
- Start with the simplest pattern (straight line or triangle)
- Begin with conservative speeds (1-2 m/s)

**Execution:**
1. Initialize localization system at starting position
2. Load waypoint file into autonomous navigation system
3. **Robot drives autonomously** to each waypoint in sequence
4. Monitor remotely with emergency stop ready
5. Record performance at each waypoint using RTK ground truth

### What You're Adding
- **Path planning**: Robot chooses how to get between waypoints
- **Control system**: Robot steers and accelerates itself
- **Decision making**: Robot determines when it has "reached" a waypoint
- **System integration**: All components working together autonomously

### Key Validation Points
- **Waypoint accuracy**: How close does robot get to each target?
- **Path efficiency**: Does robot take reasonable routes between points?
- **Completion rate**: Does robot successfully reach all waypoints?
- **Behavior**: Smooth driving or erratic movements?

### Localization breakdown
**What is the goal of localization?**
You're essentially implementing **sensor fusion** to estimate the vehicle's pose (`x`, `y`, `heading`, and optionally velocity) with more accuracy than GPS alone.

**Sensors Involved:**
- **GPS (GNSS)**: Provides global position (lat, lon, alt) – sparse but absolute
- **Wheel Odometry**: Gives relative motion (distance, speed, yaw rate) – accurate short term but drifts
- **IMU**: Measures acceleration and angular velocity – high-rate data for motion dynamics

Together, these are commonly fused in an **Extended Kalman Filter (EKF)** or **Unscented Kalman Filter (UKF)** for localization (which is handled by the **robot_localization** package).

### Software Stack
**Sensors:**
- gps_driver: publishes `sensor_msgs/NavSatFix`
- imu_driver: publishes `sensor_msgs/Imu`
- wheel_encoder_node: publishes `nav_msgs/Odometry` usually computed from encoders

**Transforms Nodes**
- `robot_state_publisher`: Publishes frame relationships (`base_link`, `odom`, etc.)
- `tf2`: Handles the coordinate transforms

**Localization node**
- Use **robot localization** package’s `ekf_node`:
    - Input: GPS, IMU, and wheel odometry
    - Output: `nav_msgs/Odometry` in `odom` frame
- Optionally use `navsat_transform_node` to fuse GPS into the EKF and transform into map frame

### How does localization work
**Localization** answers a simple but crucial question:

> “Where am I right now on the map?”

In robotics and self-driving cars, localization means estimating the vehicle’s **pose**:
- **Position**: `x`, `y`, (sometimes `z`)
- **Orientation**: yaw (heading), pitch, roll

But each sensor (GPS, odometry, IMU) has **strengths and weaknesses**. So instead of trusting just one, we combine them using **sensor fusion**—typically via a **Kalman Filter**.

### Each Sensor's role
**GPS**
- Provides **global position** (latitude, longitude, altitude)
- Good for absolute location
- **Weaknesses**: Low update rate (1–10 Hz), drifts near tall buildings, loses accuracy under trees
Think of GPS as the "ground truth" for where you are—**slow but absolute**.

**Wheel Odometry**
- Uses wheel encoders to measure how far each wheel turned
- Computes relative motion (how much the car moved forward, turned, etc.)
- **Weaknesses**: Errors accumulate over time (wheel slip, miscalibration)

**IMU**
- Measures acceleration (linear) and rotation (angular velocity)
- Can estimate changes in orientation and movement
- **Weaknesses**: Drifts quickly without correction (especially cheap IMUs)

### Sensor Fusion
We combine GPS, odometry, and IMU using a **filter**, most commonly:
- **Extended Kalman Filter (EKF)**
- **Unscented Kalman Filter (UKF)**

This fusion process:
1. **Predicts** vehicle movement using IMU + odometry (fast and frequent)
2. **Corrects** that prediction using GPS (slow but globally accurate)
3. Keeps a consistent estimate of your current position and orientation

### 🔄 In real-time:

- IMU: “I think I moved forward 5 cm and turned 2 degrees”
- Odometry: “Yep, my wheels moved forward about that far”
- GPS: “You’re here—but slightly off. Let me fix that.”
- Filter: “Thanks guys. Based on everyone’s opinion, I’m here.”


### GPS course correction for adjusting IMU orientation
## Basic Principle
When you move from one GPS position to another, the direction of that movement tells you which way you were heading. This gives you an independent estimate of orientation that you can compare with your IMU.

## The Math
**Given two consecutive GPS positions:**
- Previous position: (x₁, y₁)
- Current position: (x₂, y₂)

**Calculate the course/heading:**
```
Δx = x₂ - x₁
Δy = y₂ - y₁
GPS_heading = atan2(Δy, Δx)
```

This gives you the direction of travel between those two GPS fixes.

## How It Corrects IMU
**Compare estimates:**
- IMU says heading is: θ_IMU
- GPS course says heading is: θ_GPS
- Error = θ_GPS - θ_IMU

**Apply correction:**
- If the error is consistent, your IMU has drift
- Use this error to correct current heading and update gyroscope bias estimate
- Feed this back into your Kalman filter as a heading measurement

## Important Considerations
**Minimum speed requirement:**
- Only works when moving fast enough for GPS noise to not dominate
- Typically need to be moving > 1-2 m/s for reliable course estimation
- At slow speeds, GPS position noise creates false heading readings

**Update rate limitation:**
- GPS updates slowly (1-10 Hz), so you can't use this for high-frequency heading updates
- Use IMU for smooth, fast heading between GPS corrections

**Straight-line assumption:**
- GPS course assumes you traveled in a straight line between measurements
- Less accurate during sharp turns or when GPS update rate is too slow

## Testing Localization in the Real Word
### Core testing objective
Validate that your localization system is accurate enough for the robot to autonomously follow a pre defined set of waypoints around the test track (this is generated by lane let)

### Essential test infrastructure

#### Wheel odometry break down
## Wheel Encoders
Wheel encoders are sensors that measure wheel rotation. There are two main types:

**Optical encoders** use a disk with alternating light and dark stripes (or holes) attached to the wheel. As the wheel turns, a light sensor detects the pattern changes and counts the transitions. Each transition represents a small angular movement.

**Magnetic encoders** use a magnetized disk with alternating magnetic poles. A magnetic sensor (like a Hall effect sensor) detects the changing magnetic field as the wheel rotates.

Both types output digital pulses - the number of pulses is proportional to how far the wheel has turned. If you know there are, say, 1000 pulses per wheel revolution and the wheel has a 0.3m circumference, then each pulse represents 0.3mm of linear wheel movement. 

Given:
- 1000 pulses per wheel revolution
- Wheel circumference = 0.3m

**Step 1:** Convert circumference to millimeters 0.3m = 0.3 × 1000 = 300mm
**Step 2:** Calculate distance per pulse Distance per pulse = Total circumference ÷ Number of pulses per revolution Distance per pulse = 300mm ÷ 1000 pulses = 0.3mm per pulse

So each pulse represents 0.3mm of linear movement along the ground.

**Example in use:** If the encoder counts 2500 pulses, the wheel has traveled: 2500 pulses × 0.3mm/pulse = 750mm = 0.75m

This is the fundamental relationship that lets wheel encoders measure distance - by counting pulses and multiplying by the known distance each pulse represents, you can determine how far the wheel (and vehicle) has moved.

## Dead Reckoning
Dead reckoning is a navigation technique where you calculate your current position based on your starting position plus all the movements you've made since then. It's like keeping a running tally: "I started at point A, moved 5 meters north, then 3 meters east, so now I'm at position X."

The term actually comes from maritime navigation - sailors would estimate their ship's position based on speed, time, and direction traveled from a known starting point.

The key characteristic of dead reckoning is that **errors accumulate**. If you're off by just a tiny bit in measuring each movement, those small errors add up over time until your calculated position is significantly wrong. That's why your explanation mentions wheel slip and miscalibration as major issues - even small measurement errors compound into large position errors over long distances.

This is why modern robots often combine wheel odometry with other sensors (like GPS, cameras, or lidar) to periodically correct these accumulated errors.

**With a bicycle model you can only tell how far the robot has moved forwards and backwards**

**IMU**
- Measures acceleration (linear) and rotation (angular velocity)
- Can estimate changes in orientation and movement
- **Weaknesses**: Drifts quickly without correction (especially cheap IMUs)
#### IMU breakdown

Inertial Measurement Unit
## What's Inside an IMU

A typical IMU contains:
**Accelerometers** - Measure linear acceleration in 3 axes (X, Y, Z). They can detect when you're speeding up, slowing down, or changing direction, as well as the direction of gravity.
**Gyroscopes** - Measure angular velocity (rotation rate) around 3 axes. They detect how fast you're rotating in pitch, roll, and yaw.
**Magnetometers** (in some IMUs) - Measure magnetic field strength, acting like a digital compass to detect orientation relative to Earth's magnetic field.

## How IMUs Help with Odometry
For vehicle navigation, IMUs are particularly useful because:

**Heading estimation:** Gyroscopes can directly measure how fast the vehicle is turning (yaw rate). By integrating this over time, you get heading changes - even without knowing the steering angle!
**Motion detection:** Accelerometers can detect forward/backward acceleration and help validate wheel encoder measurements.
**Drift correction:** While wheel odometry drifts due to wheel slip, IMU measurements drift due to sensor bias and noise. Sometimes these errors are complementary, so combining both gives better results.

## Example in Bicycle Model
Going back to your bicycle model question - an IMU could actually help solve the steering angle problem:

- Wheel encoders tell you distance traveled
- IMU gyroscope tells you turning rate
- Together, you can estimate both position and heading changes without needing a steering angle sensor

This is why many modern vehicles and robots use **sensor fusion** - combining wheel encoders, IMUs, GPS, and other sensors to get more accurate and robust position estimates than any single sensor could provide alone.

**The IMU essentially gives you an independent way to measure the rotational motion that you'd otherwise need to calculate from steering geometry.**


> [!Question] So the purpose of IMUs is to tell you which way you're facing and how much you have turned?

## What IMUs Directly Measure

IMUs measure **rates of change** and **accelerations**:
- **Gyroscopes:** Angular velocity (how fast you're currently rotating)
- **Accelerometers:** Linear acceleration (how fast your velocity is changing)
- **Magnetometers:** Magnetic field direction (like a compass)
## What You Can Calculate From IMU Data
**Orientation/Heading:** By integrating gyroscope data over time, you can track how much you've rotated and which direction you're facing relative to where you started.
**Velocity:** By integrating accelerometer data, you can estimate how fast you're moving.
**Position:** By double-integrating accelerometer data, you can estimate position changes.

## Action

### How does pure pursuit work?
Pure pursuit is a geometric path tracking algorithm that's simple, intuitive, and very effective for bicycle model vehicles.

### Core Concept
**The Big Idea:** The robot "looks ahead" on the path and steers toward a target point at a fixed distance ahead of its current position.

Think of it like driving a car - you don't steer toward where you are now, you steer toward where you want to be down the road.

### How it works
### Step 1: Find the Look-Ahead Point
- Define a "look-ahead distance" (e.g., 5 meters)
- Find the point on your path that is exactly this distance ahead of your current position
- This becomes your target point

### Step 2: Calculate Required Steering
- Draw an imaginary circle from your current position to the look-ahead point
- The radius of this circle determines how sharply you need to turn
- Use bicycle model kinematics to convert this radius into a steering angle

### Step 3: Drive Toward Target
- Apply the calculated steering angle
- Drive forward at your desired speed
- Repeat the process continuously as you move

### The Math (Simplified)
**For bicycle model:**
```
R = L² / (2 * lateral_distance)
steering_angle = atan(wheelbase / R)
```

Where:
- `R` = turning radius to reach look-ahead point
- `L` = look-ahead distance
- `lateral_distance` = how far sideways the look-ahead point is
- `wheelbase` = distance between front and rear axles

### Key Parameters
**Look-Ahead Distance:**
- **Smaller** = more aggressive, tighter following, but can be unstable
- **Larger** = smoother, more stable, but cuts corners on sharp turns
- Typically 1-3 times your vehicle length

You start the robot

The robot looks ahead to a point along the path and determines the steering angle needed to get there

It gets to the point and then localizes to see where it actually is

It notices that it's slightly off course

It finds a point along the path that's closest to its current position 

Using the closest point on the path, it determines the next point it has to get to along the path using the look ahead distance

It determines the steering angle needed to get from its current position to the look ahead point