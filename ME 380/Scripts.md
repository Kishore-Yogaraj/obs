detect.py
- Bounding boxes and angles
calibrate_homography.py
- point click calibration 
- Pick a point and remember the order
- Input real world coordinates in order
`python calibrate_homography.py --image <path> --points <#> --output homography.json`

1. Capture a frame and run object detection  (scan phase)
2. Build an ordered pick list from the detections
3. For each object:
       a. Solve IK for the pick position
       b. Move the robot to the pick position
       c. Activate the gripper  (close)
       d. Solve IK for the drop-off position matching the object colour
       e. Move the robot to the drop-off position
       f. Release the gripper  (open)
4. When the pick list is empty, re-scan automatically
5. Press Q or ESC in the preview window to shut down cleanly

`python vision/calibrate_homography.py --image vision/calib_images_benchy/calib_000.jpg --points 5 --output homography.json`

`python scripts/vision/detector.py --z-obj 20`

**Test**
- Calibrate and test
- Test parallax correction
- Test rotation servo

7mm y 
7mm x


### Message Flow Between Computer and ESP 32
- We run pick.py 
- Python opens a port and waits 2 seconds. It doesn't actually wait for a return message from the esp 32 it just waits 2 seconds 
- After 2 seconds the python script will run and countdown then it will run the detector 
- After it runs the detector it will capture the coordinates and angles and then calculate the needed motor angles for each object in order from closest to furthest and then it will store the motor angles in a list in this order and then immediately execute the first move
- It will then move to an object and wait 3 seconds. (This part confuses me because does it wait 3 seconds, 4 seconds or 7 seconds?) I know that the firmware does not send a "DONE" command back but we have a dwell delay and a move drain timeout so if you could clarify that it would be great 
- After whatever the timer is set runs out the next motor angles are sent to firmware and it executes the next move and waits whatever the timer is again. After executing all moves the program ends.

drain timeout (4.0s) then dwell timer (3.0s)

during drain timeout, the video feed does not display. The `wait_for_done()` only reads from the serial output and blocks the camera feed

### Sequencing
I want to take the sequencing procedures step by step to avoid making too many changes all at once. First, I want to see how I can send the motor command then wait for a "DONE" signal because I'm going to be sending a lot of back and forth. This can then be reused for all the other operations.

Goal right now:
- Send motor command
- Wait for encoders to sit within a range
- Send "DONE" from ESP32
- Wait 3 seconds
- Then send second motor command

```python
import cv2
import numpy as np
import json
import argparse
import os
import sys
from pathlib import Path

DEFAULT_CONFIG = {
    "red": {
        "low1":  [0,   100, 60],   # lower red hue band
        "high1": [10,  255, 255],
        "low2":  [165, 100, 60],   # upper red hue band (wraps at 180)
        "high2": [180, 255, 255],
    },
    "blue": {
        "low":  [100, 80,  60],
        "high": [130, 255, 255],
    },
}

CONFIG_PATH = Path("hsv_config.json")

#Contour filtering parameters 
MIN_AREA    = 200    
MAX_AREA    = 80000  
MIN_SOLIDITY = 0.25  

# Morphological kernel
MORPH_KERNEL = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))

# Trackbar helpers

def nothing(_):
    pass


def make_trackbars(win: str, color: str, config: dict):
    """Create trackbars in `win` for the given color's HSV ranges."""
    try:
        cv2.destroyWindow(win)
    except cv2.error:
        pass
    cv2.namedWindow(win, cv2.WINDOW_NORMAL)
    cv2.resizeWindow(win, 500, 300 if color == "red" else 200)

    if color == "red":
        r = config["red"]
        cv2.createTrackbar("H low1",  win, r["low1"][0],  180, nothing)
        cv2.createTrackbar("H high1", win, r["high1"][0], 180, nothing)
        cv2.createTrackbar("H low2",  win, r["low2"][0],  180, nothing)
        cv2.createTrackbar("H high2", win, r["high2"][0], 180, nothing)
        cv2.createTrackbar("S min",   win, r["low1"][1],  255, nothing)
        cv2.createTrackbar("V min",   win, r["low1"][2],  255, nothing)
    else:
        b = config["blue"]
        cv2.createTrackbar("H low",  win, b["low"][0],  180, nothing)
        cv2.createTrackbar("H high", win, b["high"][0], 180, nothing)
        cv2.createTrackbar("S min",  win, b["low"][1],  255, nothing)
        cv2.createTrackbar("V min",  win, b["low"][2],  255, nothing)


def read_trackbars(win: str, color: str) -> dict:
    """Read current trackbar values and return updated config slice."""
    g = lambda n: cv2.getTrackbarPos(n, win)
    if color == "red":
        s_min = g("S min")
        v_min = g("V min")
        return {
            "low1":  [g("H low1"),  s_min, v_min],
            "high1": [g("H high1"), 255,   255],
            "low2":  [g("H low2"),  s_min, v_min],
            "high2": [g("H high2"), 255,   255],
        }
    else:
        s_min = g("S min")
        v_min = g("V min")
        return {
            "low":  [g("H low"),  s_min, v_min],
            "high": [g("H high"), 255,   255],
        }

# Mask builders

def build_red_mask(hsv: np.ndarray, cfg: dict) -> np.ndarray:
    m1 = cv2.inRange(hsv, np.array(cfg["low1"]), np.array(cfg["high1"]))
    m2 = cv2.inRange(hsv, np.array(cfg["low2"]), np.array(cfg["high2"]))
    mask = cv2.bitwise_or(m1, m2)
    return cv2.morphologyEx(mask, cv2.MORPH_OPEN, MORPH_KERNEL)


def build_blue_mask(hsv: np.ndarray, cfg: dict) -> np.ndarray:
    mask = cv2.inRange(hsv, np.array(cfg["low"]), np.array(cfg["high"]))
    mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN, MORPH_KERNEL)
    close_kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (15, 15))
    mask = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, close_kernel)
    return mask

# Contour analysis

def filter_contours(contours):
    """Return contours that pass area and solidity thresholds."""
    valid = []
    for c in contours:
        area = cv2.contourArea(c)
        if not (MIN_AREA < area < MAX_AREA):
            continue
        hull_area = cv2.contourArea(cv2.convexHull(c))
        if hull_area == 0:
            continue
        if area / hull_area >= MIN_SOLIDITY:
            valid.append(c)
    return valid


def get_centroid(c) -> tuple[int, int]:
    M = cv2.moments(c)
    if M["m00"] == 0:
        return (0, 0)
    return (int(M["m10"] / M["m00"]), int(M["m01"] / M["m00"]))


def draw_detections(frame: np.ndarray, mask: np.ndarray, color_bgr: tuple, label: str):
    """Find contours in mask, draw overlays on frame, print detection info."""
    contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    valid = filter_contours(contours)

    for i, c in enumerate(valid):
        cx, cy   = get_centroid(c)
        rect     = cv2.minAreaRect(c)
        angle    = rect[2]          # degrees, OpenCV convention
        box_pts  = cv2.boxPoints(rect).astype(np.int32)

        # Draw rotated bounding box and centroid
        cv2.drawContours(frame, [box_pts], 0, color_bgr, 2)
        cv2.circle(frame, (cx, cy), 5, color_bgr, -1)

        # Orientation line through centroid
        length = 40
        rad = np.deg2rad(angle)
        x2 = int(cx + length * np.cos(rad))
        y2 = int(cy + length * np.sin(rad))
        cv2.line(frame, (cx, cy), (x2, y2), (255, 255, 255), 2)

        # Label
        tag = f"{label} #{i+1}  ({cx},{cy})  {angle:.1f}deg"
        cv2.putText(frame, tag, (cx + 8, cy - 8),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.45, color_bgr, 1, cv2.LINE_AA)

    return len(valid)


# Config persistence

def save_config(config: dict):
    with open(CONFIG_PATH, "w") as f:
        json.dump(config, f, indent=2)
    print(f"[HSV Tuner] Config saved to {CONFIG_PATH}")


def load_config() -> dict:
    if CONFIG_PATH.exists():
        with open(CONFIG_PATH) as f:
            cfg = json.load(f)
        print(f"[HSV Tuner] Config loaded from {CONFIG_PATH}")
        return cfg
    print("[HSV Tuner] No saved config found, using defaults.")
    return DEFAULT_CONFIG.copy()

 
# Camera intrinsics (optional undistortion)
def load_intrinsics():
    cam_path  = Path("intrinsics/camera_matrix.npy")
    dist_path = Path("intrinsics/dist_coeffs.npy")
    if cam_path.exists() and dist_path.exists():
        K    = np.load(str(cam_path))
        dist = np.load(str(dist_path))
        print("[HSV Tuner] Camera intrinsics loaded — undistortion active.")
        return K, dist
    print("[HSV Tuner] No intrinsics found — skipping undistortion.")
    return None, None


# Main loop
def main():
    parser = argparse.ArgumentParser(description="HSV Mask Tuner for Benchy Detection")
    parser.add_argument("--source", type=str, default=None,
                        help="Image or video path. Omit to use webcam.")
    parser.add_argument("--camera", type=int, default=0,
                        help="Webcam device index (default 0).")
    args = parser.parse_args()

    is_image = False
    if args.source and Path(args.source).suffix.lower() in (".jpg", ".jpeg", ".png", ".bmp"):
        static_frame = cv2.imread(args.source)
        if static_frame is None:
            sys.exit(f"[HSV Tuner] Cannot read image: {args.source}")
        is_image = True
        cap = None
    else:
        src = args.source if args.source else args.camera
        cap = cv2.VideoCapture(src)
        if not cap.isOpened():
            sys.exit(f"[HSV Tuner] Cannot open source: {src}")

    # Intrinsics 
    K, dist = load_intrinsics()

    # State
    config       = load_config()
    active_color = "red"   # TAB toggles between red / blue
    paused       = False
    frame        = None

    TUNER_WIN  = "[ Tuner ]"
    ORIG_WIN   = "[ Original ]"
    MASK_WIN   = "[ Mask ]"

    cv2.namedWindow(ORIG_WIN, cv2.WINDOW_NORMAL)
    cv2.namedWindow(MASK_WIN, cv2.WINDOW_NORMAL)
    make_trackbars(TUNER_WIN, active_color, config)

    print("\n[HSV Tuner] Ready.")
    print("  TAB   — toggle Red / Blue")
    print("  S     — save config")
    print("  L     — load config")
    print("  SPACE — pause/resume")
    print("  Q/ESC — quit\n")

    while True:
        # Grab frame
        if is_image:
            frame = static_frame.copy()
        elif not paused:
            ret, frame = cap.read()
            if not ret:
                print("[HSV Tuner] End of source.")
                break

        if frame is None:
            continue

        # Undistort
        if K is not None:
            frame = cv2.undistort(frame, K, dist)

        # Read trackbars and update config
        config[active_color] = read_trackbars(TUNER_WIN, active_color)

        # Build masks
        hsv        = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
        red_mask   = build_red_mask(hsv,  config["red"])
        blue_mask  = build_blue_mask(hsv, config["blue"])

        display    = frame.copy()
        n_red      = draw_detections(display, red_mask,  (40,  40,  220), "Red")
        n_blue     = draw_detections(display, blue_mask, (200, 100, 20),  "Blue")

        active_mask = red_mask if active_color == "red" else blue_mask

        hud = f"Tuning: {active_color.upper()}  |  Red: {n_red}  Blue: {n_blue}  |  TAB=switch  S=save  Q=quit"
        cv2.putText(display, hud, (10, 22),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.5, (220, 220, 220), 1, cv2.LINE_AA)

        cv2.imshow(ORIG_WIN, display)
        cv2.imshow(MASK_WIN, active_mask)

        # Key handling
        key = cv2.waitKey(1 if not is_image else 30) & 0xFF

        if key in (ord('q'), 27):           # Q or ESC
            break
        elif key == 9:                      # TAB — toggle color
            active_color = "blue" if active_color == "red" else "red"
            make_trackbars(TUNER_WIN, active_color, config)
            print(f"[HSV Tuner] Switched to: {active_color.upper()}")
        elif key == ord('s'):               # S — save
            save_config(config)
        elif key == ord('l'):               # L — load
            config = load_config()
            make_trackbars(TUNER_WIN, active_color, config)
        elif key == ord(' '):               # SPACE — pause
            paused = not paused
            print(f"[HSV Tuner] {'Paused' if paused else 'Resumed'}")

    if cap:
        cap.release()
    cv2.destroyAllWindows()

    print("\n[HSV Tuner] Final config:")
    print(json.dumps(config, indent=2))


if __name__ == "__main__":
    main()
```

### Test Cases
- 45 degree distal link

**Object angle 30**
30 + 90 = 120
120 - 45 = 75
75 inside the range
75 + 90 = 165

**Object Angle 90**
90 + 90 = 180
180 - 45 = 135
Wrap
- 135 - 180 = -45
-45 + 90 = 45

**Object Angle 120**
120 + 90 = 210
210 - 45 = 165
165 - 180 = -15
-15 + 90 = 75

90 is parallel with the distal
0 is perpendicular to distal



I want to integrate a new flow into my directory. Currently when we run @scripts/pick.py we wait 3 seconds then grab our detections. Right now, we place our objects strategically around our robot arm so the arms don't block the detections. We have a stepper motor attached to our base which rotates the entire base of our arms so that we effectively bring the arms out of the way which you can see the integration for in @src/stepper/StepperMotor.cpp and @src/stepper/StepperMotor.h . When we start the @scripts/pick.py script we are going to assume that the arms are out of the way and the stepper is 180 degrees away from the pick up location of our objects. Then we are going to wait 3 seconds like we usually do and capture our detections. After capturing our detections, we need to let the esp32 know that we can rotate the stepper back so we rotate the stepper back and let the PC know we are know back in position. We wait 3 seconds and then we continue on with our normal operation of moving our arms to each detected object and rotating the gripper and so on. The only thing we are adding is this begining step where the arms begin 180 degrees away from the pick up location, detections happen, and then we move the stepper back. Can you create a plan for this integration before we this feature in please









## 1. Entry Point — lines 418–454

When you run the script, Python hits `if __name__ == "__main__":` at **line 418**.

- **Lines 419–428** — `argparse` parses `--port`, `--no-preview`, `--sim` flags
- **Lines 430–433** — Any flag overrides are written into `CONFIG`
- **Lines 437–449** — Serial connection is attempted:
    - If `--sim`: `comms` stays `None`, no serial
    - Otherwise: a `SerialComms` object is created (**line 441**), `.connect()` is called (**line 442**). If that fails, falls back to `comms = None` (**line 447**)
- **Line 452** — calls `run(CONFIG, comms)`, which is where everything actually happens

---

## 2. `run()` Setup — lines 221–244

Before the loop starts:

- **Lines 223–226** — Loads vision assets: homography matrix `H`, HSV config, camera intrinsics
- **Lines 229–232** — Opens the webcam. If it fails, exits immediately
- **Lines 234–235** — Opens the OpenCV preview window (if enabled)
- **Line 237** — Sets initial state: `state = State.STARTUP`
- **Lines 238–242** — Initializes variables: empty `visit_list`, `current = None`, etc.

---

## 3. The Main Loop — line 247

`while True:` spins forever. Each iteration:

**Lines 250–260** — Checks for Q/ESC/H keypresses at every tick (quit or send HOME to ESP32)

Then it falls into the state machine — a chain of `if/elif` blocks. The current `state` variable determines which block runs. Only **one block runs per loop iteration**.

---

### STATE: STARTUP — lines 263–271

Runs once at the beginning.

- **Lines 264–268** — Calls `countdown(startup_delay=3.0, ...)`. This blocks for 3 seconds, showing a live camera feed with the timer overlaid. You can press Q to abort here.
- **Line 271** — Transitions: `state = State.SCAN`

---

### STATE: SCAN — lines 274–316

Runs once. This is the detection step.

- **Line 277** — `cap.read()` — grabs a single frame from the camera
- **Lines 282–283** — Undistorts the frame using the camera calibration matrix `K`
- **Line 285** — `get_detections(frame, H, vcfg)` — runs HSV color filtering, finds contours, transforms pixel positions to robot-frame mm coordinates. Returns a list of `Detection` dicts (each with `x_mm`, `y_mm`, `angle_deg`, `color`)
- **Line 286** — `order_picks(...)` — sorts detections by `pick_order` (default: nearest first)
- **Lines 291–294** — If nothing found, skips straight to `State.DONE`
- **Lines 296–299** — Logs each detected object with its position and angle
- **Lines 302–313** — Shows annotated scan frame in the preview window
- **Line 315** — `current = visit_list.pop(0)` — grabs the first object to visit, removes it from the list
- **Line 316** — Transitions: `state = State.ROTATE_BACK`

---

### STATE: ROTATE_BACK — lines 319–337 _(new)_

Runs once, right after the scan.

- **Line 322** — `comms.send_rotate()` — sends `"ROTATE\n"` over serial to the ESP32
- **Lines 325–327** — `comms.wait_for_done("DONE", timeout=30.0)` — blocks until the ESP32 replies `"DONE"` (meaning the stepper has finished its 180° return sweep) or times out
- **Lines 328–329** — In sim mode: just logs `[SIM] ROTATE command not sent.`
- **Lines 331–335** — `countdown(rotate_settle=3.0, ...)` — 3-second settle timer with live feed
- **Line 337** — Transitions: `state = State.MOVE_TO_OBJ`

---

### STATE: MOVE_TO_OBJ — lines 340–361

Runs once per object. Uses `current` (the Detection dict set in SCAN or DWELL).

- **Lines 345–351** — Calls `move_to(current["x_mm"], current["y_mm"], ...)`:
    - **Line 122** — `solve_ik(x_mm, y_mm)` — computes `theta1`, `theta2` joint angles
    - **Line 128** — `angles_to_commands(...)` — converts to signed degree commands for the motors
    - **Lines 129–134** — Computes servo angle from object orientation vs end-effector angle
    - **Line 144** — `comms.send_command(cmd1, cmd2, servo_angle)` — sends `"cmd1,cmd2,servo\n"` to ESP32
    - **Line 149** — `comms.wait_for_done("DONE", timeout=15.0)` — blocks until ESP32 finishes the move and replies `"DONE"`
- **Lines 353–361** — If IK failed: skips to next object or DONE. If succeeded: `state = State.DWELL`

---

### STATE: DWELL — lines 364–376

Runs once per object, right after a successful move.

- **Lines 366–370** — `countdown(dwell_seconds=3.0, ...)` — holds position for 3 seconds with live feed
- **Lines 372–376** — If more objects remain: `current = visit_list.pop(0)` and back to `State.MOVE_TO_OBJ`. If list is empty: `state = State.DONE`

> **MOVE_TO_OBJ → DWELL loops** for every object in `visit_list`.

---

### STATE: DONE — lines 379–403

- **Line 380** — Logs "Run complete"
- **Lines 383–402** — If preview is on: shows a live "Run complete" feed, waiting for Q/ESC to close
- **Line 403** — `break` — exits the `while True` loop unconditionally

---

## 4. Teardown — lines 408–411

In the `finally` block (always runs, even on Ctrl+C or crash):

- **Line 409** — `cap.release()` — releases the webcam
- **Line 410** — `cv2.destroyAllWindows()` — closes the preview window

Back in `__main__` (**lines 453–455**): if a serial connection is open, `.disconnect()` flushes and closes it.




Can you help me create a plan for integrating a new feature for @scripts/pick.py . The arms might be in the way of obstacles so before it begins to rotate the arms should be brought in towards the body. I should be able to set the arms in angle as a constant. After the arms are brought in, the stepper motor should rotate as it does right now. After the stepper rotates to the pick position, the arms should then move to the first detected object as it does. Currently the arms start at 90 degrees which we initialize