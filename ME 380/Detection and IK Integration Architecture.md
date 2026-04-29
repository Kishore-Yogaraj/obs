### Batch Scan vs Per Object Detection
We are going to go with **scan then execute**
- The scene we are working with is fundamentally static which means we don't need to do per object detection. We could work with just one picture instead of a full video frame as well.
- Since the scene is not dynamic we will do the following process:
	- Scan
	- Build ordered pick list (**blue then red**)
	- Execute picks
- Now we avoid the complexity of continuous detection

### Directory Restructure
We are going to create a separate vision folder but refactor `send_ik.py` into a proper importable module

The biggest structural problem right now is that the inverse kinematics script is monolithic. We can't import a solve_ik() function from it cleanly which needs to change first.

![[Pasted image 20260323161715.png]]
### Flow for Detection for Now
- Scan and create pick list
	- Pick all blue then pick all red
- Wait 3 seconds
- Move to first objects
- Wait 3 seconds
- Move to second object
- Wait 3 seconds
- So on

### Flow After
- Scan and create pick list
	- Pick all blue then pick all red