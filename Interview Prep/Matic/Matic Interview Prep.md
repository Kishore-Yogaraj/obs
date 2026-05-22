

## **Behavioral**
### Tell me About Yourself
I'm a 3B mechanical engineering student at the University of Waterloo, with most of my focus the last three years going into robotics and autonomy.

The thread that runs through everything I've done is wanting to understand how robots actually work in the real world and not just in simulation. At Watonomous, our self-driving team at Waterloo, I went from being a perception engineer to co-captaining the team and leading 30+ engineers across our autonomy stack. A lot of that role was less about writing code and more about debugging hard problems with people and figuring out why a tracker was dropping objects, why latency spiked on one camera feed, coordinating across perception, planning, and integration to actually ship things that worked.

Last Winter I did a research term at TU Hamburg working on NeRF-based navigation for a differential drive robot. Before that I was at Eon Media in Toronto deploying computer vision pipelines for video analysis. So I've spent a lot of time recently thinking about what it actually takes to make a robot reliable enough to operate in messy, unpredictable real-world environments.

What pulled me toward Matic specifically is honestly the product. The on-device spatial AI approach, the privacy-first design, the fact that you're solving full-self-driving for the home using just RGB cameras — that's a hard problem, and the demos I've seen look like the robot actually works, which is rare in this space.

The reason I'm interested in the support role and not a pure engineering one is that I think customer support at a company like Matic is where the rubber meets the road. You see every weird edge case the engineering team didn't anticipate. You see what real homes look like versus the lab. And if I can troubleshoot a robot from a hardware, software, and algorithms perspective — which is what the job description asks for — I think I can do this job well and feed real signal back into the product. That's the kind of work I want to do.

### 3D Multi Object Tracking
One project I’m particularly proud of is the 3D multi-object tracking system I built, which was designed for tracking vehicles, pedestrians, and traffic signs in a self-driving car environment. The goal was to create a robust tracking system that could handle multiple objects in real-time, predict their movements, and account for situations where objects were temporarily occluded.

I used a combination of the **Kalman Filter** and the **Hungarian Algorithm** to solve this. The **Kalman Filter** is a mathematical model that helps predict an object’s future position based on its current state (like position, velocity) and update it as new data comes in. This was crucial for keeping track of moving objects even when they weren’t detected for a frame or two. The **Hungarian Algorithm** was used for data association—it matches detected objects in each frame with objects that were being tracked, ensuring that the system accurately tracks the same objects over time.

One of the most challenging parts of the project was handling situations where multiple objects were close together or when an object temporarily disappeared behind another (like a car passing behind a truck). The Kalman Filter allowed us to predict where the object would likely reappear, while the Hungarian Algorithm helped reassign detections when they re-emerged.

What I learned from this project is how crucial **data association** is when tracking multiple objects in real-world scenarios. You can't rely on the detection system alone because objects move, they might be missed in certain frames, or multiple objects can appear close together. It taught me the importance of combining different algorithms—like the Kalman Filter for prediction and the Hungarian Algorithm for matching—to create a more reliable system.

Additionally, I learned about the importance of real-time performance optimization. While the theory behind these algorithms is powerful, implementing them efficiently, so they run in real-time on self-driving cars, was a challenge that required a lot of testing and tuning.

### How do you handle Stress in the work environment
I handle stress by staying organized and maintaining a clear focus on priorities. When I’m faced with a challenging workload or tight deadlines, I break tasks down into manageable steps and set clear goals for each. This helps me stay on track and reduces the feeling of being overwhelmed. I also find that taking short breaks to reset, even just a few minutes away from the task, can help me come back with a fresh perspective and renewed focus.

Communication is also key for me. If a project becomes particularly demanding, I ensure to keep the lines of communication open with my team or stakeholders to manage expectations and seek input if needed. This has been especially important in roles like my time at Watonomous, where balancing multiple perception projects required careful time management and collaboration to ensure the team stayed aligned.

I view stress as a signal that something needs attention, and I try to channel it into problem-solving and maintaining a productive mindset. Additionally, I practice mindfulness techniques to keep a clear head and remain calm, even during high-pressure situations.

### How do you deal with conflict in a team and what is an example of you resolving a conflict
I believe the key to handling conflict on a team is open communication, active listening, and finding common ground. When conflicts arise, I focus on understanding the perspectives of everyone involved and working collaboratively to find a solution that benefits the project and the team. It’s important to address conflicts early before they escalate, and I strive to create a positive environment where team members feel comfortable expressing their views.

For example, while working at Watonomous as the perception lead, there was a disagreement between two team members over the best approach for implementing a 3D object tracking algorithm. One member wanted to stick with a Kalman Filter-based solution, while the other was advocating for trying a newer, more complex algorithm that they believed could provide better performance but required more development time. This disagreement was creating delays in moving forward with the project.

To resolve the conflict, I brought both team members together for a discussion where we could openly explore the pros and cons of each approach. We walked through the requirements, timelines, and the potential risks and benefits of each option. After facilitating this conversation, we decided to implement a test for both approaches on a smaller dataset to compare their performance. In the end, the Kalman Filter approach proved to be sufficient for the current phase of the project, while we noted the alternative method for future improvements.

By taking a collaborative, data-driven approach to resolving the conflict, we were able to move forward quickly, and both team members felt that their perspectives had been heard and respected.

## **Technical**
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

### Talk About Your Experience with Eon Media
During my time at Eon Media, I worked as an AI/ML developer, primarily focusing on integrating machine learning models into their media platform. One of the key projects I worked on was building a **facial recognition system** using **FaceNet**. This involved training the model to recognize and verify faces with high accuracy. I was responsible for the full pipeline—everything from preprocessing the images and training the model, to deploying it within the platform.

In addition to facial recognition, I worked on optimizing and fine-tuning other AI models to improve the platform’s ability to analyze and categorize large amounts of media content. This experience not only sharpened my skills in machine learning but also exposed me to the challenges of integrating AI models into real-world, production-level systems.

One of the key things I learned from my experience at Eon Media was the importance of balancing model performance with scalability. Working in a fast-paced media environment required our models to run efficiently on large datasets without sacrificing accuracy. It was a great learning experience in applying machine learning in a real-world setting, and I gained valuable insights into building models that can be scaled and deployed in production.

### How Does FaceNet Work
FaceNet is a deep learning model used for facial recognition and verification, but it works differently from traditional classification models. Instead of simply classifying faces, FaceNet maps each face into a **high-dimensional embedding space**, where the distance between embeddings represents the similarity between faces.

Here’s how it works:

- **Embedding Generation**: When an image of a face is passed through FaceNet, it doesn’t just classify the face; instead, it generates a 128-dimensional vector called an **embedding**. This embedding captures the unique features of the face.
    
- **Distance-Based Comparison**: Once the embeddings are created, the similarity between two faces is measured by calculating the **Euclidean distance** between their embeddings. If the distance is small, the faces are similar (i.e., the same person); if the distance is large, they belong to different people.
    
- **Training with Triplet Loss**: FaceNet is trained using a technique called **triplet loss**. During training, the model is given three types of images:
    
    1. **Anchor**: the reference face.
    2. **Positive**: a face of the same person as the anchor.
    3. **Negative**: a face of a different person.
    
    The goal is to minimize the distance between the anchor and positive embeddings while maximizing the distance between the anchor and negative embeddings. This helps the model learn to cluster faces of the same person closely together in the embedding space and push faces of different people further apart.
    

By using this embedding-based approach, FaceNet is not only great for recognizing who a person is but also for verifying whether two faces belong to the same person. This makes it highly effective for tasks like facial verification, clustering, and identification

### Talk About Your Experience with Watonomous
My experience with Watonomous has been incredibly rewarding, especially in my current role as **Co-Captain**, where I lead and manage a multidisciplinary team building cutting-edge autonomous systems — including self-driving cars, humanoid robots, and autonomous RC vehicles. This position has given me the opportunity to contribute both technically and organizationally across several subsystems, while driving the strategic direction of our projects.

One of the major technical initiatives I led was the development of a **3D object tracking system** using the **Kalman Filter** and **Hungarian Algorithm**, which enabled real-time tracking of vehicles and pedestrians. I also built a **YOLOv8-based object detection node** for recognizing traffic signs and cars, integrating it into our perception stack. This required tight collaboration with our hardware subteam to ensure sensor and compute system integration worked reliably on the vehicle.

As Co-Captain, I’ve worked across perception, planning, and systems teams to ensure seamless integration of components. I’m also heavily involved in project management—coordinating timelines, facilitating cross-functional syncs, and aligning our technical direction with the competition goals. A big part of my role involves mentoring junior members, onboarding new recruits, and ensuring that knowledge transfer and documentation are prioritized so that progress is sustainable.

This leadership experience has pushed me to grow not just as an engineer, but as a systems thinker and team builder. It’s strengthened my ability to take ownership of complex problems, communicate across disciplines, and deliver real-world solutions in the autonomous vehicle space.

## **Cool Things About the Company**

### Story with Dave
- Dealing with a wheel squeaking issue 
- Dave said he couldn't get back to the team because his father was sick
- Matic sent him flowers

### How Great Mehul is
![[Pasted image 20260521223029.png]]

### Matic Functions and Generalization

**Company & Origin**

- Founded by Mehul Nariyawala and Navneet Dalal
- Core insight: people don't want powerful, noisy, complicated vacuums with huge docking stations — they want one that's quiet and easy to operate
- Priced at $1,095 — roughly one-third the cost of other top-of-the-line robot vacuums

**Hardware**

- 8 inches tall, white and square with rounded corners
- Six RGB infrared cameras on the front, top, and back — sees in color and in darkness, so it doesn't need lights on (unlike vacuums with basic optical sensors)
- Runs on the NVIDIA Jetson Orin chip, which enables on-device learning of the home
- Suction power of 3,200 Pa — much lower than competitors (cheapest options exceed this; high-end models hit 20,000 Pa), but the reviewer says it doesn't matter in practice
- Small water tank built in
- Comes with a little plumber's snake accessory for clearing clogs in the tube
- Comes with stickers so you can give it a face (Wall-E vibe)

**Waste Bag (a standout design choice)**

- Contains diaper salts so it holds both wet and dry materials in one bag — no dirty water tank to empty
- Clear, so you can see how full it is
- HEPA filter is built into the bag itself, so you don't have to remember to replace filters separately on the vacuum

**Navigation & Mapping (the key differentiator)**

- Uses "long-term SLAM" / absolute mapping — builds a real mental map of the home, like a person would
- Most competitor vacuums use relative mapping (placing walls/obstacles relative to each other with various sensors)
- Because of absolute mapping, you can pick it up and place it anywhere in the house and it knows where it is and how to get back to the dock
- Initial mapping takes a few minutes
- Easy to add new rooms/spaces seamlessly — no need to remap the whole house
- Identifies floor surfaces on its own
- Identified a "stair" (a fireplace ledge) on its own — where other vacuums get trapped
- Learns how to navigate obstacles over time and fails gracefully instead of getting stuck and crying for help

**Cleaning Behavior**

- Three-step cleaning process: general area pass → edging pass → toe-kicks pass (under sinks, ovens)
- Cleans toe-kicks thoroughly — reviewer says no other vacuum has done this well
- Cleaning times: ~1 hour for a 12×15 ft living room; ~2 hours to vacuum + mop a 24×28 ft kitchen, bathroom, and laundry area
- Quiet enough to run while kids are watching TV

**App**

- Renders a weirdly accurate 3D model of the home — differentiates floor surfaces, furniture, and items several feet up
- Toggleable rendering styles to reduce visual clutter
- Joystick for manual control (reviewer says it's not as responsive as a gaming joystick — she prefers tapping/drawing on the map)
- "Airplane mode" lets you fly through the house at eye level instead of floor level
- Video recording feature for sending error footage to Matic's support team
- "Clean Area" + Mop: draw on the map and the vacuum goes straight there (reviewer used this for a Campari spill)
- "Go Here" feature: tap an opening on the map and Matic figures out how to get there
- Multi-user support — "Add Another User" in settings lets family members control the vacuum
- App is easy to use and shareable

**Privacy**

- All mapping data and analysis is processed locally between app and vacuum
- Data stays on your devices — no cloud processing

**Limitations the Article Calls Out**

- Will vacuum up Lego blocks (recognizes obstacles larger than 1 inch, but Lego is small enough to be sucked up)
- You have to tidy the house a bit before running it
- Reviewer fills a waste bag in ~3 days instead of the projected week (blamed on her dogs and kids)
- Missing some basic features at time of review: dirt detection, gesture controls, voice controls — Nariyawala said these are coming in software updates

**Overall Reception**

- WIRED gave it 10/10 — a rare perfect score
- Reviewer called it "game-changing" and the best robot vacuum she's ever tested

### Robert Scoble Interview

## **Questions I Have**
