### Method 1: Traditional Color Thresholding and Contour Detection
This would probably be the most efficient method for a project with distinct colours like red and blue. It requires the least computational power
#### How it works:
**Colour Space Conversion:** Convert the camera feed from RGB to HSV (Hus, Saturation, Value.) HSV is much better for color detection because it separates "color" from "brightness
**Masking:** Creates two masks - one for red and one for blue
**Contour Detection:** We can use OpenCV's `findContours` method to find the shapes of the benchies within those masks
**Centroid Calculation:** Calculate the image moments of the contour to find the (x, y) center of the Benchy in pixels

**Pros:** Runs extremely fast
**Cons:** Sensitive to lighting changes. If a shadow falls on a benchy, the red might look brow to the computer

### Method 3: Deep Learning (YOLOv8)

If we want a professional-grade solution that won't be fooled by glare or background noise, use a lightweight Object Detection model like **YOLOv8-nano**.
#### How it works:
**Dataset:** Take 50–100 photos of your Benchies in various positions/lighting
**Labeling:** Use a tool like CVAT or Roboflow to draw boxes around "Red Benchy" and "Blue Benchy."
**Inference:** The model provides a bounding box and a class label (Red/Blue) with high confidence.

**Pros:** Incredible accuracy; handles overlapping Benchies or "messy" tables very well.
**Cons:** Requires a more powerful processor (like a Jetson Nano or a PC) and time to train the model.

### Handling Lighting and Shadows
Traditional color detection (even in HSV space) relies on specific pixel value ranges. If your room has a yellow-tinted light or a dark shadow cast by the SCARA arm, the "red" pixels will shift outside your predefined range, and the robot will suddenly become "blind" to that object.

- **Deep Learning Advantage:** A model like YOLO doesn't just look at color; it looks at **features** (edges, curves, the chimney, the hull). It learns that a Benchy is a Benchy regardless of whether it's under a bright spotlight or in a dim corner.
- **Data Augmentation:** During training, you can artificially change the brightness and contrast of your photos. This "teaches" the model to recognize the red Benchy even if the lighting changes during your actual presentation.

### Why Operational Capture is the Best Strategy
- **Perspective Consistency:** Overhead cameras often have slight "fisheye" distortion or perspective warping. Training with these exact pixels means the AI learns the Benchy's shape as seen through that specific lens.
- **Lighting & Shadows:** The SCARA's arms will cast shadows as they move. By taking photos in your actual workspace, you can capture these "noise" elements (like the robot arm itself) and teach the model to ignore them.
- **Background Accuracy:** The texture of your table or the markings in your "drop-off zones" will be part of the training data, preventing the AI from getting confused by background patterns.

#### Negative Sample Trick
Include a few photos of the **empty workspace** or the workspace with **only the robot arm** moving around. Label these as having "zero" Benchies. This prevents the robot from "hallucinating" a Benchy on a table scratch or a bolt on the robot arm.

### Calibration Pipeline
We need to find the relationship between 4 known points on the table which are our world coordinates and their corresponding locations in the video feed (pixel coordinates).

#### Steps for calibration
**Mark 4 points:** Place 4 distinct markers on the table. We should make sure it forms a rectangle and it should cover the pick up and drop off zones
**Get Robot Coordinates:** Measure what these coordinates actually are in the real world using mm measurement (x,y).
**Get Pixel Coordinates:** Look at the camera feed and record the (x,y) pixel coordinates of the center of the same stickers
**Compute the homography matrix:** We can then use OpenCV's `cv2.findHomography()` function. This creates a 3 x 3 matrix that will mathematically map any pixel point to a physical robot coordinates