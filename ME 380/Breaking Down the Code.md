### Serial Connection
```python
# Serial connection
try:
    ser = serial.Serial(PORT, BAUDRATE, timeout=0.05)
    time.sleep(2)
    SERIAL_ENABLED = True
    print(f"Connected to ESP32 on {PORT}")
except Exception as e:
    ser = None
    SERIAL_ENABLED = False
    print(f"No serial connection: {e}")
```

The main purpose of this block is to establish a connection to the ESP 32. The **try** block attempts the connection and if anything inside the try block throws an error it jumps to the **except** block instead of crashing

**ser = serial.Serial(PORT, BAUDRATE, timeout=0.05)**
This opens the serial port. It takes three arguments

**PORT** - the come port the esp32 is on 
**BAUDRATE** - the communication speed in bits per second (115200), which must match whatever you configured on the ESP32 side
**timeout=0.05** - when reading from the port, if no data arrives within 50ms, stop waiting and return whatever was received which prevents the the program from hanging forever on a read

We then wait 2 seconds after opening the port. The ESP32 resets itself when a serial connection is established and it needs  moment to boot back up before its ready to receive commands

Then we set the flag that the serial connection was established. If we got an error then we set the flag to FALSE and the rest of the code knows not to run or try to send any data over the port.

### def_calculate workspace function
```python
def calculate_workspace(L1, L2, motor_dist):
    L0 = motor_dist / 2.0

    D1 = np.sqrt((X + L0)**2 + Y**2)
    D2 = np.sqrt((X - L0)**2 + Y**2)

    reach1 = (D1 <= (L1 + L2)) & (D1 >= abs(L1 - L2))
    reach2 = (D2 <= (L1 + L2)) & (D2 >= abs(L1 - L2))
    valid_reach = reach1 & reach2

    with np.errstate(invalid='ignore', divide='ignore'):
        alpha1 = np.arctan2(Y, X + L0)
        alpha2 = np.arctan2(Y, X - L0)

        beta1 = np.arccos(np.clip((L1**2 + D1**2 - L2**2) / (2 * L1 * D1), -1.0, 1.0))
        beta2 = np.arccos(np.clip((L1**2 + D2**2 - L2**2) / (2 * L1 * D2), -1.0, 1.0))

        theta1 = alpha1 + beta1
        theta2 = alpha2 - beta2

        E1x = -L0 + L1 * np.cos(theta1)
        E2x =  L0 + L1 * np.cos(theta2)

        elbows_out = (E1x < X) & (E2x > X)

        cos_gamma_max = np.cos(np.radians(comfort_angle_deg))
        cos_g1 = (L1**2 + L2**2 - D1**2) / (2 * L1 * L2)
        cos_g2 = (L1**2 + L2**2 - D2**2) / (2 * L1 * L2)
        comfort1 = (np.abs(cos_g1) <= cos_gamma_max)
        comfort2 = (np.abs(cos_g2) <= cos_gamma_max)

        mask = valid_reach & elbows_out & comfort1 & comfort2

    return mask

```

**`L0 = motor_dist / 2.0`** The two base motors are symmetric about the origin, so `L0` is the distance from the center to each motor. Left motor sits at `(-L0, 0)`, right at `(+L0, 0)`.

**`D1 = np.sqrt((X + L0)**2 + Y**2)`** **`D2 = np.sqrt((X - L0)**2 + Y**2)`** For every point on the grid, this computes the straight-line distance from that point to the left motor (`D1`) and right motor (`D2`). Just Pythagoras. These are full 2D arrays, one value per grid point.

**`reach1 = (D1 <= (L1 + L2)) & (D1 >= abs(L1 - L2))`** **`reach2 = (D2 <= (L1 + L2)) & (D2 >= abs(L1 - L2))`** **`valid_reach = reach1 & reach2`** A point is reachable from one motor if the distance to it falls between the fully-extended arm length `(L1 + L2)` and the fully-folded difference `|L1 - L2|`. Both arms must satisfy this simultaneously, hence `reach1 & reach2`.

**`with np.errstate(invalid='ignore', divide='ignore'):`** Some grid points will cause divide-by-zero or arccos-of-invalid-value warnings during the math (e.g. points exactly at a motor origin). This suppresses those warnings since the `valid_reach` mask will filter those points out anyway.

**`alpha1 = np.arctan2(Y, X + L0)`** **`alpha2 = np.arctan2(Y, X - L0)`** For each grid point, this is the angle from each motor to that point, measured from the +x axis. Think of it as "which direction does the proximal link need to point to face the target?"

**`beta1 = np.arccos(np.clip((L1**2 + D1**2 - L2**2) / (2 * L1 * D1), -1.0, 1.0))`** **`beta2 = np.arccos(np.clip((L1**2 + D2**2 - L2**2) / (2 * L1 * D2), -1.0, 1.0))`** This is the law of cosines — solving for the angle between the proximal link and the straight line from motor to target. The `np.clip(..., -1.0, 1.0)` prevents floating point errors from pushing the argument of `arccos` slightly outside `[-1, 1]`, which would return `NaN`.

**`theta1 = alpha1 + beta1`** **`theta2 = alpha2 - beta2`** The actual proximal joint angles. For the left arm, you add `beta1` to `alpha1` to get the elbow-out configuration. For the right arm, you subtract — this is the specific branch choice that enforces elbows pointing outward rather than crossing inward.

**`E1x = -L0 + L1 * np.cos(theta1)`** **`E2x = L0 + L1 * np.cos(theta2)`** The x-coordinates of each elbow joint, computed from the motor positions and proximal joint angles. Only x is needed for the next check.

**`elbows_out = (E1x < X) & (E2x > X)`** Verifies the elbows-out constraint explicitly: the left elbow must be to the left of the target, and the right elbow to the right. This filters out the elbow-in solution that the IK math could also produce.

**`cos_gamma_max = np.cos(np.radians(comfort_angle_deg))`** **`cos_g1 = (L1**2 + L2**2 - D1**2) / (2 * L1 * L2)`** **`cos_g2 = (L1**2 + L2**2 - D2**2) / (2 * L1 * L2)`** **`comfort1 = (np.abs(cos_g1) <= cos_gamma_max)`** **`comfort2 = (np.abs(cos_g2) <= cos_gamma_max)`** This is the transmission angle check. `cos_g1/g2` is the cosine of the angle between the two links of each arm (again law of cosines). When this angle is near 0° or 180°, the arm is near-singular and force transmission is poor. The comfort zone rejects points where this angle is within `comfort_angle_deg` (15°) of those singularities.

**`mask = valid_reach & elbows_out & comfort1 & comfort2`** ANDs all four conditions together into a single boolean grid — a point is in the valid workspace only if it passes every check.

**`return mask`** Returns the 2D boolean array, which the plotting code uses to render the green region.

### Sending Motor Angles to ESP32
```python
cmd1 = M1_SIGN * (result["theta1_deg"] - HOME_ANGLE)
cmd2 = M2_SIGN * (result["theta2_deg"] - HOME_ANGLE)
command = f"{cmd1:.2f},{cmd2:.2f}\n"
if SERIAL_ENABLED:
	ser.write(command.encode())
	ser.flush()
print(f"  >> Sent: {command.strip()}")
print(f"--- Monitoring for {MONITOR_SECONDS:.0f}s ---")
global monitor_until, monitoring_active
monitor_until = time.time() + MONITOR_SECONDS
monitoring_active = True

```

**`cmd1 = M1_SIGN * (result["theta1_deg"] - HOME_ANGLE)`** **`cmd2 = M2_SIGN * (result["theta2_deg"] - HOME_ANGLE)`** Converts the absolute IK angles into relative motor commands. Subtracting `HOME_ANGLE` (90°) gives the displacement from the home position. Multiplying by `M1_SIGN` / `M2_SIGN` (-1 or +1) corrects for physical motor orientation — if a motor is mounted flipped, its positive direction is reversed so you negate the command to compensate.

**`command = f"{cmd1:.2f},{cmd2:.2f}\n"`** Packs both values into a single string like `"-12.34,45.67\n"`. The `:.2f` formats each float to 2 decimal places. The `\n` newline at the end acts as a message terminator — the ESP32 firmware likely uses `Serial.readStringUntil('\n')` or similar to know when one complete command has arrived.

**`if SERIAL_ENABLED:`** **`ser.write(command.encode())`** **`ser.flush()`** Only attempts the send if a real serial connection exists. `.encode()` converts the Python string to bytes since `ser.write()` requires bytes. `.flush()` forces the OS to push the bytes out immediately rather than buffering them.

**`print(f" >> Sent: {command.strip()}")`** Prints the command to your console for debugging. `.strip()` removes the trailing `\n` so it prints cleanly on one line.

**`print(f"--- Monitoring for {MONITOR_SECONDS:.0f}s ---")`** Just a console label telling you that the program is about to start listening for responses from the ESP32 for the next `MONITOR_SECONDS` seconds.

**`ser.write()`** is what actually queues the data — it writes the bytes into the serial output buffer.

**`ser.flush()`** forces that buffer to empty immediately, pushing the bytes out over the wire right away rather than waiting for the buffer to fill up or some internal timeout to trigger.

So the analogy is: `ser.write()` puts the letter in the outbox, and `ser.flush()` tells the postman to go deliver it right now instead of waiting for his normal rounds.

### Receiving Output From the ESP32
```python
def update_animation(frame):
	global monitoring_active
	
	if SERIAL_ENABLED and monitoring_active:
		now = time.time()
		if now < monitor_until:
			while ser.in_waiting:
				line = ser.readline().decode('utf-8', errors='replace').rstrip()
				print(line)
		else:
			# Drain any remaining bytes and close out the window
			while ser.in_waiting:
				line = ser.readline().decode('utf-8', errors='replace').rstrip()
				print(line)
			print("---\n")
			monitoring_active = False
```

**`if SERIAL_ENABLED and monitoring_active:`** Only tries to read if a connection exists and a command was recently sent. `monitoring_active` gets set to `True` in `on_click` each time you send a command.

**`if now < monitor_until:`** `monitor_until` is a timestamp set to `time.time() + MONITOR_SECONDS` when a command is sent. So this reads incoming data for 3 seconds after each command, then stops.

**`while ser.in_waiting:`** `ser.in_waiting` is the number of bytes sitting in the serial buffer waiting to be read. The while loop keeps reading as long as there's something there.

**`ser.readline().decode('utf-8', errors='replace').rstrip()`** Reads one line at a time from the ESP32. `.decode()` converts bytes back to a string, `errors='replace'` handles any garbled bytes gracefully, and `.rstrip()` strips the trailing newline.