![[Pasted image 20260924204719.png]]

| Sensor                                          | Priority                       | Main purpose                                      | Suggested location       |     |
| ----------------------------------------------- | ------------------------------ | ------------------------------------------------- | ------------------------ | --- |
| **360° 3D LiDAR**                               | Required                       | SLAM, localization, obstacle detection            | Top center               |     |
| **Front RGB-D camera**                          | Required                       | Humans, elevator doors, semantic perception       | ~0.8–1.0 m high          |     |
| **IMU**                                         | Required                       | Orientation and motion estimation                 | Near robot center        |     |
| **Wheel encoders**                              | Required                       | Odometry                                          | Drive wheels             |     |
| **Low-mounted 2D LiDAR / safety LiDAR**         | Strongly recommended           | Feet, carts, low obstacles, safety field          | ~0.15–0.30 m above floor |     |
| **Perimeter bump sensors**                      | Required for robust deployment | Last-resort collision detection                   | Around lower chassis     |     |
| **Downward ToF sensors**                        | Recommended                    | Stairs/drop-offs                                  | Front/lower perimeter    |     |
| **Rear or wide-angle cameras**                  | Recommended                    | Human/object awareness outside front camera FoV   | Rear/sides               |     |
| **Wrist RGB camera**                            | Required for arm               | Detect elevator buttons precisely                 | Arm/end effector         |     |
| **Force/torque or contact sensor**              | Required for arm               | Confirm button contact and control pressing force | Wrist/end effector       |     |
| **Arm joint encoders / torque/current sensing** | Required                       | Manipulator state and collision detection         | Arm joints               |     |
| **Barometric pressure sensor**                  | Useful secondary sensor        | Detect approximate vertical elevator travel       | Inside chassis           |     |
| **Battery/motor current/temp sensors**          | Required system telemetry      | Health monitoring                                 | Electronics              |     |
