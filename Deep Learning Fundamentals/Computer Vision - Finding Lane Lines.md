### OpenCV
- Used to preprocess training data before it is fed into a deep neural network
- Purpose of this section is to build a program that can identify lane lines in a picture or a video
	- When we drive a car, we can see where the lane lines are using our eyes
	- A car doesn't have any eyes so we used computer vision to teach the car how to visualize lane lines
### Reading and Displaying an Image
```python
import cv2

#Read the image from the file and return it as a multidimensional
#Numpy array containign the relative intensities of each pixel in the test_image
image = cv2.imread('test_image.jpg')

#imshow() takes two arguments ('name of the image when displayed','which image to use' )
cv2.imshow('result', image)

#Displays image for a specified amount of miliseconds
cv2.waitKey(0)
```

### Canny Edge Detection

**Edge detection**
- Identifying sharp changes in intensity (color) in adjacent pixels
- Images can be read as a matrix
	- An array of pixels
	- A pixels will contain the light intensity at some location in the image
	- Each pixel's intensity is denoted by a numeric value that ranges from 0 to 255
	- 0 represents no intensity where something is completely black whereas 255 represents a maximum intensity or something being completely white


![[IMG_1573.jpg|600]]

**Gradient**
- Measure of change in brightness over adjacent pixels
- A strong gradient indicates a steep change whereas a small gradient represents a shallow change
- An edge is defined by the difference in intensity values in adjacent pixels 
	- Rapid changes in brightness
- Whenever there is a strong gradient, there is a corresponding bright pixel in the gradient image
- By tracing out all the pixels that have this strong gradient we can obtain the edges
- **What is a gradient image?**
	- An image that highlights the edges of the original image 
	- An edge in a gradient image is defines as the point where adjacent pixels have a strong gradient
	- A gradient is a change in intensity between adjacent pixels
	- The stronger the gradient the more likely it is to be an edge

![[IMG_1576.jpg]]

**Grayscale**
- **Why do we convert image to grayscale?**
	- Images are made up of pixels
	- A three channel color image would have red, green and blue channels
	- Each pixel is a combination of three intensity values
	- A grayscale image only has one channel where each pixel has one intensity ranging from 0 to 255
	- Processing single channel is faster then processing a three channel color image

![[IMG_1578.jpg#center|400]]

- **What does a pixel with three channels look like?**

> [!question] What does a pixel with three channels look like?
> - Grayscale Image: In a grayscale image, each pixel is represented by a single channel, so each pixel has just one intensity value ranging from 0 to 255. For example, a pixel value could be (0), which represents black, or (230), which represents a light gray. Since there is only one value per pixel, grayscale images are simpler and faster to process.
> - Colored Image: In a colored (RGB) image, each pixel is represented by three channels (Red, Green, Blue), with each channel having an intensity value ranging from 0 to 255. For example, a pixel could be (221, 139, 45), where:
> 	- 221 represents the intensity of red,
> 	- 139 represents the intensity of green, and
> 	- 45 represents the intensity of blue.
> - Because each pixel in a colored image requires three separate values instead of one, the data associated with a colored image is three times larger than that of a grayscale image. This means that processing a colored image typically requires more time and computational resources than processing a grayscale image.
> 

### Turing image to grayscale
```python
import cv2
import numpy as np

#Read the image from the file and return it as a multidimensional
#Numpy array containign the relative intensities of each pixel in the test_image
image = cv2.imread('test_image.jpg')
lane_image = np.copy(image) #Ensure to make a copy, otherwise changes will be reflected in the original
gray = cv2.cvtColor(lane_image, cv2.COLOR_RGB2GRAY) #Function that takes an image and converts it to another colour space

#imshow() takes two arguments ('name of the image when displayed','which image to use' )
cv2.imshow('result', gray) #Here we display the grayscale image instead of the original

#Displays image for a specified amount of miliseconds
cv2.waitKey(0)
```

**Gaussian Blur**
- Reducing noise and smoothening the image
- In order to accurately catch as many edges in the image as possible, we must filter out any image noise
	- Image noise can create false edges and affect edge detection
- Remember that an image can be represented a collection of pixels each with an assigned number from 0-255 which represents the intensity of the pixel
- **How do we smoothen an image?**
	- We can modify the value of a pixel with the average value of the pixel intensities around it
	- Averaging out the pixels in the image to reduce noise will be done with a kernel
		- A kernel with normally distributed numbers is run across the entire image and sets each pixel value equal to the weighted average of its neighboring pixels
		- **This is kernel convolution (go over it) **

```python
blur = cv2.GaussianBlur(gray, (5,5), 0) #(5,5) is the kernel and 0 is the deviation
#Applies a gaussian blur on a grayscale iamge with a five by five kernel
#Size of kernel depends on specific situations
#5 by 5 is a good size for most cases
# Ultimately what that will do is return a new image that we call a blur 
# Which reduces noise in our image
```

**Canny**
- An edge is a sharp change in intensity between adjacent pixels in the image
- The change in brightness over a series of pixels is the gradient
- Strong gradient is steep change and small gradient is shallow change
- Images are an array of intensities
- We can represent an image in 2D where x represents the number of number of columns (width) and y represents the number of rows (height)
	- Product of width and height give you the total number of pixels in your image
	- We can look at out image as not only as an array but also as a mathematical function of x and y
	- Since its a mathematical function f(x,y) we can perform mathematical operations
	- **What operator can we use to determine rapid changes in brightness in our image?**
		- The canny function will perform a derivative on our function in both X and Y directions which measures the change in intensity with respect to adjacent pixels
		- Small derivative = small change in intensity
		- Big derivative = big change in intensity
		- By computing the derivative in all directions of the image, we're computing the gradients
		- Gradient is change in brightness over a series of pixels

![[IMG_1579.jpg#center|400]]


![[IMG_1575.jpg#center|400]]

```python
cv2.Canny(image, low_threshold, high_threshold)
#Allows us to isolate the adjacent pixels that follow the strongest gradients
#Areas that are larger than the upper threshold are accepted as an edge pixel whereas areas below the lower threshold are rejected as an edge
#Areas where its completely black on the gradient image correspond to low changes in intensity between adjacent pixels. Wheras the white line represent as region in the image where there is a high change in intensity, exceeding the threshold
#Recommened to use a ration of 1:3 of lower:higher
#If a gradient is between the two thresholds then the function checks to see if the pixel is connected to strong edge meaning if any of 8 neighbouring pixels are considered a strong edge pixel
```

### Applying Canny Edge Detection
```python
import cv2
import numpy as np

#Read the image from the file and return it as a multidimensional
#Numpy array containign the relative intensities of each pixel in the test_image
image = cv2.imread('test_image.jpg')
lane_image = np.copy(image) #Ensure to make a copy, otherwise changes will be reflected in the original
gray = cv2.cvtColor(lane_image, cv2.COLOR_RGB2GRAY) #Function that takes an image and converts it to another colour space
blur = cv2.GaussianBlur(gray, (5,5), 0)
canny = cv2.Canny(blur, 50, 150) #Function to apply canny edge detection on lane lines
#imshow() takes two arguments ('name of the image when displayed','which image to use' )
cv2.imshow('result', canny) #Here we display the grayscale image instead of the original

#Displays image for a specified amount of miliseconds
cv2.waitKey(0)

```

### Region of Interest - Matplotlib
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt #Library used to display images visually with x and y axis


def canny(image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY) #Function that takes an image and converts it to another colour space
    blur = cv2.GaussianBlur(gray, (5,5), 0)
    canny = cv2.Canny(blur, 50, 150)
    return canny

#Read the image from the file and return it as a multidimensional
#Numpy array containign the relative intensities of each pixel in the test_image
image = cv2.imread('test_image.jpg')
lane_image = np.copy(image) #Ensure to make a copy, otherwise changes will be reflected in the original
canny = canny(lane_image)
#imshow() takes two arguments ('name of the image when displayed','which image to use' )
plt.imshow(canny) #Here we display the grayscale image instead of the original

#Displays image for a specified amount of miliseconds
plt.show()

```

### Region of Interest 
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt #Library used to display images visually with x and y axis


def canny(image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY) #Function that takes an image and converts it to another colour space
    blur = cv2.GaussianBlur(gray, (5,5), 0)
    canny = cv2.Canny(blur, 50, 150)
    return canny

def region_of_interest(image):
    height = image.shape[0]
    polygons = np.array([
    [(200, height), (1100, height), (550, 250)]
    ]) #Vertices of region of interest
    mask = np.zeros_like(image) #Creates an array of zeros with the same shape as the images corresponding array
    cv2.fillPoly(mask, polygons, 255) #Fills array 'mask' with the coordinates of 'triangle' with the colour white
    return mask

#Read the image from the file and return it as a multidimensional
#Numpy array containign the relative intensities of each pixel in the test_image
image = cv2.imread('test_image.jpg')
lane_image = np.copy(image) #Ensure to make a copy, otherwise changes will be reflected in the original
canny = canny(lane_image)
#imshow() takes two arguments ('name of the image when displayed','which image to use' )
cv2.imshow("result", region_of_interest(canny)) #Here we display the grayscale image instead of the original

#Displays image for a specified amount of miliseconds
cv2.waitKey(0)

```

### Binary numbers and Bitwise_and
- In binary 23 = 10111
	- Imagine 8 placeholders, eight boxes as we're dealing with a base, two numeral system
	- Each box represents a power of 2
	- Each numerical place will correspond to an increasing power of two
	- Each box can only except 0 or 1, how do we put 23 in binary
	- Start with the highest value and ask ourselves if the number is in 23?
		- If not we leave it as 0
	- Is 16 in 23?
		- It is so we do 23-16 and are left with 7
		- Now we have 1 here
	- Does 8 go into 7? - No
		- We have 0 here
	- Does 4 go into 7?
		- Yes so we do 7-4 and get 3
		- We have a 1 here
	- Following this pattern we get 10111

**Why is this important?**
- If we represent our as an array of pixel intensities and we represent each of the values of the pixel intensities as binary values we can see that the black region has a binary value of 000 and the white region has a binary value of 11111111
- We're going to apply this mask onto our canny image to only show the region of interest which is the region traced by the polygon
- We do this by using the Bitwise_and operation between the two images
- This operation occurs element wise between the two images between the two arrays of pixels
- Both images have the same array shape and shape dimension and same amount of pixels
- This is what the bitwise end function essentially does
	- Takes the binary value from the mask image which will either be 0000 or 11111111 and performs the & function with the corresponding binary value on the canny image
	- The & function will compare the two binary values so for example lets compare 0000 with 1010
	- If two values of the same index have 1 then your resultant is 1 otherwise it is 0
	- 0 and 1 give you 0 then 0 and 0 give you 0
	- The resultant of 0000 & 1010 will be 0000 
	- When you reflect this binary value on the canny image it masks it black therefore covering the area that is not of our region of interest
	- If we were to do 11111111 & 1010 we would get 10100000
	- the bitwise and of these two pixels would have 0 effect which means that it would be considered a region of interest and still be displayed

### Hough Transform
- **How are we going to detect straight lines and then identify the straight lines?**
	- The equation of a straight lines is y = mx + b with two parameters m and b
	- Given y = 3x +2, this line can be plotted as a single point in Hough Space as 3,2
	- A single point can be represented as a line in Hough space given the infinite amount of lines that can go through a single point in the graph
- **What is the line that consistently intersects two points in the graph?**
	- We can look at the point of intersection in Hough Space which represents the m and b values of a line consistent with crossing both points in graph
- **Why is this important?**
	- The idea of identifying possible lines from a series of points is how we're going to find lines in our gradient image
- **How do we identify the lines in our image?**
	- Take the 4 points in our image space that correspond to the following Hough Space
	- We split the Hough space into a grid with each bin inside of the bin corresponding to the slope and y intercept values of a candidate line
	- If we're told a set of points belong to a line what is that line?
	- We can see the points of intersection in the Hough Space 
	- The points of intersection can be found within a bin in the Hough space
	- For every point of intersection we're going to cast a vote inside of the bin that it belongs to 
	- The bin with the maximum number of votes will be the line
	- Whatever m an b value that the bin belongs is the line that we're going to draw since its the line of best fit in describing our data
- **What about vertical lines?**
	- We can express the vertical lines using polar coordinates
	- The main idea is that we still use the equation of the line but in polar coordinates
	- The variable rho is the perpendicular distance from the origin to that line and theta indicates the angle of inclination of the normal line from the x axis which is measured in radians clockwise with respect to the positive x axis
- A line can be detected by finding the number of intersections between curves
- The more curves intersecting means that the line represented by that intersection crosses more points
- Goal is to find the line that best describes our points, and that's what we're going to use to find the lines that best define the edge points in our gradient image - This is Hough Transform


![[IMG_1591.jpg#center|400]]
![[IMG_1588.jpg#center|400]]


![[IMG_1589.jpg#center|500]]

![[IMG_1592.jpg#center|400]]

### Hough Transform For Detecting Lane Lines
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt #Library used to display images visually with x and y axis


def canny(image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY) #Function that takes an image and converts it to another colour space
    #Will put a gaussian blur on the image to reduce noise and detect edges accurately
    #It will use a 5x5 kernel to do this and 0 is the deviation
    blur = cv2.GaussianBlur(gray, (5,5), 0)
    #Canny function detects the edges on the image and returns a gradient image
    #50 is the lower threshold and 150 is the upper threshold
    #This means that if the gradient(difference in intensity of 2 pixels) between two pixels is greater than 150
    #It is an edge, if lower than 50 it is not and edge if inbetween it checks to see if the surround 8 pixels are
    #A detected edge, if it is, then it also becomes an edge
    canny = cv2.Canny(blur, 50, 150)
    return canny #We are returned the gradient image with edges detected

def display_lines(image, lines):
    line_image = np.zeros_like(image)
    if lines is not None:
        for line in lines: #Access each block (there are 9 blocks of (1,4) arrays)
            x1, y1, x2, y2 = line.reshape(4) #Assing point to 1D arrays
            #line() draws a line segment connecting 2 points
            #line(image (graph), point 1, point 2, colour, thickness)
            cv2.line(line_image, (x1, y1), (x2,y2), (255, 0, 0), 10)
    return line_image #return the black image with lines drawn


def region_of_interest(image): #input is the edge detected image
    height = image.shape[0] #find the total heigh of the image (what is the column value at index row 0)
    polygons = np.array([
    [(200, height), (1100, height), (550, 250)]
    ]) #Vertices of region of interest - vertices we determined by observation using matplotlib
    mask = np.zeros_like(image) #Creates an array of zeros (all black image) with the same shape as the image's corresponding array

    #Arguments are the masked image, multiple polygons, colour to fill
    cv2.fillPoly(mask, polygons, 255) #Fills array 'mask' with the coordinates of 'triangle' with the colour white

    #Computing the bitwise & of both images takes the bitwise & of each homologous pixel in both arrays,
    #Ultimately masking the canny image to only show the region of interest traced by the polygonal contour of the mask
    #Arguments are edge detected image and the masked image
    masked_image = cv2.bitwise_and(image, mask) #Masks the region we don't want on the edge detected image
    return masked_image #Returns the edge detected image with only region of interest

#Read the image from the file and return it as a multidimensional
#Numpy array containign the relative intensities of each pixel in the test_image
image = cv2.imread('test_image.jpg')
lane_image = np.copy(image) #Ensure to make a copy, otherwise changes will be reflected in the original
canny = canny(lane_image)
cropped_image = region_of_interest(canny)

#HoughLinesP() function takes the cropped edge detected image and uses Hough Transform to determine the line of best
#Fit along a series of points
#HoughLinesP(edge detected cropped image, resolution or size of rho(bins in y), resolution or size of theta(bins in x), minimum number of intersections needed to accept a line, min length of line segment that should be detected, min space between two line segments in order to be considered invidual lines)
lines = cv2.HoughLinesP(cropped_image, 2, np.pi/180, 100, np.array([]), minLineLength=40, maxLineGap=5) #Returns an array of line segments with start and end points
# black image with line segments drawn
line_image = display_lines(lane_image, lines)
# addWeighted(original image, pixel intensity (darker), second black image with lines, pixel intensity of second image, some value that will add to each pixel ) fucntion takes the original image and adds the b
#takes each pixel intensity in both images, adds them together (intensity of second image is 0 therefore the coloured image will be displayed), display the coloured image darker and display the lines lighter
combo_image = cv2.addWeighted(lane_image, 0.8, line_image, 1, 1)
#imshow() takes two arguments ('name of the image when displayed','which image to use' )
cv2.imshow("result", combo_image) #Here we display the grayscale image instead of the original
#Displays image for a specified amount of miliseconds
cv2.waitKey(0)

```

### Optimizing
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

def make_coordinates(image, line_parameters):
    slope, intercept = line_parameters
    y1 = image.shape[0]
    y2 = int(y1*(3/5))
    x1 = int((y1 - intercept)/slope)
    x2 = int((y2 - intercept)/slope)
    return np.array([x1, y1, x2, y2])

def average_slope_intercept(image, lines):
    left_fit = []
    right_fit = []
    for line in lines:
        x1, y1, x2, y2 = line.reshape(4)
        parameters = np.polyfit((x1, x2), (y1, y2), 1)
        slope = parameters[0]
        intercept = parameters[1]
        if slope < 0:
            left_fit.append((slope, intercept))
        else:
            right_fit.append((slope, intercept))
    left_fit_average = np.average(left_fit, axis=0)
    right_fit_average = np.average(right_fit, axis=0)
    left_line = make_coordinates(image, left_fit_average)
    right_line = make_coordinates(image, right_fit_average)
    return np.array([left_line, right_line])

def canny(image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)
    blur = cv2.GaussianBlur(gray, (5,5), 0)
    canny = cv2.Canny(blur, 50, 150)
    return canny

def display_lines(image, lines):
    line_image = np.zeros_like(image)
    if lines is not None:
        for line in lines:
            x1, y1, x2, y2 = line.reshape(4)
            cv2.line(line_image, (x1, y1), (x2,y2), (255, 0, 0), 10)
    return line_image

def region_of_interest(image):
    height = image.shape[0]
    polygons = np.array([
    [(200, height), (1100, height), (550, 250)]
    ])
    mask = np.zeros_like(image)
    cv2.fillPoly(mask, polygons, 255)
    masked_image = cv2.bitwise_and(image, mask)
    return masked_image


image = cv2.imread('test_image.jpg')
lane_image = np.copy(image)
canny = canny(lane_image)
cropped_image = region_of_interest(canny)

lines = cv2.HoughLinesP(cropped_image, 2, np.pi/180, 100, np.array([]), minLineLength=40, maxLineGap=5)
averaged_lines = average_slope_intercept(lane_image, lines)
line_image = display_lines(lane_image, averaged_lines)
combo_image = cv2.addWeighted(lane_image, 0.8, line_image, 1, 1)
cv2.imshow("result", combo_image)
cv2.waitKey(0)

```

### Using the Algorithm on  Video
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

def make_coordinates(image, line_parameters):
    slope, intercept = line_parameters
    y1 = image.shape[0]
    y2 = int(y1*(3/5))
    x1 = int((y1 - intercept)/slope)
    x2 = int((y2 - intercept)/slope)
    return np.array([x1, y1, x2, y2])

def average_slope_intercept(image, lines):
    left_fit = []
    right_fit = []
    for line in lines:
        x1, y1, x2, y2 = line.reshape(4)
        parameters = np.polyfit((x1, x2), (y1, y2), 1)
        slope = parameters[0]
        intercept = parameters[1]
        if slope < 0:
            left_fit.append((slope, intercept))
        else:
            right_fit.append((slope, intercept))
    left_fit_average = np.average(left_fit, axis=0)
    right_fit_average = np.average(right_fit, axis=0)
    left_line = make_coordinates(image, left_fit_average)
    right_line = make_coordinates(image, right_fit_average)
    return np.array([left_line, right_line])

def canny(image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)
    blur = cv2.GaussianBlur(gray, (5,5), 0)
    canny = cv2.Canny(blur, 50, 150)
    return canny

def display_lines(image, lines):
    line_image = np.zeros_like(image)
    if lines is not None:
        for line in lines:
            x1, y1, x2, y2 = line.reshape(4)
            cv2.line(line_image, (x1, y1), (x2,y2), (255, 0, 0), 10)
    return line_image

def region_of_interest(image):
    height = image.shape[0]
    polygons = np.array([
    [(200, height), (1100, height), (550, 250)]
    ])
    mask = np.zeros_like(image)
    cv2.fillPoly(mask, polygons, 255)
    masked_image = cv2.bitwise_and(image, mask)
    return masked_image


# image = cv2.imread('test_image.jpg')
# lane_image = np.copy(image)
# canny = canny(lane_image)
# cropped_image = region_of_interest(canny)
# lines = cv2.HoughLinesP(cropped_image, 2, np.pi/180, 100, np.array([]), minLineLength=40, maxLineGap=5)
# averaged_lines = average_slope_intercept(lane_image, lines)
# line_image = display_lines(lane_image, averaged_lines)
# combo_image = cv2.addWeighted(lane_image, 0.8, line_image, 1, 1)
# cv2.imshow("result", combo_image)
# cv2.waitKey(0)

cap = cv2.VideoCapture("test2.mp4") #Begins playing the video
while(cap.isOpened()): #While the video is playing
    _, frame = cap.read() #Decode every video frame, first value is a boolean and second value is the current frame. Each frame is then run through the algorithm
    canny_image = canny(frame)
    cropped_image = region_of_interest(canny_image)
    lines = cv2.HoughLinesP(cropped_image, 2, np.pi/180, 100, np.array([]), minLineLength=40, maxLineGap=5)
    averaged_lines = average_slope_intercept(frame, lines)
    line_image = display_lines(frame, averaged_lines)
    combo_image = cv2.addWeighted(frame, 0.8, line_image, 1, 1)
    cv2.imshow("result", combo_image)

    #cv2.waitKey(1), 1 is the time in miliseconds between frames after being run though the algorithm
    if cv2.waitKey(1) == ord('q'): #Program ends after video ends or if q key is pressed
        break
cap.release() #Close video file
cv2.destroyAllWindows() #Destroy the window we are currently on

```