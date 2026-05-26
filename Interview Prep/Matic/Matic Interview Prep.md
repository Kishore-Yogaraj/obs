

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

### Talk about your experience with Eon Media
Yeah, so Eon Media was my first co-op, back in fall of 2023 into early 2024. They're a Toronto-based media intelligence company — basically, they ingest huge volumes of video content from TV broadcasts, streaming, social media, and they extract structured data from it. Things like: which brands appeared in which shows, for how long, in what context. That data gets sold to advertisers and brand teams who want to understand where their logos are actually showing up.

I was a machine learning engineer intern, and my main project was building out their brand and logo recognition pipeline. The existing system was missing a lot of detections — partially occluded logos, weird angles, low-resolution clips — so I rebuilt the detection layer using YOLOv8 and added OpenCV-based pre-processing to handle the messier video inputs. The result was about a 30% improvement in detection accuracy, and we ran it across a back catalog of over 500,000 videos.

The other piece I worked on was the workflow side. Their training and evaluation processes were pretty manual — someone would kick off a training run, wait for it to finish, manually pull metrics, manually start the next one. I built out Python-based automation that let us run 10+ training experiments per week without someone babysitting them. That sounds boring but it actually mattered a lot — it meant the team could iterate way faster on model improvements.

A couple of things I took away from that experience that feel relevant here:

First, that was my first real exposure to deploying ML systems at scale — not toy datasets, not academic benchmarks, but actual production video pipelines processing hundreds of thousands of real-world inputs. And the lesson there is that real-world data is messy in ways you don't anticipate. The model that works on your validation set will hit edge cases in production that you've never seen — videos with logos partially obscured by lower-thirds, weird aspect ratios, broadcast compression artifacts. You only find those by actually shipping and watching what breaks.

Second, it was a small company — maybe 20-30 people at the time — so I got to see how a startup actually operates. I sat next to the engineers whose pipelines I was modifying, I'd hear customer feedback come in directly, I'd see when a model regression actually caused a problem for a paying customer. That experience of working close to the customer, even in an engineering role, is part of what's pulled me toward a support role at Matic — you see the full loop, not just your slice of it.

### What is a challenge you ran into at eon media and how did you overcome it?
Yeah, so the biggest challenge at Eon Media was actually about model evaluation, and it taught me a lesson I still think about.

When I first deployed the new YOLOv8 brand detection model, the metrics looked great. On our validation set we were hitting really strong precision and recall — better than the previous system across the board. I was feeling pretty good about it. We pushed it into the production pipeline that processes the back catalog of videos.

And within a week, the team started flagging issues. Customers were getting reports where major brands were missing — like, a Coca-Cola logo would be on screen for ten seconds and the system wouldn't catch it. Other times we'd get false positives — the model would label a random red shape as a brand logo because it kind of looked like one in the training distribution. The metrics said the model was better. The customers were saying it was worse. That's a really uncomfortable place to be when you're the person who shipped it.

So I had to back up and figure out what was actually going on. What I found, after digging into the failure cases, was that my validation set was biased. Most of the labeled training and validation data was on relatively clean footage — high-resolution, well-lit, logos roughly centered. But real broadcast video has all kinds of stuff going on — logos behind lower-third graphics, logos in motion blur during sports highlights, logos partially occluded by people moving in front of them, broadcast compression artifacts. My validation set just didn't represent the actual distribution of what the model was going to see in production.

Once I understood that, the fix was more about process than about the model itself. I worked with the team to put together a harder evaluation set — we deliberately sampled difficult cases from production: occluded logos, low-light scenes, broadcast graphics overlaying brands, motion blur. Anything that was breaking in the real world went into the eval set. Then I retrained with augmentation specifically targeting those failure modes — adding occlusion, brightness variation, compression artifacts during training so the model would see those cases before it hit them in production.

The model that came out of that second iteration was the one that actually got the 30% improvement number, and more importantly, it held up in production. The complaints from the team mostly went away.

The lesson I took from it — and this is the one I still think about — is that your evaluation is only as good as how well it represents reality. A model that crushes a clean test set but falls apart on real data is worse than useless, because it gives you false confidence. You have to go look at where it's actually breaking and feed that back into how you measure it. And honestly, the only way I learned that was by shipping something that broke and having to own it. You can read about distribution shift in a paper a hundred times, but it doesn't land until you're the one explaining to the team why production looks bad.

I think that applies pretty directly to a support role too. You can have a great triage process on paper, but it's only as good as how well it handles the actual cases coming in. The customers tell you where your process is wrong. Your job is to listen.

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

### 5 Bar Parallel Scara Project
Yeah, the project I'd want to talk about is my ME380 capstone — a 5-bar parallel SCARA pick-and-place robot. ME380 is the mechanical engineering design workshop at Waterloo, and the project ran through winter 2026. The goal was to build a robot arm that could autonomously identify colour-coded 3D-printed objects with an overhead camera, figure out where they were in the real world, pick them up, and sort them into the right drop zone — all within ±5 mm positional accuracy.

I was on a small team, and I owned the entire software stack. So that meant four pieces working together: the inverse kinematics solver, the computer vision pipeline, the simulation tools we used to validate the design, and the embedded control integration over UART to the ESP32.

The inverse kinematics side was a closed-form solution for the 5-bar parallel linkage — given an (x, y) target, the solver figures out the angles for both motors using the law of cosines, and I added a singularity guard that rejects any configuration where the linkage is within 15 degrees of fully extending or folding. That matters because near singularities the mechanical advantage collapses — small motor errors produce huge end-effector errors — so you want to eliminate that whole region from the workspace.

The computer vision pipeline was the piece I spent the most time on. It takes a frame from an overhead webcam, removes lens distortion using the camera's intrinsic calibration, segments objects by colour in HSV space — red is actually tricky because in HSV red wraps around both ends of the hue wheel, so you need two separate ranges combined with a bitwise OR — filters contours by area and shape solidity to throw out noise and partial detections, extracts the orientation of each object, and then transforms everything from pixel coordinates into real-world millimetres using a homography.

The reason I'm proud of this project specifically — there are a few reasons.

First, it actually worked. We hit the ±5 mm accuracy target, the robot successfully sorted both red and blue objects during the final demo, and the singularity guard meant the arm never tried a move that would have damaged the hardware. That's not always how capstone projects go.

Second, I owned the full software stack end-to-end. There was nowhere to hide — if vision was failing, that was mine; if the IK math was wrong, that was mine; if the serial protocol was glitching, that was mine. I had to be fluent across all of it, and I had to make architectural decisions about how those pieces talked to each other. The protocol I designed between the Python host and the ESP32 was deliberately minimal — just two comma-separated motor commands per line over UART — because it had to be easy to debug at three in the morning the week before the demo. Simple choices made under that kind of pressure are choices I'm proud of.

Third — and this is the one I keep coming back to — the biggest accuracy problem we ran into wasn't software at all. The camera was mounted too low, which introduced parallax error: because the objects had real height, the camera saw their tops displaced from where their bases actually sat on the workspace. And the error was direction-dependent — objects farther from directly under the camera deviated more. I could partially correct it in software, but the real fix would have been mounting the camera higher in the first place. That taught me that sensor geometry isn't a software problem you can paper over — it's a first-order design decision that has to be part of the mechanical conversation from day one. I think that lesson about the hardware-software boundary is something I'll carry into every robotics project I work on after this.

### What is an issue you faced with the project and how did you overcome it?
Yeah, the biggest issue I ran into on the SCARA project actually wasn't a software bug — it was a problem that exposed a flaw in how we'd thought about the design as a whole.

When we started doing pick tests on the assembled robot, the accuracy was off. Not catastrophically — the robot was getting close — but consistently off by enough that we were missing picks, especially toward the edges of the workspace. And the error wasn't random. It had a direction to it. Objects in the corners of the camera frame were always being targeted at a position offset _toward_ the centre of the workspace. The closer to the camera's centerline, the smaller the error.

My first instinct was that it had to be a software problem. I'd done the homography calibration, I'd done the camera intrinsic calibration to remove lens distortion, I'd built the orientation extraction with the Jacobian correction — everything on the software side felt like it should be right. So I spent maybe a day and a half re-checking the math, re-running calibrations, adding more debug visualizations, trying to find where my pipeline was wrong.

And I couldn't find it. The pipeline was right.

What finally clicked was when I actually put a ruler on the workspace and started measuring the error by hand. The pattern looked exactly like parallax — the kind of effect where if you look at a tall coffee cup from an angle, the rim looks displaced from where the base actually sits on the table. And the benchys we were picking had real height — maybe 40 millimetres tall. The overhead camera wasn't directly above the workspace, it was mounted at a height where there was a meaningful viewing angle from the centre out to the corners. So the camera was seeing the _top_ of each benchy, but the IK solver was being told to go to the position of the top, when it actually needed to go to the position of the base.

That's a problem you can't fully fix in software without knowing the exact height of every object in the frame on a per-pixel basis. The real solution was mechanical — the camera needed to be mounted significantly higher to shrink the viewing angle and make the parallax negligible.

So I had to go to my teammates with this. And the conversation was tricky because the camera mount had been designed and built weeks earlier — at the time, we'd treated camera placement as a "wherever fits" decision, not a precision-critical one. Asking them to redesign and reprint the mount that late in the project was a real ask. I tried to make it easy on them: I pulled my measurements, showed them the error pattern visually, and demonstrated with a simple geometric sketch why no amount of software work was going to fix this completely. I also offered a fallback — I could implement a per-pixel parallax correction in software using the object's known height and the camera's known geometry, which would get us most of the way there if mounting changes weren't feasible. That gave them a real choice rather than just a complaint.

In the end they were able to raise the camera enough to bring the worst-case error down into the range where the software correction could close the rest of the gap. And the system hit its ±5 mm accuracy target during the final demo.

The lesson I took from this is that when something's going wrong, your first instinct is to look in your own domain — for me as the software person, that meant assuming the bug was in my pipeline. But sometimes the problem is at the interface between domains, and the only way to find it is to actually go put your hands on the physical system, take measurements, and be willing to say "this isn't a software problem." The other lesson is about how you bring issues to teammates — coming in with data and a fallback option instead of just a problem makes it a much easier conversation.

I think both of those translate pretty directly to support work too. When a customer says something's broken, the easy thing is to assume it's the thing you know best — the firmware, or the app, or whatever. But sometimes the real issue is at the interface — between the robot and a weird floor surface, between the app and someone's WiFi setup, between user expectations and what the product actually does. The job is to be open to that, and when you find the answer, to bring it to engineering in a way that makes the fix easy to act on.


## Customer Support Role Interview Prep

### Why Customer Support

Honestly, two things. The first is that I've spent the last two years on the engineering side of autonomy — leading the perception team at Watonomous, doing NeRF research at TU Hamburg, deploying CV pipelines at Eon — and the loop I haven't lived in is the one where a real user's environment breaks your assumptions and that feedback makes it back into the code. I've built the stack. I want to see what happens when it meets a real living room. That's the part of robotics I'm most curious about right now.

The second is Matic specifically. I've followed the company since you came out of stealth in 2023, and I think you're doing something most robotics companies aren't — building actual trust between people and machines in their homes. That trust is the unlock for the whole industry. And the thing that convinced me Matic takes that seriously isn't the tech, it's stuff like Mehul sending flowers to Dave when his dad got sick mid-support-ticket. That's a company I want to learn how to do support at and see dominate the industry.

### Why Matic Specifically

Three things, but they all point at the same thing for me.

First, the technical bet. Most robotics companies attacking the home throw more sensors at the problem — LIDAR, ToF, structured light. Matic went the other direction: RGB-only, on-device neural networks, no cloud. That's a much harder bet to make and a much harder bet to execute, and the fact that Navneet — whose HOG paper is one of the most-cited papers in computer vision — is the one making it tells me they're not guessing. They're playing a game most companies can't play.

Second, the stage. There's a line from your LinkedIn post about joining Matic that stuck with me — that the flywheel is already turning and gaining momentum by the day. That's exactly it. You're past stealth, past v1, deploying in real homes, and the loop from customer data back into autonomy is the engine. That moment only happens once in a hardware company's life, and it's the moment where support has the most leverage on the product. I want to be in the room for that.

Third, the team. The Flutter exit, the investor list — Collison (Stripe Cofounders), Friedman (Former CEO of Github), Dorsey (Founder of twitter and block), Rogers (Co founder of Nest) — those aren't people who back robotics companies casually. And then there's the Dave story, which to me is the proof that the customer obsession isn't a slide in a pitch deck.

The common thread is that Matic is one of the very few home robotics companies actually positioned to win. I want to learn from a team that's winning.

### What do you know about the product

At the basic level — RGB-only, on-device autonomy, no LIDAR, no cloud. Five to six infrared-capable RGB cameras feeding a Jetson Orin Nano, building photorealistic 3D maps with long-term SLAM, all running locally. It vacuums and mops in a single pass, uses the camera system to recognize carpets and avoid soaking them, and runs at around 55dB — quieter than a conversation.

But the more interesting thing to me is what those choices _mean_ for customers. The on-device compute means privacy is structural, not a policy — a customer's home data physically never leaves the robot. The long-term SLAM means if someone picks the robot up and moves it to a different room, it relocalizes instead of breaking — which removes a huge class of support tickets. The infrared cameras mean it works in the dark, so customers can run it overnight without thinking about lighting.

The trade-off I find honest is the height. At 7.8 inches, it doesn't fit under most beds or low sofas. That's a real limitation, and the fact that Matic ships the product with that constraint instead of compromising the camera stack to fix it tells me a lot about how the team makes decisions. They optimized for the thing that matters most — actually mapping the home well — and accepted a known cost. That's the kind of trade-off I'd want to be able to explain clearly to a customer who asks about it.

### Pros and Cons of Matic Robots

### Pros

**Raw cleaning is class-leading.** 8.11 kPa suction on max power — the highest score Vacuum Wars has ever recorded out of nearly 200 robot vacuums tested, roughly eight times the 0.86 kPa average. Airflow of 35 CFM, more than double the 17 CFM average and the second-highest ever recorded. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Pet hair pickup is perfect.** A perfect 100% score on the 2½-inch flattened pet hair pickup test, compared to the 81% average. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Mopping is above average and water-efficient.** Scored 147 points on dried-on stain testing while using only 0.8 g of water, for a combined score of 263 vs. the 184 average. Uses its camera system to recognize and avoid carpets during mopping, allowing it to vacuum and mop mixed flooring in a single run without wetting carpets. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)[Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Hair tangle resistance is excellent.** In the 7-inch hair test, only about 7% of hair wrapped around its brush, compared to a 28% average. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Massive dust capacity.** 1,000 ml disposable bag vs. the 400 ml average — technically the largest onboard dustbin capacity Vacuum Wars has tested. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Quiet operation.** Around 55 dB, noticeably quieter than many competitors. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Privacy + on-device intelligence.** Five RGB-IR cameras with SLAM creating photorealistic 3D maps stored entirely on-device — nothing uploaded to the cloud. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Graceful movement.** Two large independently driven wheels give it a smart, deliberate, and unusually graceful movement pattern. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

### Cons

**No multifunctional dock.** Nearly all premium robot vacuums now include a base station that empties the internal dustbin, washes and dries mop pads, manages water tanks, and reduces required maintenance. The Matic simply does not offer it. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Tall — can't fit under furniture.** At 7.8 inches, it is the tallest robot vacuum Vacuum Wars has tested, compared to an average height of 3.7 inches. In many homes, the Matic simply cannot reach under most beds or couches. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Moisture + odor in the bag.** The roller-style mop pad squeezes out dirty water that collects in the same bag that stores dirt and debris. After about a week of typical use, the smell became noticeable. Owners will need to replace the bag more frequently than with other systems. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Slow navigation.** In navigation efficiency testing, the Matic covered only 0.52 square meters per minute, significantly below the 0.75 average. A floor plan that typically took other robot vacuums about an hour took the Matic 227 minutes to complete. Even partial cleanings routinely ran two hours. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Below-average obstacle avoidance in the lab.** In standardized avoidance testing, it avoided 14 of 24 objects, slightly below the average of 17 — although during real-world use, obstacle avoidance felt noticeably better than the test score suggests. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)[Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Limited coverage per charge.** Estimated coverage is about 755 square feet per charge — slow navigation severely limits the square footage it can clean on a single charge. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

**Didn't make the Top 20 list.** After compiling all testing data, the Matic did not qualify for the Vacuum Wars Top 20 Robot Vacuums list. [Vacuum Wars](https://vacuumwars.com/matic-robot-vacuum-review/)

### Matic vs Dreame X60 Ultra Max
Honestly, I'd start by validating that the Dreame X60 is a genuinely great robot — it just topped the Vacuum Wars list, and pretending otherwise would lose the customer's trust immediately. What I'd then explain is that they're solving different problems.

Dreame is the best-refined version of the existing robot vacuum paradigm. They've spent years iterating on LiDAR-based navigation with cloud-assisted intelligence and increasingly elaborate docks. If what you want is a polished version of what a robot vacuum has been for the last five years, with maximum convenience and minimal maintenance, the X60 is a fantastic choice.

Matic is a different bet. We rebuilt from scratch around three constraints: the robot should actually understand your home in 3D, all the intelligence should run on-device so your data never leaves the house, and the product should keep getting better through software because the autonomy is the product. The 1,000 mL bag, the on-device compute, the camera-only navigation — those choices come with trade-offs the customer should know about: it's taller, it's slower today, and we don't have an auto-empty dock yet. But the architecture is the one that gets better over time, and a customer's Matic today won't be the same Matic in 12 months.

The honest framing is: if convenience-first matters more than anything, Dreame is probably the right call right now. If you care about owning a robot that learns your home and gets smarter, and you care about your data staying private, Matic is the bet.

![[Pasted image 20260525212422.png]]

### The honest read

**Where the Dreame X60 wins decisively:**
- Auto-empty dock and overall convenience (huge — this is the dominant trend in the premium market)
- Obstacle avoidance (22/24 vs. 14/24)
- Height — fits under furniture (3.13" vs. 7.8")
- Threshold climbing
- Speed of cleaning / coverage per charge
- Mature app ecosystem, Matter, voice assistants
- Overall "polish" and feature breadth — it's a refined 5th-gen flagship from a giant that ships millions of robots

**Where Matic wins decisively:**
- Raw suction (the highest VW has ever recorded)
- Pet hair pickup (perfect — but so is Dreame)
- Dustbin capacity (4x larger)
- Privacy (on-device, no cloud — Dreame is cloud-connected)
- Quiet operation (55 dB)
- Vacuum-and-mop in a single pass without soaking carpets
- Software-improvable architecture — the navigation will get faster over time, the LiDAR-based architecture is more locked in
- It's $500 cheaper
- It's made by an independent US company, not a Chinese consumer electronics giant



### Framework for Situational Questions
Scenario questions like this aren't tests of whether you know the right "answer" — they're tests of whether you have a **mental model for how to do support**. He's listening for five things:
1. **Customer-first reflex.** Do you acknowledge the human before diving into diagnostics?
2. **Diagnostic discipline.** Do you actually try to understand the problem, or do you guess?
3. **Pattern thinking.** Do you ask "is this just this customer, or is this a class of issue?"
4. **Cross-functional instinct.** Do you know when and how to involve engineering / product?
5. **Closing the loop.** Do you follow up with the customer _and_ feed insight back into the product?

### A customer emails saying their Matic 'keeps bumping into the same chair leg.' Walk me through how you'd handle the ticket from open to close.

**1. Acknowledge (first hour)**

- Reply quickly. Even a holding response within an hour matters. "Hey [name], thanks for flagging this — sorry it's been frustrating. I want to dig into this with you. Can you send me a few details so I can figure out what's going on?"
- Tone matters: warm, owns the problem, doesn't make excuses.

**2. Diagnose (gather info before acting)** This is where your technical background actually shines. You don't guess — you ask the right questions. For a "bumps into chair leg" issue, things to figure out:

- _Which chair?_ Is it metal, glass, transparent, thin-legged? Cameras can struggle with reflective, transparent, or very thin objects.
- _Lighting conditions?_ The cameras are RGB-IR, so they should work in the dark — but is the chair near a backlit window where the cameras might be glare-affected?
- _When did it start?_ Was it always like this, or did it start after a software update / after they moved the chair / after a new rug?
- _Every time or sometimes?_ Consistent failure is different from intermittent.
- _Can they share the cleaning log or video?_ Matic's app shows the path — the visual evidence is gold.
- _Is the chair in the 3D map correctly?_ Sometimes the map is fine but the planner is wrong; sometimes the map itself is wrong.

This is where you _show_ technical credibility without flexing. You're not impressing them with jargon — you're asking diagnostic questions that demonstrate you understand the product.

**3. Resolve (act on the diagnosis)**

- Easy cases: clean cameras, re-map, software update, move the chair temporarily.
- Medium cases: walk them through a re-mapping flow with you on the phone/email, escalate to engineering for log analysis.
- Hard cases: this is a real bug → loop in the perception team with the customer's logs, give the customer a clear timeline ("Engineering is looking at your logs; I'll have an update by Friday"), maybe offer a workaround in the meantime (no-go zone around the chair).
- **Never leave them hanging.** Even if the answer is "we're investigating," they should hear from you on a clear cadence.

**4. Document and tag the ticket properly**

- The JD specifically mentions: "Ensure that tickets are accurately tagged to enable the gathering of meaningful insights." This is the part where you mention it.
- Tag: obstacle avoidance, furniture type, room type, software version. So that next month when someone says "we keep getting tickets about chairs," you can pull the data.

**5. Feed back to the product / engineering team**

- One ticket about a chair leg is data. Three is a signal. Ten is a roadmap item.
- "If I see this pattern repeating, I want to bring it to perception with the cluster of examples, not just one — that's how it turns into a product fix rather than a one-off support resolution."

Sure. I'd think about it in five stages: acknowledge, diagnose, resolve, document, and feed back.

First thing — within an hour, even if I don't have an answer, the customer hears from me. Just a 'thanks for flagging this, sorry it's been frustrating, I want to dig in — can you share a few details?' Tone matters here: I own the problem, I don't make excuses.

Then I diagnose before I act. For 'bumping into the same chair leg' specifically, I'm asking: what kind of chair is it — metal, glass, thin legs? Because camera-based systems can struggle with reflective and transparent objects, and Matic is camera-only. Is it backlit by a window? Did this start after a software update or after they moved furniture? Is it every cleaning or intermittent? And can they share the cleaning log or a video — the app's 3D map will tell me whether the chair is mapped correctly, or whether the map is fine and the planner is failing. That's two very different bugs, and the fix is different.

Then I resolve. If it's something simple — dirty cameras, an outdated firmware, a re-mapping needed — I walk them through it directly. If it's a real edge case for perception, I escalate to the engineering team with the customer's logs, and I always give the customer a clear timeline so they're not wondering. And critically — I always offer an immediate workaround. In this case, that's setting a no-go zone around the chair in the app. They feel helped now, not just 'we'll look into it.'

Then documentation. The JD specifically mentions tagging tickets accurately, and I think that's where this gets interesting. I'd tag this with the furniture type, the room context, the software version — so that next month when ten more customers report chair-leg issues, we can pull the cluster and bring it to perception as a pattern, not just ten individual tickets.

Last — closing the loop. I'd follow up with the customer a week after fixing it, not just at resolution. 'Hey, just checking — has it been clean since?' That last touch is what turns a customer who tolerated the bug into a customer who tells their friends about Matic.

### You notice three different customers this week reporting the robot getting stuck under the same type of low-clearance furniture. What do you do?

The first thing I'd notice is that three reports of the same thing in one week isn't three tickets — it's a signal. So I'd actually run two things in parallel.

Track one: each of those three customers needs a real response today, not next week when engineering has a fix. I'd acknowledge each one individually, offer an immediate workaround — a no-go zone around the furniture, or lifting the robot manually until we resolve it — and tell them honestly that we've seen this pattern, we're escalating, and I'll come back with an update.

Track two: before I take this to engineering, I do my homework. I want to bring them a cluster, not a panic. What's the furniture exactly — same type, same clearance height? Same firmware version on all three robots? Same map characteristics? Are there more cases in the queue I haven't been tagged on? Once I have that, I bring it to perception with the data: 'three customers this week, all sub-8-inch clearance, all firmware X, all logs attached, here's my hypothesis — what's yours?' That's how I earn the engineering team's trust over time, by bringing them signal rather than noise.
One thing I'd flag here specifically — the Matic is the tallest robot vacuum on the market at 7.8 inches. That's a deliberate trade-off, and 'getting stuck under low furniture' might not be a bug — it might be the mapping system letting the robot enter a space that's borderline navigable. Which means the fix might be at the mapping level, not the planner level. That's a much more interesting conversation with engineering.

Then — and this is the part most people would miss — if three customers hit this in a week, more customers will hit it next week. So I'd also: prep a workaround response for the rest of the support team so we're consistent, work with product on whether we update the in-app messaging or onboarding to flag clearance limits, and ask whether we can proactively notify customers whose mapped homes have similar clearance characteristics. That's the move from reactive to proactive, and it's the only kind of support that scales as Matic grows."

### A customer is angry — they say the robot mapped their house wrong and missed a whole room. How do you respond, and what do you do internally?

### The structure

Three things, in this order, separated cleanly:

**1. Validate (genuinely, briefly).** Acknowledge the experience and the frustration without being saccharine about it. "That's a fair frustration — when you buy a robot to clean your house and it misses a room, that's the basic promise of the product not being met." Then _pause_ and let them respond. Don't barrel into diagnostics.

**2. Own it.** Don't blame the customer ("did you re-map after moving the furniture?"), don't blame engineering ("yeah this is a known issue"), don't deflect to the product ("well technically..."). Just take ownership. "I want to figure this out with you and make it right."

**3. Then diagnose.** Now you can ask the technical questions. Now the customer is _with_ you, not _against_ you.

For the **internal** action, the moves are the same as the previous scenarios — diagnose, escalate if needed, document — but with one extra: **flag this as a churn risk**, because angry customers are the ones who leave reviews, cancel orders, or tell their friends. That deserves a different level of internal attention than a customer who's just confused.

I'd separate the response into two parts, because I think the question itself has two parts.

Externally, before anything technical, I'd lead with validation — not scripted empathy, but genuine. Something like, 'I'm sorry, that's a real frustration. You bought a robot to clean your house and it skipped a whole room — that's the basic promise of the product not being met, and I'd be annoyed too.' Then I'd pause. The biggest mistake people make with angry customers is barreling straight into diagnostics. The customer needs to feel heard before they can hear me.

Then I'd own it. Not blame the customer for not re-mapping, not throw engineering under the bus, not get defensive about the product. Just: 'I want to figure this out with you and make it right.'

Then — and only then — I'd diagnose. For a missing room specifically, I'd ask the practical questions: was the door open or closed during the initial mapping pass? Is the room darker than the rest of the house — basement, closet, blackout-curtain situation? Has it always been missing, or did it disappear after a recent re-map? Those three questions cover the most likely causes, and 'door was closed during mapping' is the most common one — which is reassuring for the customer because it means the robot works, it just needs a fresh map.

Internally, I'd run the same playbook as any other ticket — diagnose, escalate to perception if I can't resolve it, document with logs. But I'd also flag this as a churn risk. An angry customer who feels unheard leaves reviews, cancels, and tells their friends. So I'd be more proactive than usual: follow up the same day with a fix or a timeline, follow up again after resolution, and if it turns out to be a real bug rather than a re-map issue, I'd want to involve someone more senior to make sure we make it right — whether that's a personal note from someone on the team, a small gesture, whatever's appropriate. The Dave-and-the-flowers story is the standard, not the exception.

The one thing I wouldn't do — and I think this is the biggest trap — is over-promise. If I don't know yet, I say I don't know yet, but I'm on it and they'll hear from me by [time]. Angry customers respect honesty way more than vague reassurance.

### You suspect a bug, engineering says it's working as designed. How do you handle it?

### The structure

Four moves:

**1. Take engineering's verdict seriously.** Don't dismiss "working as designed." Understand _why_ they're saying it. Maybe there's a performance trade-off you didn't see. Maybe the behavior is intentional for a good reason. Ask them to explain it, genuinely.

**2. Reframe the question.** If engineering is right that it's working as designed, the question isn't "is this a bug" — it's "does the design match customer expectations?" That's a different question, with a different owner (product / leadership), and a different decision-making process.

**3. Bring data to the reframe.** Not "one customer is unhappy" — but "here are five tickets in two weeks, here's the impact on customer satisfaction, here's what they expected, here's what the product does, here's the gap." Make it impossible for product or leadership to dismiss the pattern.

**4. Take care of the customer in the meantime.** While you're working the systemic issue, the customer in front of you still needs an answer. Be honest with them: explain why the product does what it does, offer a workaround if there is one, set realistic expectations, and let them know you're advocating for the change.

There's also a **fifth move** for the truly great answer: **acknowledge engineering might be right about the customer.** Maybe the customer's expectation actually _is_ unreasonable. Maybe they want the robot to do something it was never designed to do. In that case, your job is to help the customer understand the product's actual capabilities, gracefully — and feed back to product whether the marketing or onboarding is over-promising.

I think this is one of the most interesting situations in support, because 'bug' and 'working as designed' aren't actually opposites — and treating them as opposites is the first mistake.

My first move would be to take engineering's verdict seriously. Not 'okay sure, I'll tell the customer,' but actually understanding _why_ they're saying it. Maybe there's a performance trade-off I didn't see. Maybe the behavior is intentional for a reason that's defensible. Engineers are closest to the system; their perspective deserves real weight. So I'd ask them to walk me through it — what's the design intent, what's the trade-off, why is this the chosen behavior?

If after that conversation I still think there's a gap, I'd reframe the question. The question isn't 'is this a bug' — it's 'does the design match what customers actually need?' Because engineering might be completely right that the system is doing what the spec says, _and_ the spec might still be wrong. That's a product question, not a bug question, and it has a different owner.

Then I'd bring data to the reframe. Not 'this customer is upset' — but 'here are five tickets in two weeks where customers had the same expectation, here's the impact on satisfaction or churn, here's the gap between what they expected and what we deliver.' That's the kind of signal product and engineering can act on together. Without data, I'm just one person's opinion against engineering's; with data, I'm bringing forward what customers are telling us.

The other thing I'd hold open is that engineering might be right about the customer too. Maybe the customer's expectation actually is unreasonable, or based on something they saw in a competitor that Matic doesn't promise. In that case my job is to help the customer understand the product gracefully, _and_ feed back to product whether our marketing or onboarding is setting up the wrong expectation. That's still a fix — it's just a different kind of fix.

And throughout all of this, the customer in front of me still needs an answer. So I'd be honest with them: explain why the product behaves the way it does, offer any workaround that exists, set realistic expectations, and tell them I'm advocating for the change internally without over-promising that it'll happen.

The thing I'd never do is throw engineering under the bus to the customer — 'yeah engineering says it's working as designed' is the worst possible thing to say. That tells the customer the company doesn't have their back. The framing should always be 'this is how the product works today, here's why, here's what I'm doing about it on your behalf

### Machine has Smell
This one's interesting because it's not a bug — it's a real, known characteristic of the product. So my approach is different from a 'something broke' ticket.

First, I'd acknowledge the specific experience. Not just 'sorry that's frustrating' — but 'yeah, the smell is real, you're not imagining it, and I've heard this from other customers too.' Customers can tell when an acknowledgment is generic versus when it's specifically validating what they're experiencing.

Then I'd be honest about the cause. Most premium robot vacuums avoid this issue because they have a big multifunction dock that auto-empties the dustbin and stores wet and dry separately. Matic made a different architectural choice — everything is onboard, which is what lets the robot vacuum and mop in a single pass and skip the giant dock. The trade-off is that the same bag stores wet mop residue and dry debris together, and over a week or two that combination starts to smell. The team has tried to mitigate it — there's a gelling salt to solidify liquid waste, antibacterial agents, and the robot applies a drying cycle after each run — but those measures reduce the problem, they don't eliminate it.

Then I'd offer what I actually can. The most practical answer is changing the bag more frequently — every week or so for high-usage households, rather than waiting until it's full. If they're doing a lot of mopping, even more frequent. I'd also make sure they know about the drying cycle and that running it consistently helps. None of that is a perfect fix, but it's a real workaround that meaningfully reduces the issue.

And I'd be honest about the bigger picture. I'd tell them this is something the company is aware of and actively thinking about, but I wouldn't promise a specific fix on a specific timeline, because I don't actually know that and promising it would just create another problem later. What I _would_ commit to is making sure their feedback gets logged with the pattern, so when product is making decisions about future versions or about reformulating the bag, customer feedback like theirs is part of that decision.

Internally, this is exactly the kind of issue I'd want to be tracking carefully. One ticket about smell is one customer. Twenty tickets is a product roadmap item. So I'd be tagging these consistently and making sure the pattern is visible to product — because the right fix here is probably at the design level, not at the support level. Support can give workarounds; only product can give a real solution.

*"I'd take that seriously and not get defensive about it. If they bought the product expecting it to not have smell issues — which is reasonable, because most premium robot vacuums don't have this problem — then we set the wrong expectation, not them. I'd want to understand whether their issue is fundamentally with the smell, in which case a refund might be the right answer, or whether it's that they want it fixed and don't think we're moving fast enough, in which case the conversation is about urgency and timeline.*

*Either way, I'd loop in whoever owns refund decisions — I don't think that's my call to make unilaterally as an intern — but I'd come to that conversation with a clear recommendation, because the person who actually talked to the customer is the one with the best read on what they need."*

### Kalman Filter Customer Breakdown

Sure — I'd start with what a Kalman filter actually is in everyday terms, then connect it to what I built.

Imagine you're driving down a highway. Your speedometer says 60, but you can also look out the window and see how fast you're passing cars. Your brain combines those two imperfect signals into a better estimate than either one alone gives you. That's the core of what a Kalman filter does — it combines multiple noisy pieces of information into one confident answer, continuously, many times a second.

The project I used it on was for the autonomous car team at Waterloo. The car has multiple cameras, and at any given moment it's seeing other cars, pedestrians, cyclists — up to twenty objects at once. The challenge is that a camera frame is just one snapshot, and each snapshot is noisy. A pedestrian might be partially blocked behind a sign for a moment, or a car might be hard to see in glare. What I built was a system that took all those imperfect camera readings and stitched them into a _consistent track_ of each object — so the car knew not just 'there's a pedestrian there right now' but 'this is the same pedestrian I saw two seconds ago, this is where they're heading.'

The reason I'd bring it up in the Matic context is that the underlying problem is really similar. Your Matic has five cameras. At any moment, some of them see your couch clearly, and some don't. The robot's job is to combine those imperfect views into one confident picture of where everything in your home is. Same kind of math, different application.

The shortest version: it's how robots take imperfect sensor data and turn it into reliable decisions.
### Nerf Breakdown
Sure. Same approach — let me explain the idea first, then how I used it.

The concept: imagine walking into a friend's living room for the first time. You only see it from wherever you happen to be standing — a few angles, never the whole room at once. But after a couple of minutes, you have a complete mental picture. You could close your eyes and point to where the couch is, or imagine what it looks like from the other chair, even though you never literally stood there. Your brain combined the partial views you _did_ see into a full 3D understanding.

A NeRF — Neural Radiance Field — is a way of getting a computer to do that same thing. You give it photos of a space from a bunch of different angles, and it learns a complete 3D model of the space. You can then 'look at' the space from any angle, including ones you never photographed.

The project I used it on was at TU Hamburg. We had a small wheeled robot in simulation that needed to navigate a 3D environment using only its cameras — no LIDAR. What I built took the 3D model the NeRF produced and converted it into the simpler kind of map a navigation system needs to plan a path. So the robot was essentially using the same trick your brain uses — building a 3D understanding from a series of 2D glimpses — to figure out how to get from one side of a room to the other safely.

The reason this matters for the Matic conversation is the underlying bet is the same. The Matic uses cameras only — no LIDAR, no special sensors. Whether or not it uses NeRF specifically, the _idea_ is the same family: building a rich 3D understanding of a home from partial 2D views, and using that understanding to navigate intelligently. The work I did was an early version of the same problem Matic is solving in real homes today.

Shortest version: it's how a robot builds a complete picture of a space from incomplete glimpses.

### Generalist Customer Service Questions
### 1. How do you handle difficult customers?

> "The frame I'd start with: difficult customers are almost never difficult for no reason. Usually they're frustrated because something stopped working, or they've been bounced around, or they feel unheard. So my first move is always to figure out which of those it is.
> 
> Practically — I'd start by genuinely acknowledging the frustration without making it sound scripted. Then I'd let them talk. A lot of 'difficult' customers de-escalate themselves if they actually feel heard for the first thirty seconds. Then I'd take ownership and tell them what I'm going to do, with a clear timeline. The two things I'd never do are get defensive or over-promise — both make it worse.
> 
> And honestly, the customers who are hardest in the moment are often the most loyal afterwards, because they remember the company that actually fixed it."


### 2. How do you prioritize multiple customer requests?

> "I'd think about three axes: severity, customer impact, and time-sensitivity.
> 
> Severity is about whether the robot is broken or merely annoying — a robot that won't run at all is a higher priority than one that's missing a corner. Customer impact is who's affected — one customer or a pattern across many. Time-sensitivity is whether there's a deadline or escalation risk — someone threatening a chargeback or about to leave a public review is different from someone patiently waiting.
> 
> In practice, I'd triage incoming tickets quickly into something like 'fire,' 'urgent,' and 'standard,' and work fires first. But I'd also batch similar issues — if I have five tickets about the same furniture issue, I'd handle them together because the diagnostic work is shared.
> 
> The thing I'd protect against is letting the loudest customer always win. Sometimes the quiet customer with a more serious issue is the one who actually needs attention first."


### 3. How would you handle a situation where a customer is not satisfied with your solution?

> "First thing — I'd take it seriously and not get defensive about the solution. If the customer isn't satisfied, the solution didn't work for them, even if technically it 'should' have. That's information, not pushback.
> 
> Then I'd actually ask them what would make it right. A lot of the time people don't expect the actual problem to be solved perfectly — they want acknowledgment, a workaround, or some sign the company cares. Sometimes what they want is reasonable and easy. Sometimes it's not, and I'd be honest about what's possible and what isn't, but I'd never just say 'sorry, that's our policy.'
> 
> If we genuinely can't give them what they want, I'd escalate — both to make sure the right person weighs in, and so the customer feels they've been heard at a higher level than just me. And I'd document what didn't work, so next time we know the original solution wasn't sufficient for that class of issue."

### What would you do if you didn't know the answer to a customer's question?

> "Tell them I don't know.
> 
> Honestly — the worst thing I could do is make something up or stall with vague language. Customers can tell, and it destroys trust faster than the original question ever could. So I'd just say 'I don't know off the top of my head, but I'll find out and get back to you by [specific time].'
> 
> Then I'd actually figure it out — first by checking internal documentation or past tickets, then by asking the right teammate or engineer. And when I came back to the customer, I'd give them the answer plus a bit of context about _why_ it's that way, not just the bare answer.
> 
> The one thing I'd watch for is patterns. If three customers in a week ask me the same question I didn't know the answer to, that's a sign our documentation has a gap, and I'd flag it. 'I don't know' is fine once; it shouldn't be 'I don't know' five times for the same thing."


### Questions for Vivek
#### Vivek's Transition and Perspective
- You've been here two months. What's surprised you most about how Matic operates compared to TerraClear?
- You've now done this twice — TerraClear and Matic — deploying autonomous hardware into real-world environments your team doesn't control. What's the pattern in what breaks first when customers actually get the product?
- At TerraClear and at Bain, you've operated in roles where success looks completely different — building something from scratch versus advising. What's the part of your operating style that's been most transferable across those different contexts?

#### Questions about the role and what success looks like
- What does a great intern look like three months in? What would make you say at the end, 'we should've hired this person full-time'?
- How does the feedback loop from support tickets back into engineering actually work today? Is it formalized, or is it still figuring itself out?
- What's the hardest part of this role that isn't obvious from the job description?

#### Company Bets and Strategy
- Matic made a real bet on RGB-only, on-device, no LIDAR. From an operations perspective, what does that bet _cost_ you that customers don't see? Like, what's the operational tax of that engineering choice?
- As Matic scales and ticket volume grows, what do you think breaks first about how support works today?
- Matic talks about being a flat, no-politics organization. Two months in, what's been the reality of that? What does it look like in practice when there's actual disagreement?
- How do you think about the line between 'this customer issue is a one-off' and 'this is a product problem that needs to escalate'? Who actually owns that judgment day-to-day?





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


## **Questions I Have**

**Understanding the role**
- How would you define success in this role? What would be the key indicators to you that you hired the right person?
- **What aspects of working here excite you the most?**
- Is this a roll for the summer or for the winter
	- How would visas work for this and would we get it in time?
- What would the day to day look like for a customer support intern 
- How do you and the team decide when an issue is a one-off vs when it's a pattern worth flagging to engineering? Is there a formal process or is it more pattern-recognition over time?
- The job description talks about being the voice of the customer. Can you give me an example of a time customer feedback actually changed the product or a process?

**Thought about Matic Specifically**
- When customers run into edge cases — weird home layouts, unusual flooring, pets — how does the team approach those? Is it mostly real-time debugging, or do you build up a playbook over time?
- **Privacy-first and on-device processing is a core part of the product. Does that ever create unique support challenges — like, you can't just pull logs from the cloud the way other companies can?**

**Care about growth and the team**
- **What does success look like for an intern in this role at the three-month mark? If we're sitting down at the end and you're telling me it went really well, what did I do?**
- What's the part of the job you find hardest, even now?

**Bold**
- Mehul and Navneet have talked about wanting to build single-purpose robots first and add complexity over time. From a support perspective, do you think about your role as supporting Matic the vacuum, or supporting customers through Matic's broader robotics journey? Because those feel like pretty different jobs over a five-year horizon.