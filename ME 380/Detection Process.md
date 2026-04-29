```python
MIN_AREA     = 200
MAX_AREA     = 80000
MIN_SOLIDITY = 0.25
```

Used for contour filtering. MIN_AREA means the mased area need to meet or surpass the minimum number of pixels in order to be considered for a successful contour.

MAX_AREA is the max area that a mask can be applied to. 

MIN_SOLIDITY is used to make sure we only accept masks that are fully covered to avoid spindly objects like wires

```python
# ── Morphological kernel ──────────────────────────────────────────────────────
MORPH_KERNEL  = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
CLOSE_KERNEL  = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (15, 15))
```

The `getStructuringElement` in this code is used to basically trace a kernel (matrix of 1s and 0s) over the image in a convoluting pattern to clean up the image.

Here is an example of what the kernel might look like
![[Pasted image 20260323124834.png]]

The kernel then makes a decision between **dilation and erosion** over each step
- Erosion (noise removal) - If any pixel under the kernel is black, the center pixel becomes black. This shrinks the object which causes the tiny noise specks to vanish
- Dilation (Gap filling) - if any pixel under the kernel is white, the center pixel becomes white. This expands objects and causes nearby edges to grow until they touch and close a gap

```python
def build_red_mask(hsv: np.ndarray, cfg: dict) -> np.ndarray:
    r  = cfg["red"]
    m1 = cv2.inRange(hsv, np.array(r["low1"]), np.array(r["high1"]))
    m2 = cv2.inRange(hsv, np.array(r["low2"]), np.array(r["high2"]))
    mask = cv2.bitwise_or(m1, m2)
    return cv2.morphologyEx(mask, cv2.MORPH_OPEN, MORPH_KERNEL)


def build_blue_mask(hsv: np.ndarray, cfg: dict) -> np.ndarray:
    b    = cfg["blue"]
    mask = cv2.inRange(hsv, np.array(b["low"]), np.array(b["high"]))
    mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN,  MORPH_KERNEL)
    mask = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, CLOSE_KERNEL)
    return mask
```

These are the functions responsible for creating the mask. We detect which pixels fall within the hue range that was set and set that pixel to either 255 (pixel belongs to colour target), 0 (pixel does not belong).

The masks are later used for
- Contour detection
- Bounding boxes
- Orientation estimation
- Object filtering
![[Pasted image 20260323125940.png]]

![[Pasted image 20260323125950.png]]

```python
return cv2.morphologyEx(mask, cv2.MORPH_OPEN, MORPH_KERNEL)
```

This is the clean up step for the mask. It performs erosion and dilation to:
- Remove small specks
- Isolated pixels
- Tiny false detections

Keeps:
- Real objects that may have some gaps in between and fills it in

```python
 mask = cv2.inRange(hsv, np.array(b["low"]), np.array(b["high"]))
```

This is the function responsible for actually creating the mask based on the colour. The function will look at every pixel in the image and if the color falls within the hue boundaries is will set that pixel to 255 (white), if it doesn't, it sets it to 0 (black).

The resulting array:
![[Pasted image 20260323130521.png]]

```python
def draw_detections(frame: np.ndarray, mask: np.ndarray,
                    H: np.ndarray, color_bgr: tuple, label: str):
    """
    Find contours in mask, project centroids to world frame, and annotate.
    """
    contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    valid = filter_contours(contours)

    for i, c in enumerate(valid):
        cx, cy = get_centroid(c)

        # ── Rotated bounding box ───────────────────────────────────────────
        rect    = cv2.minAreaRect(c)
        angle   = rect[2]          # degrees, OpenCV convention
        box_pts = cv2.boxPoints(rect).astype(np.int32)

        cv2.drawContours(frame, [box_pts], 0, color_bgr, 2, cv2.LINE_AA)
        cv2.circle(frame, (cx, cy), 5, color_bgr, -1, cv2.LINE_AA)

        # ── Orientation line ───────────────────────────────────────────────
        length = 35
        rad    = np.deg2rad(angle)
        x2     = int(cx + length * np.cos(rad))
        y2     = int(cy + length * np.sin(rad))
        cv2.line(frame, (cx, cy), (x2, y2), (255, 255, 255), 2, cv2.LINE_AA)

        # ── Pixel → world ──────────────────────────────────────────────────
        X_mm, Y_mm = pixel_to_world(H, cx, cy)

        # ── Annotation text ────────────────────────────────────────────────
        tag = f"{label} #{i+1}  ({X_mm:+.1f}, {Y_mm:+.1f}) mm  {angle:.1f}\xb0"
        tx, ty = cx + 10, cy - 10

        # Shadow pass for legibility
        cv2.putText(frame, tag, (tx, ty), FONT, 0.44, (0, 0, 0),      2, cv2.LINE_AA)
        cv2.putText(frame, tag, (tx, ty), FONT, 0.44, color_bgr,       1, cv2.LINE_AA)

    return len(valid)
```

This is the function that actually draws the bounding boxes around our contours:
- It looks at a binary mask
- Finds object contours in that mask
- Filters out bad contours
- For each valid object:
	- Find its center
	- Draws a rotated bounding box
	- Estimates orientation
	- Converts pixel position to world coordinates
	- Writes text on the frame

```python
def draw_detections(frame: np.ndarray, mask: np.ndarray,
                    H: np.ndarray, color_bgr: tuple, label: str):
```

frame: np.ndarray - camera frame
mask: np.ndarray - binary mask for one color
H: np.ndarray - homography matrix
color_bgr: tuple - drawing color used for specific object type
label: str - text prefix for the object

```python
contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
```

This is the function that handles actually finding the contours of each mask for the object. The function itself find the contour boundaries from the mask

mask
- Binary image to search

cv2.RETY_EXTERNAL
- Only return the outermost contours and ignore counter/holes

cv2.CHAIN_APPROX_SIMPLE
- This compresses the contour representation. Instead of storing every single pixel, it stores enough points to describe the shape which saves memory

```python
valid = filter_contours(contours)
```

This is used to filter the contours itself to make sure they're not too small or too big:
- removes tiny noise blobs
- false detections
- weird thin shapes
- unstable junk contours
"valid" effectively becomes the list of contours we actually trust

```python
for i, c in enumerate(valid):
        cx, cy = get_centroid(c)

        # ── Rotated bounding box ───────────────────────────────────────────
        rect    = cv2.minAreaRect(c)
        angle   = rect[2]          # degrees, OpenCV convention
        box_pts = cv2.boxPoints(rect).astype(np.int32)

        cv2.drawContours(frame, [box_pts], 0, color_bgr, 2, cv2.LINE_AA)
        cv2.circle(frame, (cx, cy), 5, color_bgr, -1, cv2.LINE_AA)

        # ── Orientation line ───────────────────────────────────────────────
        length = 35
        rad    = np.deg2rad(angle)
        x2     = int(cx + length * np.cos(rad))
        y2     = int(cy + length * np.sin(rad))
        cv2.line(frame, (cx, cy), (x2, y2), (255, 255, 255), 2, cv2.LINE_AA)

        # ── Pixel → world ──────────────────────────────────────────────────
        X_mm, Y_mm = pixel_to_world(H, cx, cy)

        # ── Annotation text ────────────────────────────────────────────────
        tag = f"{label} #{i+1}  ({X_mm:+.1f}, {Y_mm:+.1f}) mm  {angle:.1f}\xb0"
        tx, ty = cx + 10, cy - 10

        # Shadow pass for legibility
        cv2.putText(frame, tag, (tx, ty), FONT, 0.44, (0, 0, 0),      2, cv2.LINE_AA)
        cv2.putText(frame, tag, (tx, ty), FONT, 0.44, color_bgr,       1, cv2.LINE_AA)
```

We then go through each valid contour that was found and:
- Find the centroid
- Find the smallest rotated rectangle that can contain the contour and gives:
	- center of rectangle
	- width and height
	- angle of rotation
- We then extract the angle specifically in OpenCV convention
- Find the 4 corners of the box
- Draw the rectangle on to the contours
- Draw the centroid on the box
- Prepare the orientation line
- Convert coordinates from pixel to world coordinates
- Display the coordinates and text
	- `tx, ty = cx + 10, cy - 10`
	- Tells you where to place the text
- Function returns the count of how many valid detections were found
- **Overall flow**
	- Take the detected blobs in the mask, keep only the good ones, figure out where each object is, how it is rotated and draw that information on the camera image, and report how many objects were found

```python
def main():
    parser = argparse.ArgumentParser(description="Benchy Detector with World-Frame Coordinates")
    parser.add_argument("--source",     type=str,   default=None,
                        help="Image or video path. Omit to use webcam.")
    parser.add_argument("--camera",     type=int,   default=0,
                        help="Webcam device index (default 0).")
    parser.add_argument("--homography", type=str,   default="homography.json",
                        help="Path to homography JSON (default: homography.json).")
    parser.add_argument("--hsv",        type=str,   default="hsv_config.json",
                        help="Path to HSV config JSON (default: hsv_config.json).")
    args = parser.parse_args()

    # ── Load calibration data ──────────────────────────────────────────────
    H     = load_homography(Path(args.homography))
    H_inv = np.linalg.inv(H)
    cfg   = load_hsv_config(Path(args.hsv))
    K, dist = load_intrinsics()

    # ── Source setup ───────────────────────────────────────────────────────
    is_image = False
    cap      = None

    if args.source:
        ext = Path(args.source).suffix.lower()
        if ext in (".jpg", ".jpeg", ".png", ".bmp"):
            static_frame = cv2.imread(args.source)
            if static_frame is None:
                sys.exit(f"[Detector] Cannot read image: {args.source}")
            is_image = True
        else:
            cap = cv2.VideoCapture(args.source)
    else:
        cap = cv2.VideoCapture(args.camera)

    if not is_image and (cap is None or not cap.isOpened()):
        sys.exit(f"[Detector] Cannot open video source.")

    WIN = "[ Benchy Detector ]"
    cv2.namedWindow(WIN, cv2.WINDOW_NORMAL)

    print("\n[Detector] Running.  Q / ESC to quit.\n")

    while True:
        # ── Grab frame ─────────────────────────────────────────────────────
        if is_image:
            frame = static_frame.copy()
        else:
            ret, frame = cap.read()
            if not ret:
                print("[Detector] End of source.")
                break

        # ── Undistort ──────────────────────────────────────────────────────
        if K is not None:
            frame = cv2.undistort(frame, K, dist)

        # ── Build masks ────────────────────────────────────────────────────
        hsv       = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
        red_mask  = build_red_mask(hsv,  cfg)
        blue_mask = build_blue_mask(hsv, cfg)

        display = frame.copy()

        # ── Origin crosshair ───────────────────────────────────────────────
        draw_origin_crosshair(display, H_inv)

        # ── Detections ─────────────────────────────────────────────────────
        n_red  = draw_detections(display, red_mask,  H, (40,  40,  220), "Red")
        n_blue = draw_detections(display, blue_mask, H, (200, 100,  20), "Blue")

        # ── Minimal HUD ────────────────────────────────────────────────────
        hud = f"Red: {n_red}  Blue: {n_blue}  |  Q = quit"
        cv2.putText(display, hud, (8, 20), FONT, 0.48, (0,   0,   0),   2, cv2.LINE_AA)
        cv2.putText(display, hud, (8, 20), FONT, 0.48, (220, 220, 220), 1, cv2.LINE_AA)

        cv2.imshow(WIN, display)

        key = cv2.waitKey(1 if not is_image else 30) & 0xFF
        if key in (ord('q'), 27):
            break

    if cap:
        cap.release()
    cv2.destroyAllWindows()
    print("[Detector] Done.")
```

This is the core run time loop of our detector:
- Grabs a frame
- Undistorts it
- Builds a color mask
- Finds objects
- Draws annotations
- Displays results

Everything inside this loop will execute once per frame.

```python
while True:
        # ── Grab frame ─────────────────────────────────────────────────────
        if is_image:
            frame = static_frame.copy()
        else:
            ret, frame = cap.read()
            if not ret:
                print("[Detector] End of source.")
                break

        # ── Undistort ──────────────────────────────────────────────────────
        if K is not None:
            frame = cv2.undistort(frame, K, dist)

        # ── Build masks ────────────────────────────────────────────────────
        hsv       = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
        red_mask  = build_red_mask(hsv,  cfg)
        blue_mask = build_blue_mask(hsv, cfg)

        display = frame.copy()

        # ── Origin crosshair ───────────────────────────────────────────────
        draw_origin_crosshair(display, H_inv)

        # ── Detections ─────────────────────────────────────────────────────
        n_red  = draw_detections(display, red_mask,  H, (40,  40,  220), "Red")
        n_blue = draw_detections(display, blue_mask, H, (200, 100,  20), "Blue")
```

- Grab your video frame
- Undistort your frame
- Convert from BGR colour frame to HSV colour space
- Build your red and blue colour masks
	- Threshold HSV ranges
	- Remove noise
	- Fill Gaps
	- Return binary masks (white for detected masks, black for background)
- Create display copy
	- We don't want to write on the actual frame incase we need it elsewhere
- Draw the origin on the display
- Draw detections on display
	- Finds contours inside red and blue mask
	- Filters them
	- Compute centroids
	- Computes orientation
	- Converts to world coordinates
	- Draws bounding boxes draws orientation lines
	- Writes annotation text
- `cv2.imshow(WIN, display)` - at this point we see
	- bounding boxes
	- centroids
	- orientation lines
	- world coordinates
	- object counts
	- origin crosshair

### Flow of entire system
- Camera frame
- Undistort
- HSV conversion
- Color thresholding
- Morphological cleanup
- Contour detection
- Centroid extraction
- Orientation estimation
- Pixel to world coordinate transform
- Annotation overlay
- Display output
