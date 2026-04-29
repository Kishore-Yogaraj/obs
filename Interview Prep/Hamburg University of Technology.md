### Tell me about yourself
Thank you for the opportunity to interview for this position. I’m currently a Mechanical Engineering student at the University of Waterloo with a focus on mechatronics, and I’m deeply passionate about leveraging AI and robotics to solve real-world problems, especially in industrial environments. During my time at Watonomous, I’ve had the chance to work extensively on perception systems for self-driving cars. I’ve developed and implemented 3D object tracking systems, integrated object detection models like YOLOv8, and worked with sensor fusion, which has given me a solid foundation in computer vision and robotics.

I’ve also created deep learning algorithms for behavior cloning in autonomous vehicles, experience that has sharpened my understanding of AI-based applications in automation. This background has equipped me with skills in Python, C++, and ROS2, along with hands-on experience in sensor interfacing and hardware integration, which I believe aligns well with the focus on industrial robots and AI applications in aircraft production that this role offers.

I’m particularly excited about the opportunity to work in fields like AI-based defect localization and 3D synthetic training data generation. The potential to contribute to research publications is something I find incredibly motivating, and I look forward to applying my skills in experimentation and research within the aircraft production domain

### Why do you want to work here
I’m excited about the opportunity to work at the Institute of Aircraft Production Technology because it’s a leading research institution in automation processes for aircraft production, which is an area that deeply interests me. The institute’s close collaboration with Airbus and its focus on cutting-edge AI and robotics applications make it an ideal place for me to contribute and grow as a researcher.

The chance to work on real-world industrial use cases like AI-based defect localization, object detection, and 3D synthetic data generation aligns perfectly with my experience in autonomous systems and AI. Additionally, I’m drawn to the opportunity to work with advanced robotics and sensor technologies in the institute’s lab, and the possibility to contribute to research publications is something I’m particularly excited about. I believe that working here will allow me to make a meaningful contribution while also gaining invaluable experience in a field that combines my passion for computer vision, AI, and automation in industrial environments.

### Talk about your experience with watonomous
My experience with Watonomous has been incredibly rewarding, as it has allowed me to contribute significantly to the development of autonomous vehicle systems and to grow into my current role as the **Perception Lead**. In this leadership position, I guide the team in developing and integrating various perception systems that enable the vehicle to understand and interact with its environment.

One of the major projects I led was the development of a **3D object tracking system** using the **Kalman Filter** and **Hungarian Algorithm** to track vehicles and pedestrians in real time. I also played a key role in building a **YOLOv8-based object detection node** for detecting objects such as traffic signs and cars, which was integrated into our vehicle’s perception stack. This involved working closely with our hardware team to integrate sensors, cameras, and compute systems on the vehicle.

As the perception lead, I oversee the development of algorithms for **sensor fusion**, 2D and 3D object detection, and **semantic segmentation**, making sure the team’s work is aligned with the overall goals of the autonomous vehicle. I assign tasks, conduct code reviews, and work closely with other subteams to ensure our systems integrate seamlessly with the vehicle’s control and planning modules.

In addition to the technical work, I’ve focused on mentoring new team members and ensuring smooth project management. I coordinate meetings, handle project timelines, and ensure that our perception solutions are robust and scalable. Watonomous has provided me with the opportunity to grow both technically and as a leader, strengthening my ability to deliver impactful, real-world solutions in the autonomous vehicle industry.



### Tell me about a project you are proud of that you worked on and something you learned form it
#### Behavior Cloning
One project I’m particularly proud of is my behavior cloning project for an autonomous RC car, where I used imitation learning to train a deep learning model to autonomously navigate a path. To collect data, I manually drove the car around a track while capturing images from a forward-facing camera at around 10 frames per second. Simultaneously, I recorded the steering and throttle angles, which were mapped to the images. This data collection process allowed us to create a dataset where each image was associated with its corresponding control action.

For the deep learning model, I used a **Convolutional Neural Network (CNN)** based on the **NVIDIA end-to-end architecture**. Mathematically, this model uses convolutional layers to apply learnable filters across the image, capturing spatial hierarchies of features like road edges. The extracted features are passed through fully connected layers to predict control actions. During training, the model minimizes the **mean squared error (MSE)** between predicted steering/throttle angles and the actual values, effectively learning the mapping from visual input to control commands.

To improve model robustness, I applied **data augmentation** techniques. This included flipping images horizontally, adjusting brightness, and adding random shadows. These augmentations help the model generalize by simulating different driving conditions such as turns, low lighting, or shadows on the road. **Batch processing** was also critical during training, where multiple images were processed simultaneously in batches. This not only sped up training but also allowed for better gradient updates through stochastic gradient descent.

One of the key lessons I learned from this project was the importance of data diversity and preprocessing. Initially, the model struggled with sections of the track that had less training data, which highlighted the need for balanced data across different scenarios. Additionally, I learned how to optimize a deep learning model for real-time control, which is crucial for robotics applications like autonomous driving.

#### Lane Finding
One project I’m particularly proud of is the lane-finding algorithm I developed using computer vision techniques. The goal was to create a program that could detect and highlight lane lines in an image or video, which is a fundamental part of self-driving car systems. I implemented the project using OpenCV and several key techniques such as edge detection, region masking, and the Hough Transform to detect lane lines.

The process started with reading and preprocessing the image by converting it to grayscale to reduce complexity and applying Gaussian blur to reduce noise. Afterward, I used **Canny Edge Detection** to identify edges in the image, which allowed the algorithm to detect the boundaries of lane lines. To focus only on the relevant part of the image, I defined a **Region of Interest**—a triangular section in front of the car—by applying a mask that filtered out unnecessary regions.

The **Hough Line Transform** was crucial for detecting the lane lines themselves. This method works by transforming points in the image space to a parameter space and then detecting the lines based on intersections in this space. The detected lines were then averaged to create smooth lane markings, which were overlaid onto the original image or video.

One of the key challenges I faced was ensuring that the algorithm was robust against noise, lighting changes, and varying road conditions. I learned how important it is to preprocess images effectively and how techniques like **Gaussian Blur** and **Canny Edge Detection** can significantly improve the accuracy of detecting important features. I also gained a deeper understanding of how mathematical models, like the Hough Transform, can be applied to solve real-world problems in computer vision.

This project taught me the importance of balancing simplicity with performance, and how critical preprocessing and parameter tuning can be for creating reliable AI systems. It also solidified my understanding of how computer vision can be applied in real-world autonomous systems, and it was rewarding to see the algorithm work successfully on video footage of moving cars.

#### Perceptron Algorithm
One project I’m particularly proud of is building a custom perceptron algorithm from scratch to classify data points into two categories, similar to a basic neural network. The goal was to classify points based on their features, such as glucose level and age, to predict whether an individual had diabetes. This project required me to understand the fundamentals of neural networks, particularly how weights, biases, and activation functions work together to form a predictive model.

The algorithm used a **sigmoid activation function** to calculate the probability of a data point belonging to a certain class. I started by generating a dataset of points and initializing a random decision boundary (a line), which would eventually be adjusted through **gradient descent** to minimize errors. Each point’s classification was based on a combination of input values multiplied by respective weights and a bias, which was passed through the sigmoid function to produce a probability.

I implemented **cross-entropy** as the error function to quantify how well the model classified the points. Through gradient descent, the algorithm updated the weights and bias iteratively, minimizing the error and improving the decision boundary with each iteration. Over time, the perceptron learned to classify the data points with greater accuracy.

One of the key things I learned from this project was the importance of understanding the mathematics behind machine learning algorithms. Concepts like gradient descent and cross-entropy became much clearer as I implemented them from the ground up. I also learned how small changes in the **learning rate** can significantly impact the convergence of the model, either speeding up the process or causing it to miss the optimal solution. This hands-on experience gave me a deep appreciation for the mechanics behind neural networks and machine learning algorithms, and the project solidified my understanding of how models learn from data.

### What is Gradient Descent
Gradient Descent is an optimization algorithm used to minimize the loss function in machine learning models. It iteratively adjusts the model’s parameters—such as weights and biases—in order to reduce the error between the predicted and actual values.

Mathematically, the algorithm calculates the gradient, or the derivative of the loss function, with respect to the model's parameters. This gradient points in the direction of the steepest increase in the loss function, and by updating the parameters in the opposite direction of the gradient, we move towards minimizing the loss. The size of these steps is controlled by a hyperparameter called the **learning rate**.

The update rule can be written as:

θnew=θold−α∇θL(θ)\theta_{\text{new}} = \theta_{\text{old}} - \alpha \nabla_\theta L(\theta)θnew​=θold​−α∇θ​L(θ)

where α\alphaα is the learning rate, and ∇θL(θ)\nabla_\theta L(\theta)∇θ​L(θ) is the gradient of the loss function with respect to the model's parameters. This process is repeated over many iterations until the loss function converges to a minimum.

In my behavior cloning project, for example, gradient descent was essential for updating the weights of the Convolutional Neural Network that mapped camera images to steering and throttle angles. By minimizing the **mean squared error** between the predicted and actual control values, the model learned to control the RC car autonomously.

### How did the Kalman Filter and Hungarian Algorithm work in your 3D tracking node?
In my 3D tracking node, I used the Kalman Filter and Hungarian Algorithm to track objects like cars and pedestrians from sensor data in real-time. These two algorithms work together to maintain accurate tracking, even when objects temporarily disappear from the sensor’s view or overlap.

1. **Kalman Filter**: The Kalman Filter is a recursive state estimation algorithm used to predict the future state of an object, given noisy observations. In my tracking node, each object’s state was defined by its position and velocity in 3D space. The filter has two steps:
    
    - **Prediction Step**: Based on the current state and velocity of an object, the Kalman Filter predicts the object’s next position in the following frame. This helps in situations where an object might not be detected in every frame, as we can estimate where it should be based on its previous state.
    - **Update Step**: When new measurements (like a new detection from a LiDAR or camera sensor) become available, the Kalman Filter updates its prediction by incorporating this measurement to correct the state estimate. This balancing act between prediction and correction allows for more robust tracking even with noisy sensor data.
    
    Mathematically, the filter uses a set of equations that include a **prediction step** based on the object’s current state and a **correction step** when new measurements come in, all while considering the noise in both the process and the measurements.
    
2. **Hungarian Algorithm**: The Hungarian Algorithm solves the **data association problem**, which is crucial when tracking multiple objects. In every new frame, we receive multiple detections (potential objects), and we need to associate these new detections with the existing tracked objects from the previous frame.
    
    The Hungarian Algorithm works by minimizing the cost of matching these detections with the tracked objects. The cost is typically defined as the distance between the predicted position of the tracked object (using the Kalman Filter) and the new detection. It formulates this matching as an assignment problem and finds the optimal set of matches that minimizes the overall cost. Once the new detections are associated with the correct objects, we can update the object’s state with the new measurement.
    
    In practice, the Kalman Filter helps smooth the tracking of each object, while the Hungarian Algorithm ensures the correct correspondence between detected objects across frames, even when objects temporarily disappear from view or overlap with each other.

### How does your lane finding algorithm work
**Step 1: Preprocessing the Image (Reading and Converting to Grayscale)**

- The algorithm first reads the image or video frame and processes it using OpenCV. To reduce complexity and make edge detection easier, the image is converted from its original RGB format (which has three color channels) into **grayscale**. A grayscale image has only one intensity channel, making it faster to process and suitable for detecting lane lines.

**Step 2: Noise Reduction with Gaussian Blur**

- The image is passed through a **Gaussian Blur** filter. This helps reduce noise or small variations in intensity, which can cause false edges during the edge detection step. The Gaussian Blur works by averaging the intensity of each pixel with its neighboring pixels, effectively smoothing the image.

**Step 3: Canny Edge Detection**

- Now that the image is preprocessed, we apply **Canny Edge Detection**. The Canny algorithm computes the gradient of pixel intensities to identify edges, which are regions where there's a sharp change in brightness. The Canny algorithm works by:
    - Calculating the gradient in both the x and y directions.
    - Identifying strong edges using a **high threshold** and weak edges using a **low threshold**.
    - If a pixel falls between the two thresholds, it’s only accepted as an edge if it's connected to a strong edge.
- The output of this step is a binary image where white pixels represent edges, and black pixels represent non-edge regions.

**Step 4: Defining a Region of Interest (ROI)**

- After detecting edges, we need to focus on the region of the image where lane lines are expected to appear, typically in front of the car on the road. To do this, we define a **Region of Interest (ROI)**, which is usually a triangular area that covers the bottom half of the image.
    
- We create a mask using this triangular region and apply a **bitwise AND** operation between the mask and the Canny output. This keeps only the edges within the ROI, filtering out the irrelevant edges outside this region (such as the sky or nearby objects).
    

**Step 5: Hough Line Transform**

- The next step is detecting the lane lines using the **Hough Line Transform**. The Hough Transform detects straight lines in the edge-detected image by transforming points in the image space to a parameter space (in this case, lines are represented as (ρ,θ)(\rho, \theta)(ρ,θ) in polar coordinates).
    
- The Hough Transform identifies the most voted lines that represent the lane markings. Each point on an edge in the image space corresponds to a set of potential lines that pass through it. These lines are represented as intersections in the Hough space, and the point where the most intersections occur corresponds to the best-fit line through the points in the image.
    

**Step 6: Drawing the Detected Lane Lines**

- Once the lane lines are detected by the Hough Transform, the algorithm draws these lines on a blank image using **cv2.line()**. We can then overlay this image with the original frame using **addWeighted()**, which combines the two images by adjusting the transparency of each. This step highlights the lane lines on the original image or video frame.

**Step 7: Smoothing with Slope Averaging**

- The final step is to ensure smooth lane lines by averaging the slopes of multiple detected line segments. This reduces noise and prevents erratic line detection. The detected lines are categorized into left and right lanes based on their slope (negative slopes for left lanes, positive slopes for right lanes). The algorithm then computes the average slope and intercept for each set of lines, allowing us to draw a single smooth line for each lane.

**Step 8: Applying to Video (Optional)**

- For video, the process is applied to each frame in a continuous loop. The algorithm reads each frame, applies the steps above, and displays the result in real-time. This creates a dynamic lane-finding system that can adjust to changing lane lines as the vehicle moves.
- **Key learning points**
- The algorithm relies on edge detection and region masking to isolate lane lines, and the Hough Transform is used to fit lines based on detected edges.
- Gaussian Blur reduces noise to improve edge detection, while the combination of ROI and bitwise operations ensures that only relevant parts of the image are processed.
- Averaging slopes helps make lane detection smoother and more robust.


**Questions**
- What does your ideal intern look like? What are some traits and characteristics you value in your interns and what do you expect of them on the job?
- I talked to shubh agarawal and eidan erlich who were some of your previous co ops. They both said they had an amazing experience and I heard that eidan was able to publish two papers on his research. Do you think the co ops for the winter term would get the opportunity to work on research papers as well?
- How committed is the team to mentorship? Does the co op emphasize more on how much you put in is how much you get out of it? Or are supervisors usually available to keep co ops accountable?

**Things I am interested in**
- 3D AI Perception
- Robotic AI for bin picking and action recognition
- Machine vision
- Synthetic data
- **Automated marketplace**
	- Where are you at with the automated marketplace?
	- How far have you gotten and what has gotten done so far?
	- Reinforcement learning for trajectory planning