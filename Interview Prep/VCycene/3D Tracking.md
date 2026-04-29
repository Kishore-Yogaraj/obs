
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