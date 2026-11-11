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

### USB 
![[Pasted image 20261111173800.png]]
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

### Check Depth Accuracy with the Tape Measure
- Aim the white center box at a flat wall at 0.5m, 1 m and 2 m. Compare the on screen "center=" reading to the tape
- Being off by about 1-2 % is normal so 1-3 cm at 1 m is normal. If it's off by 10 cm or more, or the reading jumps around a lot the calibration is bad or something has shifted
- Stay at least 30-50 cm away. The camera can't measure depth closer than about 20 cm away

### Check the IR dot projector
- Point it at the plainest wall you can find with dots off. The depth view should have black holes and valid % should be low
- Press d. You should see a speckly of dots in both mono views, the holes should fill in and valid% should jump
- Don't look into projector

### Check the IR flood LED
- Make it dark: Throw the jacket over the camera and your hand or point it under the table. The mono view will be close to black
- Press f. the mono views should light up clearly
- The color camera won't brighten. That's normal because it blocks IR light

### Check the IMU
- Sitting still, one accelerometer axis should read about + or - 9.8 and the gyro should read close to 0
- Tilt it 90 degrees and the 9.8 should move to another axis. Rotate it and the gyro numbers should spike then settle

### Leave it running
- Leave the dots on and let it run. Wiggle the cable at both ends partway through
- Press q at the end. The summary should show no crash or disconnect, steady frame rates and a stable chip temperature

### What to expect
![[Pasted image 20261111170733.png]]![[Pasted image 20261111170749.png]]![[Pasted image 20261111170805.png]]![[Pasted image 20261111170839.png]]

### RGB Window Look For
- A normal, smooth color image. Watch for frozen frames, colored lines, or permanent spots.
- The sharpness number has no “good” value on its own. Use it to compare: hold your text page at 50 cm, then 1 m, and the image should stay sharp. With a fixed-focus lens, very close objects (under about 30 cm) will look a bit soft. That’s normal.

### Mono Pair Window
- Two grayscale images that look nearly the same. Objects sit slightly further left in the right image, which is how stereo works.
- Both halves should be about equally bright and clean. If one side is black, very noisy, or much darker, that sensor is a problem.
- When you cover a lens with your finger, only that half should go dark.

### Depth Window
- Each pixel’s color is its distance. Blue is close, then cyan, green and yellow, up to red at about 4 m or farther. Black means the camera couldn’t work out a distance there.
- Some black is normal:
    - a strip down the left edge, which only one camera can see
    - anything closer than about 35 cm
    - shiny or completely plain surfaces when the dots are off
- **Tape-measure test:** point the white square at a flat wall and read “centre=” at the top. At 1 m it should say roughly 98–102 cm. “valid=” is the percentage of the image that has depth.
- **Dot projector test:** on a blank wall with dots off, you’ll see lots of black and a low valid %. Press d and the black patches should fill in with color and valid % should jump. At the same time, small speckles appear in both mono images.
- **IMU lines at the bottom:** with the camera sitting still, one of the three acc numbers should read about ±9.8 (gravity) and the other two near 0. Tilt the camera and the 9.8 moves to a different axis. Gyro numbers sit near 0 and jump when you rotate it.