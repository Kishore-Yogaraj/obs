### Goal

This project is to use AI and Machine Learning tools to create an application that can track different volleyball stats. This stats include:

- Kills
- Blocks
- Passes
- Sets
- Serves
- Location of all actions mentioned above
- Cut up game footage to only show rallies

This application will be used for indoor volleyball and used to track one team. Camera should be placed directly behind the team that stats need to be collected for.

### Specifics

- This project should start off small so that it can be put on the resume
- The application should be able to track each individual player and the ball in order to pick up appropriate stats
- This project will be responsible for picking up the following stats
    - Attacks
    - Digs
    - Sets
- The application should also map the player that performed said action as mentioned above and have it displayer to a csv (temporary)
- Making the decision for this application to be a post-match analysis because of the computational requirements and algorithm efficiencies in real-time analysis

### High Level User Flow

- User records full match of volleyball from the correct camera angle
- User inputs the video into the application
- Application processes the video
- Application outputs stats of digs, sets, and attacks for each individual player
- Tracker should also record the segments that these stats were recorded and add it to a section with a “player highlight reel” (bonus)

### Technical Application Steps

- Find a short video of a player successfully attacking a volleyball
- Develop a model that will identify this as an attack and record the data
- You will have to build a mode that tracks the ball as well as the player itself
- You’ll need to train the model on what an attack looks like
- The model will then have to use this dataset to determine if there was a successful attack in the video
- Important things to consider:
    - Player and ball tracking (first)
        1. **Objective**: Create a system that can identify and track the positions of players and the ball throughout the match.
        2. **Approach**:
            - Use object detection algorithms (like YOLO, SSD) to identify players and the ball in each frame.
            - Implement tracking algorithms (like Deep SORT) to track their movement over time.
        3. **Outcome**: A reliable tracking system where each player and the ball can be consistently followed across the frames of a video.
    - Action recognition (second)
        1. **Objective**: Build a model capable of recognizing key volleyball actions (like spikes, sets, digs, and blocks) from video footage.
        2. **Approach**:
            - Start with frame-by-frame analysis to identify when specific actions occur.
            - Explore using convolutional neural networks (CNNs), possibly combined with temporal models like LSTMs for understanding sequences of movements.
        3. **Outcome**: A model that can accurately identify and classify different volleyball actions in video footage.

Tutorial for object detection with YoloV8 model
    - [https://www.youtube.com/watch?v=m9fH9OWn8YM](https://www.youtube.com/watch?v=m9fH9OWn8YM)
- Find a way to classify each player by jersey number so that stats can be tracked for each individual player
    - Need to find a way to detect and classify as soon as a video is inputted because you can’t train the model on just players that have their backs towards the camera

[https://blog.roboflow.com/train-a-custom-yolov8-pose-estimation-model/amp/](https://blog.roboflow.com/train-a-custom-yolov8-pose-estimation-model/amp/)

[https://www.youtube.com/watch?v=L23oIHZE14w&t=11364s](https://www.youtube.com/watch?v=L23oIHZE14w&t=11364s)

- We’re going to end up using pose estimation with a yolo model to track the movements of players and recognize their actions
- We can maybe do something like eon media where we run a video through multiple models to detect different stats instead of everything in one model
    - One model for hit detection
    - One model for pass detection
    - etc.
    - This model should identify the stats for each players
    - It would be nice if it could capture the video where these were detected as well for testing
- Pose estimation will identify key points and LSTM will track movements within the key points
- Goin to have to use a LSTM model or different type of RNN to track type of action
- [https://bleedaiacademy.com/human-activity-recognition-using-tensorflow-cnn-lstm/](https://bleedaiacademy.com/human-activity-recognition-using-tensorflow-cnn-lstm/)
- [https://universe.roboflow.com/mikhail-klyukin/volleyball_dataset](https://universe.roboflow.com/mikhail-klyukin/volleyball_dataset)
- If you can use the above data set, the only challenge would be to identify which player did which action and output that to a csv - could do this instead of an LSTM
    - So player 1 had a block or player 2 had a pass
- Learn about using person re identification to keep track of individual players