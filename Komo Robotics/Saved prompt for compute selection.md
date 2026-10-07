1. Ideally I would want the robot to operate fully autonomously and maybe not hours at a time, it just has to be able to do 1 fully autonomous delivery which I assume will take 15 minutes max
    
2. It needs to navigate, hallways, crowded lobbies, elevators, narrow dooryways, no ramps.
    
3. The cruise speed will be adjustable from 0.5 m/s to 1.2m/s
    
4. During autonomous navigation, people shouldn't be closer than 30cm to the robot
    
5. It will be using 3D LiDAR SLAM as the main way to handle localization. We will have an IMU, 3D LiDAR, wheel encoders and an RGB-D camera if needed
    
6. The robot will maintain a prebuilt map of the building
    
7. We will have separate maps for each floor
    
8. The deployment will be at most 7 floors
    
9. It should perform 3D obstacl detection in order to avoid obstacles and people