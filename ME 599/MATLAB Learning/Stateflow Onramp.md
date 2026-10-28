### What is a state machine?
Imagine a vision system that tracks objects. It will have multiple operating modes:
- Off
- Seeking
- Tracking

The active state is the current operating mode. The object tracker as a whole is a **state machine** which is a system that has a series of predefined states and can only exist in one state at a time

Examples of state machines
- Traffic signals
- Automatic transmission

State machines have a set of rules for how to switch between states. For example if an object is found in the object tracker then it will switch from the seeking mode to the tracking mode.
![[Pasted image 20261026175213.png]]

Above is **state transition diagram**. 
- A shape represents the state and the arrows indicate the transitions

### How to implement a state machine
You could translate your state transition diagram into code but that becomes unmanageable pretty quickly.

Instead, **state flow** allows you to model state machines as you would on a white board diagram

glpat-9UpVUPfFElhB4hOifK-PJ286MQp1Omhpdgk.01.0z033vm50

