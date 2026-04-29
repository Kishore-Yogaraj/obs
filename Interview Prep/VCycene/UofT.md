
---

"Hi, I'm Kishore — I'm a third year Mechanical Engineering student at the University of Waterloo. My background sits at the intersection of robotics, machine learning, and automation, which I think aligns really well with what you're building here at the Acceleration Consortium.

Most recently, I was a Co-Captain at Watonomous, our university's self driving car team, where I led development on things like real-time multi-object tracking using Kalman filters integrated with ROS2, and optimized vision pipelines using TensorRT across multiple cameras. That experience taught me a lot about building reliable, real-time robotic systems under constraints.

I also did a research assistantship at the Technical University of Hamburg, where I built a full navigation stack for a differential drive robot — from NeRF-based 3D mapping all the way down to A* pathfinding and ROS2 deployment. That project really sharpened my ability to work across the full robotics software stack.

On the industry side, I interned at Eon Media as a Machine Learning Engineer, where I deployed computer vision pipelines at scale — processing over 500,000 videos — so I'm comfortable taking ML work from prototype to production.

What draws me to this role specifically is the self-driving lab concept. I find the idea of applying autonomous systems to accelerate scientific discovery genuinely exciting — it's a natural extension of the robotics and automation work I've been doing, just applied to a completely new domain. I'm comfortable in Python, I have hands-on ROS2 and Docker experience, and I'm excited to contribute to something with this kind of real-world impact.


### Why do you want to work here
"Honestly, what really excites me about this role is the intersection of two things I care a lot about — autonomous systems and real-world impact.

Most of my experience has been in autonomous vehicles and robotics, and what I've always loved about that space is the idea that you're building systems that can operate and make decisions on their own. What the Acceleration Consortium is doing with self-driving labs feels like a really natural and meaningful extension of that — instead of navigating roads, you're navigating the scientific discovery process. That reframing is genuinely exciting to me.

The scale of what you're working on also stood out to me. A $200-million investment specifically aimed at accelerating materials discovery for renewable energy — that's not a small research side project, that's a serious, well-resourced mission. I want to be somewhere where the work actually moves the needle, and this feels like one of those places.

And practically speaking, the technical stack here maps closely to what I've been building toward. I've worked with ROS2, I've coordinated multi-sensor robotic platforms, I've written and deployed automation pipelines — so I know I can contribute from day one rather than spending the whole term getting up to speed.

But more than anything, I think I'm at a point in my degree where I want to see robotics and AI applied to something bigger than the problem itself, and materials science for clean energy feels about as meaningful as it gets."

### 3D Multi Object Tracking
One project I’m particularly proud of is the 3D multi-object tracking system I built, which was designed for tracking vehicles, pedestrians, and traffic signs in a self-driving car environment. The goal was to create a robust tracking system that could handle multiple objects in real-time, predict their movements, and account for situations where objects were temporarily occluded.

I used a combination of the **Kalman Filter** and the **Hungarian Algorithm** to solve this. The **Kalman Filter** is a mathematical model that helps predict an object’s future position based on its current state (like position, velocity) and update it as new data comes in. This was crucial for keeping track of moving objects even when they weren’t detected for a frame or two. The **Hungarian Algorithm** was used for data association—it matches detected objects in each frame with objects that were being tracked, ensuring that the system accurately tracks the same objects over time.

One of the most challenging parts of the project was handling situations where multiple objects were close together or when an object temporarily disappeared behind another (like a car passing behind a truck). The Kalman Filter allowed us to predict where the object would likely reappear, while the Hungarian Algorithm helped reassign detections when they re-emerged.

What I learned from this project is how crucial **data association** is when tracking multiple objects in real-world scenarios. You can't rely on the detection system alone because objects move, they might be missed in certain frames, or multiple objects can appear close together. It taught me the importance of combining different algorithms—like the Kalman Filter for prediction and the Hungarian Algorithm for matching—to create a more reliable system.

Additionally, I learned about the importance of real-time performance optimization. While the theory behind these algorithms is powerful, implementing them efficiently, so they run in real-time on self-driving cars, was a challenge that required a lot of testing and tuning.

### How did the Kalman Filter and Hungarian Algorithm work in your 3D tracking node?
In my 3D tracking node, I used the Kalman Filter and Hungarian Algorithm to track objects like cars and pedestrians from sensor data in real-time. These two algorithms work together to maintain accurate tracking, even when objects temporarily disappear from the sensor’s view or overlap.

1. **Kalman Filter**: The Kalman Filter is a recursive state estimation algorithm used to predict the future state of an object, given noisy observations. In my tracking node, each object’s state was defined by its position and velocity in 3D space. The filter has two steps:
    
    - **Prediction Step**: Based on the current state and velocity of an object, the Kalman Filter predicts the object’s next position in the following frame. This helps in situations where an object might not be detected in every frame, as we can estimate where it should be based on its previous state.
    - **Update Step**: When new measurements (like a new detection from a LiDAR or camera sensor) become available, the Kalman Filter updates its prediction by incorporating this measurement to correct the state estimate. This balancing act between prediction and correction allows for more robust tracking even with noisy sensor data.
    
    Mathematically, the filter uses a set of equations that include a **prediction step** based on the object’s current state and a **correction step** when new measurements come in, all while considering the noise in both the process and the measurements.
    
2. **Hungarian Algorithm**: The Hungarian Algorithm solves the **data association problem**, which is crucial when tracking multiple objects. In every new frame, we receive multiple detections (potential objects), and we need to associate these new detections with the existing tracked objects from the previous frame.
    
    The Hungarian Algorithm works by minimizing the cost of matching these detections with the tracked objects. The cost is typically defined as the distance between the predicted position of the tracked object (using the Kalman Filter) and the new detection. It formulates this matching as an assignment problem and finds the optimal set of matches that minimizes the overall cost. Once the new detections are associated with the correct objects, we can update the object’s state with the new measurement.
    
    In practice, the Kalman Filter helps smooth the tracking of each object, while the Hungarian Algorithm ensures the correct correspondence between detected objects across frames, even when objects temporarily disappear from view or overlap with each other.

### Issues I ran into
One particularly frustrating issue I ran into was the **data association** problem when multiple objects were close together or when they temporarily overlapped. The tracking system would sometimes struggle to reassign detections accurately, especially when objects passed behind each other, like a car moving behind a truck. This led to the system occasionally switching the identities of the objects or losing track of them altogether, which is a major problem for reliable tracking in self-driving vehicles.

The root of the problem was that the **Hungarian Algorithm**, which I was using for data association, struggled when the distance between detected objects and their predicted positions (from the **Kalman Filter**) became too small or indistinguishable. This caused the system to get confused about which detection matched which object.

To solve this, I fine-tuned the **cost function** in the Hungarian Algorithm, which determines how the detections are matched to the tracked objects. Instead of just relying on the distance between predicted and detected positions, I incorporated additional information like the size and velocity of the objects into the cost function. This gave the algorithm more context when making its matching decisions, making it less likely to switch object identities when they were close together.

This adjustment significantly improved the accuracy of the tracking system. It taught me that even small changes in how you handle data association can make a big difference, and that combining multiple features—not just position—can help make the system more robust.

### Experience at TUHH
Absolutely. I worked as a Machine Learning and Robotics Research Assistant at the Technical University of Hamburg (TUHH) during a co-op term from January to May. It was a unique experience where I got to take full ownership of a vision-based navigation project for a differential-drive robot, entirely built around Neural Radiance Fields (NeRFs).

My goal was to create a system that could navigate using just an RGB camera. I trained a NeRF model to reconstruct the 3D environment and then queried it to build a 3D occupancy grid. From there, I converted it to a 2D costmap and used A* for path planning. I also developed a ROS2 control node that generated smooth velocity commands to follow the path. Everything was containerized using Docker and tested in simulation.

The learning curve was steep — especially working with NeRFs and trying to make them practical for real-time navigation. One of the biggest challenges was localization. NeRF models define their own coordinate frames based on training images, so aligning them with the real-world frame was tricky. I experimented with optimizing camera pose by minimizing the difference between rendered and actual camera images. It wasn’t perfect, but it moved the project in the right direction.

Working in Germany also meant adjusting to a different academic culture. I had a lot more autonomy than I was used to, which helped me grow significantly in terms of self-directed problem solving, research implementation, and cross-cultural communication. Overall, it was a really rewarding experience that pushed my boundaries both technically and personally.

### Nerf Navigation
One of the projects I’m most proud of is a vision-based navigation system I built during my research internship at the Technical University of Hamburg. The idea was to use Neural Radiance Fields — or NeRFs — to create 3D maps from RGB images, and then use those maps for autonomous robot navigation. I started by generating a volumetric density field from camera images, then converted that into a 3D occupancy grid. From there, I downsampled to a 2D costmap, ran A* path planning, and designed a ROS2-based control node to generate smooth velocity commands for a differential-drive robot in simulation.

What made it really exciting was that the whole system relied only on RGB input — no LiDAR or depth sensors — which made it cost-effective and scalable. I also containerized the whole stack with Docker, making it portable and reproducible.

One major challenge was localization. Since NeRF coordinates are relative to the training data, aligning them with a global map frame was tricky. I initially tried static transformations, but they weren’t robust across environments. Eventually, I implemented an optimization loop that aligned synthesized views from the NeRF with live camera frames, minimizing photometric error to estimate pose. It wasn’t perfect, but it moved us closer to a fully vision-based SLAM alternative.

This project really deepened my understanding of perception pipelines, control, and spatial reasoning in robotics. It also taught me how to break down a complex system into modular components, debug at multiple levels, and adapt to a research environment where the problem isn’t fully defined. It was the first time I built a full autonomy stack from scratch — and seeing the robot follow planned paths from just a camera feed was incredibly rewarding.

### Issues I ran into
Sure, I can walk through a couple specifically.

With the Z-thresholding issue for ground segmentation — the problem was that the threshold value that worked in one part of the environment would break in another, because the NeRF reconstruction had slight inconsistencies in scale depending on the viewing angle during training. The way we addressed that was by iteratively retraining the NeRF with more diverse camera poses to get a more geometrically consistent reconstruction, and then tuning the threshold empirically across multiple test zones rather than relying on a single global value.

For the latency issue with feeding NeRF output into the real-time navigation stack — we couldn't make NeRF inference itself faster without sacrificing map quality, so the fix was more architectural. Instead of regenerating the full costmap on every cycle, we moved to a cached map approach where the costmap would only update when the robot detected it had moved beyond a certain distance threshold. That cut down the computational load significantly without meaningfully hurting navigation performance in a mostly static environment.

And for the ROS2 integration headaches — a lot of that came down to coordinate frame mismatches between the NeRF world frame and the robot's odometry frame. We resolved that by being really disciplined about defining a single reference frame early on and writing explicit transform broadcasters to keep everything consistent across nodes.

### What is a self driving lab
A self-driving lab is essentially a laboratory that can run experiments autonomously — without needing a human to physically be there directing every step.

The idea is that you combine robotics, AI, and automation to close the loop on the scientific process. So instead of a researcher manually preparing samples, running an experiment, recording results, and then deciding what to try next — a self-driving lab does all of that on its own. The AI looks at the results of one experiment and uses that to intelligently decide what the next experiment should be, and the robotic systems physically carry it out.

The reason it's such a big deal — especially in materials science — is that discovering new materials traditionally takes an enormous amount of time. You're essentially searching through a massive space of possible chemical combinations and conditions, and doing that by hand is incredibly slow. A self-driving lab can run hundreds of experiments in the time it would take a human to run a handful, and it's doing it intelligently rather than just randomly.

What drew me to this specific role is that the Acceleration Consortium is applying this to inorganic materials for renewable energy — so the potential downstream impact is really significant. And from a technical standpoint, it's a fascinating problem because you're not just building a robot that repeats the same task over and over — you're building a system that actually adapts and learns from what it's doing.

### Questions
**Questions**
- What does your ideal intern look like? What are some traits and characteristics you value in your interns and what do you expect of them on the job?
- How committed is the team to mentorship? Does the co op emphasize more on how much you put in is how much you get out of it? Or are supervisors usually available to keep co ops accountable?
- First, I'm curious about the robotic platforms themselves — what does the current hardware setup look like in the inorganic SDL, and how mature is the software stack around it? I want to understand where things are today versus where the team is trying to take it.
- Second, you mentioned coordinating multiple robotic platforms through open-source APIs — are there specific frameworks already in place for that orchestration, or is part of the role helping to define and build that architecture?
- I'd also love to understand what a typical day or week looks like for someone in this role — how much of it is hands-on lab work versus software development versus collaboration with the science side of the team?
- he=

