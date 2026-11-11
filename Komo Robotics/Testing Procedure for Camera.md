### Marketplace Inspection Checklist
- USB Connectivity
- RGB Camera
- Left stereo camera
- Right stereo camera
- Depth estimation
- 3D point cloud
- IR dot projector
- IR flood illuminator
- IMU
- Calibration data

### Physical Checks
- Check the front glass over all three lenses and the two IR windows for scratches, cracks or haze
- Check that USB-C port isn't loose or bent and the plug seats firmly
- Look for missing or chewed-up screws
- Cracked front means walk away

### Run Python Script
- USB speed: Super
- Sensors: IMX378 color camera plus two OV9282 mono cameras
- Focus: color camera should report fixed focus
- IR driver: This is what makes it pro
- IMU: detected
- Factory Calibration readable: Should be close to 7.5 cm. If calibration is missing or wiped then depth will be garbage
- Take a photo of the device ID it prints

### Check image streams
- Cover each lens with a finder, one at time. The matching window should go dark: color, left, then right
- This catches mixed-up or dead sensors
- Point it at a plain surface and look for stuck pixels, dark spots or dust blobs that stay in the same place when you move the camera
- Hold the printed page about 50 cm away. text should be crips in the color view. The sharpness number goes up as focus gets better you can compare distances. A fixed focus lens should look sharp from roughly 50cm out to the far wall

### Check Depth Accuracy with the 