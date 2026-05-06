### Tell me About Yourself
I'm Kishore, a third-year Mechanical Engineering student at the University of Waterloo, and most of my work over the past few years has been in robotics software which is what drew me to NavMobility in the first place.

My deepest experience comes from Watonomous, Waterloo's autonomous vehicle research team, where I'm Co-Captain and lead the autonomy stack across a team of more than 30 engineers. I mainly worked in perception and sensor integration all built in ROS2 with C++ and Python. A couple of projects I'm particularly proud of: I built a Kalman filter–based multi-object tracker that runs as a ROS2 node and tracks up to 20 objects at 30 Hz, and I optimized our computer vision pipelines using TensorRT and Docker, which cut inference latency by about 40% across our multi-camera setup.

I also did a research term at the Technical University of Hamburg, where I worked on camera-based perception and navigation for a differential drive robot. I implemented an autonomy stack that used Neural Radiance Fields for 3D scene representation, and then converted those NeRF-derived maps into 2D costmap for an A* planner. We hit a 95% navigation success rate across simulated trials, with planning times under 400 milliseconds. Before that, I interned at Eon Media as an ML engineer, where I deployed a YOLOv8 and OpenCV pipeline for brand and logo recognition that improved detection accuracy by 30% across a library of over 500,000 videos.

What excites me about NavMobility specifically is the combination of GNSS/INS, LiDAR, and camera data feeding into HD maps and perception for ITS applications. I've worked extensively with the camera and LiDAR side of autonomous vehicles in perception applications, and I'm eager to deepen my GNSS/INS knowledge fusing absolute positioning with onboard perception is one of the harder and more interesting problems in this space, and it's exactly the kind of work I want to be doing.

### Why do You Want to Work Here
Honestly, when I came across the posting, it hit a really specific intersection of where I am in my career and where I want to go next.

Most of my robotics work so far has been on the perception side — the multi-object tracker I built at Watonomous, the TensorRT-optimized vision pipelines, the camera-based depth estimation, the YOLOv8 work at Eon Media. I've spent a lot of time turning raw sensor data into something a robot can reason about in the moment. But the more I've worked on full autonomy stacks, the more I've realized that perception is only half the picture. The other half is _mapping_ — having a strong prior about the world that perception can localize against and plan on top of. And that's the side I haven't gone deep on yet.

That's a big part of why NavMobility stands out to me. The fact that you're fusing GNSS/INS, LiDAR, and camera data specifically to build HD maps means I'd be working on the exact part of the stack I want to grow into. I'm especially interested in the point cloud and image segmentation work for road infrastructure, because that's where my computer vision background overlaps with the mapping problems I want to learn.

The other reason this is well-timed for me personally is my capstone. Starting in September, I'm building a food delivery robot with my team and mapping is going to be one of the core challenges. Last-mile delivery robots live or die on how well they can build and use maps of sidewalks, intersections, and dynamic environments. So getting hands-on experience with how a company that does this professionally approaches HD map generation, sensor fusion, and the GNSS/INS side of localization would be incredibly valuable. I'd come back to my capstone a much stronger engineer because of it.

And finally, the startup environment is genuinely something I'm looking for. At Watonomous I've been part of building things from the ground up across a team of 30+ engineers, and I really enjoy the pace and ownership that comes with that.

### Challenge with Watonomous - Interface Issues
One of the biggest challenges I faced as Co-Captain was actually less technical and more about coordination across the team.

When I took on the perception lead role, we had around 30 engineers split across perception, planning, controls, and simulation. The issue I kept running into early on was that perception was being developed in relative isolation from the downstream teams that actually consumed our outputs. I'd have engineers on my side build a tracker or a depth estimation node, get it working on their own bag files, and then we'd hand it off to planning — and the planning team would come back saying the output format didn't match what they expected, or the latency was too high for their control loop, or the coordinate frames weren't aligned with what they were assuming. We were burning a lot of cycles re-doing integration work that should have been right the first time.

The specific moment it came to a head was when we were preparing for an internal demo. My team had built the Kalman filter–based multi-object tracker which was working great in isolation. But when we plugged it into the planning stack two days before the demo, the planner was making poor decisions because the tracker was publishing at a slightly different rate than the planner expected, and the velocity estimates were in a different frame. We basically had a working perception module and a working planner that couldn't talk to each other properly.

What I did to solve it was push for what I started calling "interface-first development." Before any new perception node got built, I made the team sit down with whoever was going to consume the output and agree on the message definition, the publishing rate, the frame conventions, and the latency budget _before_ writing any code. For the immediate demo, I paired one of my perception engineers with the planning lead for an afternoon, and we re-aligned the tracker's output to match what the planner needed. We made the demo.

Longer term, I introduced a lightweight integration checkpoint into our development cycle — basically, every two weeks each sub-team had to demonstrate their module running end-to-end with at least one downstream consumer, not just in isolation. It wasn't a heavy process, but it caught integration issues weeks earlier than we used to.

The result was that our integration time before our final demo dropped significantly. We went from spending the last two weeks of a sprint frantically debugging interface mismatches to spending that time actually tuning and improving the stack. And honestly, the bigger win was cultural — engineers started thinking about their nodes as part of a system rather than as standalone projects, which is a mindset I think really matters for any robotics work.

### Challenge with NeRF Robot - Bridging the gap of nerf to nav
One of the most interesting challenges I worked on at TU Hamburg was bridging Neural Radiance Fields with a classical navigation stack. NeRFs give you a continuous volumetric scene representation, but Nav2 expects a clean 2D occupancy grid — so the core problem was: how do you turn a NeRF into something a planner can actually use?

My first attempt was the obvious one — sample the NeRF at a fixed height, threshold the density, and call anything above the threshold an obstacle. The costmaps came out really noisy though. The NeRF was producing spurious density spikes in free space, so the robot kept getting stuck in places that were obviously open in reality.

What I did was visualize the NeRF output across different heights and density thresholds to actually build intuition for where the network was confident versus hallucinating. From that, I changed two things: I sampled across a range of heights spanning the robot's body and took the maximum density along each vertical column, so nothing the robot would physically collide with got missed. And I added a confidence filter so low-density, high-uncertainty regions got marked as unknown space rather than free space.

After that, the costmaps were clean. We generated them at 0.05 m resolution, the planner was producing trajectories in under 400 ms, and we hit a 95% success rate across 50+ simulated trials.

The bigger takeaway was that bridging novel perception methods with classical robotics stacks isn't just plugging them together — the representations carry assumptions, and you have to really understand both sides before you can connect them. 

### 5 Bar Parallel Scara
For my mechanical design course, my team built a 5-bar parallel SCARA robot — a two-armed planar manipulator that does autonomous pick-and-place. A camera mounted above the workspace detects objects, and the robot picks them up and places them at target locations across an 800 by 800 mm area. My role was owning the software side end-to-end: the inverse kinematics solver, the OpenCV vision pipeline, and the embedded firmware running on the microcontroller.

The biggest challenge came at integration. Each piece worked great on its own — the kinematics hit ±5 mm accuracy when I commanded the robot directly, and the vision pipeline detected objects reliably at 30 FPS. But when I connected them and asked the robot to pick up an object the camera had detected, it kept missing, and the error got worse near the edges of the workspace.

The issue was my camera-to-robot calibration. I was using a naive pixel-to-millimetre scale factor, which ignored lens distortion and any tilt in the camera mount. The fix was a proper calibration — I used OpenCV to get the camera intrinsics and distortion coefficients, then collected point correspondences by jogging the robot to known positions and solving for the homography between the camera plane and the robot's workspace plane.

After that, the end-to-end pick accuracy matched the kinematics accuracy, ±5 mm across the full workspace. The lesson I took from it was that the hardest bugs in robotics usually live at the seams between subsystems — each piece can be correct and the whole thing can still fail because of an assumption one module makes about another.

### What is GNSS
Global Navigation Satellite System

### Questions
- How would you define success in this role? What would be the key indicators to you that you hired the right person?
- Why did you choose to tackle the problem of generating maps for autonomous vehicle applications? 
- What does success with this company look like to you?
- How many people work at this company?