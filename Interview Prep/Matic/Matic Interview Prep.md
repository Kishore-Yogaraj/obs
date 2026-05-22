

## **Behavioral**
### Tell me About Yourself
I'm a 3B mechanical engineering student at the University of Waterloo, with most of my focus the last three years going into robotics and autonomy.

The thread that runs through everything I've done is wanting to understand how robots actually work in the real world and not just in simulation. At Watonomous, our self-driving team at Waterloo, I went from being a perception engineer to co-captaining the team and leading 30+ engineers across our autonomy stack. A lot of that role was less about writing code and more about debugging hard problems with people and figuring out why a tracker was dropping objects, why latency spiked on one camera feed, coordinating across perception, planning, and integration to actually ship things that worked.

Last Winter I did a research term at TU Hamburg working on NeRF-based navigation for a differential drive robot. Before that I was at Eon Media in Toronto deploying computer vision pipelines for video analysis. So I've spent a lot of time recently thinking about what it actually takes to make a robot reliable enough to operate in messy, unpredictable real-world environments.

What pulled me toward Matic specifically is honestly the product. The on-device spatial AI approach, the privacy-first design, the fact that you're solving full-self-driving for the home using just RGB cameras — that's a hard problem, and the demos I've seen look like the robot actually works, which is rare in this space.

The reason I'm interested in the support role and not a pure engineering one is that I think customer support at a company like Matic is where the rubber meets the road. You see every weird edge case the engineering team didn't anticipate. You see what real homes look like versus the lab. And if I can troubleshoot a robot from a hardware, software, and algorithms perspective — which is what the job description asks for — I think I can do this job well and feed real signal back into the product. That's the kind of work I want to do.

### How do you handle Stress in the work environment
I handle stress by staying organized and maintaining a clear focus on priorities. When I’m faced with a challenging workload or tight deadlines, I break tasks down into manageable steps and set clear goals for each. This helps me stay on track and reduces the feeling of being overwhelmed. I also find that taking short breaks to reset, even just a few minutes away from the task, can help me come back with a fresh perspective and renewed focus.

Communication is also key for me. If a project becomes particularly demanding, I ensure to keep the lines of communication open with my team or stakeholders to manage expectations and seek input if needed. This has been especially important in roles like my time at Watonomous, where balancing multiple perception projects required careful time management and collaboration to ensure the team stayed aligned.

I view stress as a signal that something needs attention, and I try to channel it into problem-solving and maintaining a productive mindset. Additionally, I practice mindfulness techniques to keep a clear head and remain calm, even during high-pressure situations.

### How do you deal with conflict in a team and what is an example of you resolving a conflict
I believe the key to handling conflict on a team is open communication, active listening, and finding common ground. When conflicts arise, I focus on understanding the perspectives of everyone involved and working collaboratively to find a solution that benefits the project and the team. It’s important to address conflicts early before they escalate, and I strive to create a positive environment where team members feel comfortable expressing their views.

For example, while working at Watonomous as the perception lead, there was a disagreement between two team members over the best approach for implementing a 3D object tracking algorithm. One member wanted to stick with a Kalman Filter-based solution, while the other was advocating for trying a newer, more complex algorithm that they believed could provide better performance but required more development time. This disagreement was creating delays in moving forward with the project.

To resolve the conflict, I brought both team members together for a discussion where we could openly explore the pros and cons of each approach. We walked through the requirements, timelines, and the potential risks and benefits of each option. After facilitating this conversation, we decided to implement a test for both approaches on a smaller dataset to compare their performance. In the end, the Kalman Filter approach proved to be sufficient for the current phase of the project, while we noted the alternative method for future improvements.

By taking a collaborative, data-driven approach to resolving the conflict, we were able to move forward quickly, and both team members felt that their perspectives had been heard and respected.

### Talk About Your Experience with Watonomous
My experience with Watonomous has been incredibly rewarding, especially in my current role as **Co-Captain**, where I lead and manage a multidisciplinary team building cutting-edge autonomous systems — including self-driving cars, humanoid robots, and autonomous RC vehicles. This position has given me the opportunity to contribute both technically and organizationally across several subsystems, while driving the strategic direction of our projects.

One of the major technical initiatives I led was the development of a **3D object tracking system** using the **Kalman Filter** and **Hungarian Algorithm**, which enabled real-time tracking of vehicles and pedestrians. I also built a **YOLOv8-based object detection node** for recognizing traffic signs and cars, integrating it into our perception stack. This required tight collaboration with our hardware subteam to ensure sensor and compute system integration worked reliably on the vehicle.

As Co-Captain, I’ve worked across perception, planning, and systems teams to ensure seamless integration of components. I’m also heavily involved in project management—coordinating timelines, facilitating cross-functional syncs, and aligning our technical direction with the competition goals. A big part of my role involves mentoring junior members, onboarding new recruits, and ensuring that knowledge transfer and documentation are prioritized so that progress is sustainable.

This leadership experience has pushed me to grow not just as an engineer, but as a systems thinker and team builder. It’s strengthened my ability to take ownership of complex problems, communicate across disciplines, and deliver real-world solutions in the autonomous vehicle space.

### What is a challenge you had at Watonomous and how did you overcome it
Yeah, so probably the biggest challenge I had at Watonomous was less technical and more about leadership. When I stepped into the co-captain role, I went from being a perception engineer — heads down writing code, debugging my own nodes — to suddenly being responsible for 30+ engineers across the perception team. And I really struggled with it at first.

The specific problem was that we had a lot of really talented people, but our progress was slow. People would pick up tickets, disappear for two weeks, and come back with either nothing or work that didn't integrate with what someone else had built in parallel. We were duplicating effort, our ROS2 nodes weren't talking to each other cleanly, and we'd hit integration weeks where nothing worked together. As the person responsible for shipping the perception stack, that was on me.

My first instinct was to just do more of the work myself — if I wrote the tricky pieces, at least I knew they'd get done. That made things worse. I became a bottleneck, my engineers felt like I didn't trust them, and I was burning out trying to code and lead at the same time.

What actually fixed it was changing how I ran the team. A few specific things:

First, I started doing weekly one-on-ones with my sub-leads instead of just relying on group standups. Group standups are great for status, but people don't tell you they're stuck or confused in front of 15 other people. In one-on-ones I'd actually find out that someone had been blocked for a week on a TensorRT issue and was too embarrassed to ask for help.

Second, I started writing much clearer technical specs before work began — what the node's inputs and outputs should be, what the message types looked like, what the acceptance criteria were. Before, we'd say "build the tracker" and three people would build three incompatible things. After, integration got way smoother.

Third, and this is the one I had to learn the hard way — I had to actually trust people to own their pieces, even when I would have done it differently. There were times I watched someone take an approach I wouldn't have, and instead of jumping in I'd ask questions, let them try, and only intervene if it was clearly going off the rails. Most of the time they'd either make it work or learn something better than I would have taught them.

By the end of my term, we were shipping pipelines that genuinely worked together — the Kalman filter tracker was running stably at 30 Hz feeding into downstream nodes, our TensorRT optimizations actually got deployed, and integration weeks went from chaos to mostly boring. Which is what you want.

The biggest thing I took away from it — and I think this applies directly to a support role too — is that most problems on a team aren't technical. They're communication problems. People being stuck and not asking. Unclear ownership. Assumptions that didn't get written down. If you fix the communication, the technical stuff usually sorts itself out.

### Talk about your experience at TU Hamburg

Yeah, so this past winter — January through May — I did a research term at TU Hamburg in Germany. I was working in a robotics lab on camera-based perception and navigation for a differential drive robot, all in simulation.

The core research question was: can you use Neural Radiance Fields — NeRFs — as the foundation for a robot's understanding of its environment, instead of traditional approaches like LiDAR point clouds or occupancy grids? NeRFs are usually used for novel view synthesis — basically rendering photorealistic 3D scenes from a handful of 2D images — but my project was about taking those 3D representations and turning them into something a robot can actually navigate on.

So the pipeline I built went something like this: we'd capture RGB images of the environment, train a NeRF to build a 3D representation, and then I wrote code to convert that NeRF-derived 3D map into a 2D costmap at 0.05 meter resolution — which is what ROS2's Nav2 stack actually uses for planning. From there I implemented A* pathfinding on top of the costmap and a trajectory generator that produced smooth paths for the robot to follow. End-to-end, the planner was generating trajectories in under 400 milliseconds, and across about 50 simulated trials we hit a 95% navigation success rate.

The hardest part honestly wasn't the algorithms — it was the integration. NeRFs and ROS2 don't naturally play well together. NeRFs are a deep learning research thing, mostly used in Python with PyTorch in Jupyter notebooks. ROS2 is a real-time robotics framework with strict messaging and timing constraints. So a lot of my time was spent on the unglamorous middleware — getting tensors out of the NeRF model, into the right coordinate frames, packaged as ROS2 messages, at a rate the planner could actually consume without choking.

A couple of things I took away from the experience that I think are relevant here:

First, working in a research lab in a foreign country — I went to Germany not knowing anyone, and had to figure out how to be productive in a new environment, with a new advisor, on a topic I'd never worked on before. That stretched me a lot in terms of just being comfortable in ambiguity and asking for help when I needed it.

Second, the technical takeaway: I spent five months learning that the gap between "this works in a paper" and "this works on a robot" is enormous. Most of the published work on NeRFs for robotics gives you a beautiful render and skips over the fact that you still need to feed a real planner real data at real rates. That gap — between something working in theory and working in the messy real world — is the same gap that exists between a robot working in the lab and a robot working in someone's actual home. Which is the problem Matic is solving.


### What is a challenge you had at TU Hamburg and How did You Overcome it

Yeah, so the biggest challenge at TU Hamburg was honestly a scoping problem, and it took me too long to recognize it.

When I got there, my advisor gave me a pretty open-ended research question: can NeRFs be used as the foundation for robot navigation? That's exciting — it's the kind of ambiguity you sign up for in research — but it's also dangerous, because there's no clear definition of done. I could spend five months on it, or five years.

My first instinct was to go deep on the NeRF side. I figured: the navigation stack is well-understood, A* and Nav2 are off-the-shelf, the hard novel thing is the perception piece, so let me really nail that first. I spent probably the first six weeks trying to train better NeRFs, tuning hyperparameters, reading papers on NeRF variants, trying to get sharper 3D representations. And the results were genuinely better — my renders looked great.

The problem was, when I finally turned to the navigation side, I realized I'd built something the robot couldn't actually use. The NeRF output was high-fidelity but slow to query, the coordinate frames didn't line up with what ROS2 expected, and there was no clean way to get from "beautiful 3D model" to "2D costmap a planner can read at runtime." I'd optimized one piece of the pipeline in isolation without thinking about what the next piece needed from it. Classic mistake.

So I had to back up. My advisor and I had a pretty honest conversation where I basically said, I've been chasing the wrong thing. We re-scoped the project — instead of "build the best NeRF possible," it became "build the simplest end-to-end pipeline that works, then improve the weakest link." I rewrote the timeline. I picked a NeRF implementation that was good enough rather than state-of-the-art, and I spent the next two months on the integration glue — the coordinate transforms, the costmap conversion, the message timing — that I'd been avoiding because it wasn't the interesting part.

And once I had the full pipeline working end-to-end, even crudely, everything got easier. I could see exactly where the bottlenecks were. The 400 ms planning time, the 95% success rate — those numbers only existed because I'd built the full loop and could actually measure it. None of that would have happened if I'd kept polishing the NeRF in isolation.

The lesson I took away — and I think about this a lot now — is that when you're working on a system, the right move is almost always to build the whole thing end-to-end as fast as you can, even if it's ugly, and then improve the weakest link. Otherwise you're optimizing things in a vacuum and you don't know what actually matters. That applies to research, it applies to product work, and I think it applies to support too — you want to see the full customer journey end-to-end before you go deep on optimizing any one part.


## **Technical**

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



## **Cool Things About the Company**

### Story with Dave
- Dealing with a wheel squeaking issue 
- Dave said he couldn't get back to the team because his father was sick
- Matic sent him flowers

### How Great Mehul is
![[Pasted image 20260521223029.png]]

### Robert Scoble Interview

### Problem with Current Indoor Robots
- Algorithmically behind with a lot of sensors so no brain with a bunch of eyes
- If the robot can see like us then we can solve the robot from a first principles point of view
- Roomba's got stuck too much
- Loses its way a lot
- Wheels cant go over any rugs
- Things fall out when its full
- Very small bin


## Organized Questions
### Talk About Your Experience with Watonomous
My experience with Watonomous has been incredibly rewarding, especially in my current role as **Co-Captain**, where I lead and manage a multidisciplinary team building cutting-edge autonomous systems — including self-driving cars, humanoid robots, and autonomous RC vehicles. This position has given me the opportunity to contribute both technically and organizationally across several subsystems, while driving the strategic direction of our projects.

One of the major technical initiatives I led was the development of a **3D object tracking system** using the **Kalman Filter** and **Hungarian Algorithm**, which enabled real-time tracking of vehicles and pedestrians. I also built a **YOLOv8-based object detection node** for recognizing traffic signs and cars, integrating it into our perception stack. This required tight collaboration with our hardware subteam to ensure sensor and compute system integration worked reliably on the vehicle.

As Co-Captain, I’ve worked across perception, planning, and systems teams to ensure seamless integration of components. I’m also heavily involved in project management—coordinating timelines, facilitating cross-functional syncs, and aligning our technical direction with the competition goals. A big part of my role involves mentoring junior members, onboarding new recruits, and ensuring that knowledge transfer and documentation are prioritized so that progress is sustainable.

This leadership experience has pushed me to grow not just as an engineer, but as a systems thinker and team builder. It’s strengthened my ability to take ownership of complex problems, communicate across disciplines, and deliver real-world solutions in the autonomous vehicle space.

### What is a challenge you had at Watonomous and how did you overcome it
Yeah, so probably the biggest challenge I had at Watonomous was less technical and more about leadership. When I stepped into the co-captain role, I went from being a perception engineer — heads down writing code, debugging my own nodes — to suddenly being responsible for 30+ engineers across the perception team. And I really struggled with it at first.

The specific problem was that we had a lot of really talented people, but our progress was slow. People would pick up tickets, disappear for two weeks, and come back with either nothing or work that didn't integrate with what someone else had built in parallel. We were duplicating effort, our ROS2 nodes weren't talking to each other cleanly, and we'd hit integration weeks where nothing worked together. As the person responsible for shipping the perception stack, that was on me.

My first instinct was to just do more of the work myself — if I wrote the tricky pieces, at least I knew they'd get done. That made things worse. I became a bottleneck, my engineers felt like I didn't trust them, and I was burning out trying to code and lead at the same time.

What actually fixed it was changing how I ran the team. A few specific things:

First, I started doing weekly one-on-ones with my sub-leads instead of just relying on group standups. Group standups are great for status, but people don't tell you they're stuck or confused in front of 15 other people. In one-on-ones I'd actually find out that someone had been blocked for a week on a TensorRT issue and was too embarrassed to ask for help.

Second, I started writing much clearer technical specs before work began — what the node's inputs and outputs should be, what the message types looked like, what the acceptance criteria were. Before, we'd say "build the tracker" and three people would build three incompatible things. After, integration got way smoother.

Third, and this is the one I had to learn the hard way — I had to actually trust people to own their pieces, even when I would have done it differently. There were times I watched someone take an approach I wouldn't have, and instead of jumping in I'd ask questions, let them try, and only intervene if it was clearly going off the rails. Most of the time they'd either make it work or learn something better than I would have taught them.

By the end of my term, we were shipping pipelines that genuinely worked together — the Kalman filter tracker was running stably at 30 Hz feeding into downstream nodes, our TensorRT optimizations actually got deployed, and integration weeks went from chaos to mostly boring. Which is what you want.

The biggest thing I took away from it — and I think this applies directly to a support role too — is that most problems on a team aren't technical. They're communication problems. People being stuck and not asking. Unclear ownership. Assumptions that didn't get written down. If you fix the communication, the technical stuff usually sorts itself out.

### Talk about your experience at TU Hamburg

Yeah, so this past winter — January through May — I did a research term at TU Hamburg in Germany. I was working in a robotics lab on camera-based perception and navigation for a differential drive robot, all in simulation.

The core research question was: can you use Neural Radiance Fields — NeRFs — as the foundation for a robot's understanding of its environment, instead of traditional approaches like LiDAR point clouds or occupancy grids? NeRFs are usually used for novel view synthesis — basically rendering photorealistic 3D scenes from a handful of 2D images — but my project was about taking those 3D representations and turning them into something a robot can actually navigate on.

So the pipeline I built went something like this: we'd capture RGB images of the environment, train a NeRF to build a 3D representation, and then I wrote code to convert that NeRF-derived 3D map into a 2D costmap at 0.05 meter resolution — which is what ROS2's Nav2 stack actually uses for planning. From there I implemented A* pathfinding on top of the costmap and a trajectory generator that produced smooth paths for the robot to follow. End-to-end, the planner was generating trajectories in under 400 milliseconds, and across about 50 simulated trials we hit a 95% navigation success rate.

The hardest part honestly wasn't the algorithms — it was the integration. NeRFs and ROS2 don't naturally play well together. NeRFs are a deep learning research thing, mostly used in Python with PyTorch in Jupyter notebooks. ROS2 is a real-time robotics framework with strict messaging and timing constraints. So a lot of my time was spent on the unglamorous middleware — getting tensors out of the NeRF model, into the right coordinate frames, packaged as ROS2 messages, at a rate the planner could actually consume without choking.

A couple of things I took away from the experience that I think are relevant here:

First, working in a research lab in a foreign country — I went to Germany not knowing anyone, and had to figure out how to be productive in a new environment, with a new advisor, on a topic I'd never worked on before. That stretched me a lot in terms of just being comfortable in ambiguity and asking for help when I needed it.

Second, the technical takeaway: I spent five months learning that the gap between "this works in a paper" and "this works on a robot" is enormous. Most of the published work on NeRFs for robotics gives you a beautiful render and skips over the fact that you still need to feed a real planner real data at real rates. That gap — between something working in theory and working in the messy real world — is the same gap that exists between a robot working in the lab and a robot working in someone's actual home. Which is the problem Matic is solving.


### What is a challenge you had at TU Hamburg and How did You Overcome it

Yeah, so the biggest challenge at TU Hamburg was honestly a scoping problem, and it took me too long to recognize it.

When I got there, my advisor gave me a pretty open-ended research question: can NeRFs be used as the foundation for robot navigation? That's exciting — it's the kind of ambiguity you sign up for in research — but it's also dangerous, because there's no clear definition of done. I could spend five months on it, or five years.

My first instinct was to go deep on the NeRF side. I figured: the navigation stack is well-understood, A* and Nav2 are off-the-shelf, the hard novel thing is the perception piece, so let me really nail that first. I spent probably the first six weeks trying to train better NeRFs, tuning hyperparameters, reading papers on NeRF variants, trying to get sharper 3D representations. And the results were genuinely better — my renders looked great.

The problem was, when I finally turned to the navigation side, I realized I'd built something the robot couldn't actually use. The NeRF output was high-fidelity but slow to query, the coordinate frames didn't line up with what ROS2 expected, and there was no clean way to get from "beautiful 3D model" to "2D costmap a planner can read at runtime." I'd optimized one piece of the pipeline in isolation without thinking about what the next piece needed from it. Classic mistake.

So I had to back up. My advisor and I had a pretty honest conversation where I basically said, I've been chasing the wrong thing. We re-scoped the project — instead of "build the best NeRF possible," it became "build the simplest end-to-end pipeline that works, then improve the weakest link." I rewrote the timeline. I picked a NeRF implementation that was good enough rather than state-of-the-art, and I spent the next two months on the integration glue — the coordinate transforms, the costmap conversion, the message timing — that I'd been avoiding because it wasn't the interesting part.

And once I had the full pipeline working end-to-end, even crudely, everything got easier. I could see exactly where the bottlenecks were. The 400 ms planning time, the 95% success rate — those numbers only existed because I'd built the full loop and could actually measure it. None of that would have happened if I'd kept polishing the NeRF in isolation.

The lesson I took away — and I think about this a lot now — is that when you're working on a system, the right move is almost always to build the whole thing end-to-end as fast as you can, even if it's ugly, and then improve the weakest link. Otherwise you're optimizing things in a vacuum and you don't know what actually matters. That applies to research, it applies to product work, and I think it applies to support too — you want to see the full customer journey end-to-end before you go deep on optimizing any one part.


### What is a Challenge you had with a customer and how did you overcome it?


## **Questions I Have**

**Understanding the role**
- How would you define success in this role? What would be the key indicators to you that you hired the right person?
- What aspects of working here excite you the most?
- Is this a roll for the summer or for the winter
	- How would visas work for this and would we get it in time?
- What would the day to day look like for a customer support intern 
- How do you and the team decide when an issue is a one-off vs when it's a pattern worth flagging to engineering? Is there a formal process or is it more pattern-recognition over time?
- The job description talks about being the voice of the customer. Can you give me an example of a time customer feedback actually changed the product or a process?

**Thought about Matic Specifically**
- When customers run into edge cases — weird home layouts, unusual flooring, pets — how does the team approach those? Is it mostly real-time debugging, or do you build up a playbook over time?
- Privacy-first and on-device processing is a core part of the product. Does that ever create unique support challenges — like, you can't just pull logs from the cloud the way other companies can?

**Care about growth and the team**
- What does success look like for an intern in this role at the three-month mark? If we're sitting down at the end and you're telling me it went really well, what did I do?
- What's the part of the job you find hardest, even now?

**Bold**
- Mehul and Navneet have talked about wanting to build single-purpose robots first and add complexity over time. From a support perspective, do you think about your role as supporting Matic the vacuum, or supporting customers through Matic's broader robotics journey? Because those feel like pretty different jobs over a five-year horizon.