### What I believe Is Happening
In the 5 bar parallel scara the two proximal links are not independent. They shar a common end effector. This means that any movement from the proximal link caused by 1 motor will also result in a bit of unwanted torque on the second motor through the coupled links. Once the second motor sees this movement it will want to error correct but by the time it starts correcting, motor 1 has moved further and creates mor disturbance. They're constantly chasing each other. However the disturbances become so small that the Kp term doesn't fully push the motors enough to get to where it needs to go. You can add a little bit of the I term but it won't account for the fix completely. 

### Lets walk through a scenario
Lets say we want the end effector to move diagonally across the workspace. We want both motors to move at the same time and the end effector traces a clean path to the target

**This is what actually happens**
- Motor 1 will start accelerating its link which puts a reaction force through the distal link onto Motor 2 link
- Motor 2's PID will see its position being disturbed and start compensating but its reacting to an error that already happened with some delay 
- Motor 1 is still moving so the disturbance on Motor 2 is changing the whole time
- Both loops now react to each other's corrections at the same tine

### Why can't PID solve this
PID can't solve this not because it does not see the error but because it is too late to respond to the disturbances. PID needs to act as the error happens in theory but in this case if motor 1 moves it creates a disturbance on motor 2 and motor 2 will try to correct this disturbance but by the time it corrects, motor 1 is still in motion so it exerts another disturbance and now motor 2 has to correct for that disturbance as well. After all of this reactive error correction, the position errors build up and the end effector ends up settling in the wrong position.