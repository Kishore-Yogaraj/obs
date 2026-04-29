One project I’m particularly proud of is the lane-finding algorithm I developed using computer vision techniques. The goal was to create a program that could detect and highlight lane lines in an image or video, which is a fundamental part of self-driving car systems. I implemented the project using OpenCV and several key techniques such as edge detection, region masking, and the Hough Transform to detect lane lines.

The process started with reading and preprocessing the image by converting it to grayscale to reduce complexity and applying Gaussian blur to reduce noise. Afterward, I used **Canny Edge Detection** to identify edges in the image, which allowed the algorithm to detect the boundaries of lane lines. To focus only on the relevant part of the image, I defined a **Region of Interest**—a triangular section in front of the car—by applying a mask that filtered out unnecessary regions.

The **Hough Line Transform** was crucial for detecting the lane lines themselves. This method works by transforming points in the image space to a parameter space and then detecting the lines based on intersections in this space. The detected lines were then averaged to create smooth lane markings, which were overlaid onto the original image or video.

One of the key challenges I faced was ensuring that the algorithm was robust against noise, lighting changes, and varying road conditions. I learned how important it is to preprocess images effectively and how techniques like **Gaussian Blur** and **Canny Edge Detection** can significantly improve the accuracy of detecting important features. I also gained a deeper understanding of how mathematical models, like the Hough Transform, can be applied to solve real-world problems in computer vision.

This project taught me the importance of balancing simplicity with performance, and how critical preprocessing and parameter tuning can be for creating reliable AI systems. It also solidified my understanding of how computer vision can be applied in real-world autonomous systems, and it was rewarding to see the algorithm work successfully on video footage of moving cars.

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

### Issues I ran into
One oddly specific issue that really frustrated me during the lane-finding project was how the algorithm struggled when the road had faded lane lines or inconsistent markings. The algorithm worked perfectly on clear, well-defined lanes, but when I tested it on roads where the lane markings were faded or even partially missing, the detected lines became erratic. In some cases, the algorithm would either not detect the lane at all or incorrectly detect random markings on the road as lane lines.

It was really annoying because, initially, everything seemed to work so well during testing with ideal conditions. But when I took it into the real world with a video of an actual road, the variability in lane quality made the system unreliable. What ticked me off most was that the algorithm was detecting lines where there were none and missing lines where they should have been.

To solve this, I had to rethink how the algorithm processed edges and made decisions. One thing I did was tune the **Canny Edge Detection** thresholds more carefully and adjust the **Region of Interest** so the algorithm was more focused on the actual driving lanes. I also added a check for the **slope of the detected lines**, ensuring that only lines with reasonable slopes for lane markings (like those angled toward the car) were accepted. This filtering step made the algorithm much more robust to poor road conditions.

This experience taught me that in real-world applications, you can’t always rely on perfect data. You have to build systems that can handle the messy, unpredictable nature of real environments, and that sometimes means a lot of fine-tuning and trial and error.