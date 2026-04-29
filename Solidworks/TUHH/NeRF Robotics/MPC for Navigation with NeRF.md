The time horizon in MPC represents the future timespan over which we predict and optimize. It comprises two key elements:

1. **Prediction Horizon Length**: The total future time window we're considering (in your example, 1 second)
2. **Discretization**: How we divide that horizon into steps (in your example, 10 steps of 0.1 seconds each)

Here's how the process unfolds in practice:

At time t = 0:

- The MPC estimates the current state
- It predicts states at t = 0.1s, 0.2s, ..., 1.0s based on different possible control sequences
- It optimizes a control sequence for the entire horizon
- It implements only the first control action (for the 0-0.1s interval)

At time t = 0.1s:

- The MPC gets a new state estimate (which might differ from what was predicted)
- It shifts the prediction window forward, now predicting states at t = 0.2s, 0.3s, ..., 1.1s
- It optimizes a new control sequence
- It implements only the first control action (for the 0.1-0.2s interval)

And so on, continuously shifting the prediction window forward as time progresses. This is why it's called a "receding horizon" approach - the horizon keeps moving forward with time.

This approach gives MPC some important advantages:

- It provides the foresight of planning ahead
- It maintains the responsiveness of continuous feedback
- It allows for adaptation when predictions don't match reality

The selection of horizon length and step size involves tradeoffs:

- A longer horizon sees further into the future but requires more computation
- Smaller step sizes give finer control resolution but increase computational load
- The horizon needs to be long enough to capture significant dynamics (like approaching a turn or obstacle)

For your robot navigation task with the NeRF model, this means you'll be constantly capturing new images, re-localizing, and planning new optimal trajectories - all while only executing the immediate next control action. This constant re-planning is what makes MPC robust against both model inaccuracies and unexpected situations.


### NeRF MPC Steps
There are actually two separate processes happening:
1. **State Estimation (Localization)**  
    At each time step, you capture an RGB image and query the NeRF model to determine where the robot actually is. This gives you the current state (position and orientation) of the robot. This is not part of the prediction or cost function evaluation - it's the input to the MPC process.
2. **Prediction and Optimization**  
    Once you know the current state, the MPC creates predictions about future states based on different possible control sequences. These predictions use a dynamic model of the robot (not the NeRF model). Then the cost function evaluates how well these predicted trajectories follow your desired path.

So the workflow at each time step would be:

First: Use the RGB camera + NeRF model to determine where the robot actually is right now (state estimation).

Second: Using that current position as a starting point, the MPC predicts multiple possible future trajectories based on different control inputs. These predictions use the robot's dynamic model (like how it responds to steering and acceleration commands).

Third: The cost function evaluates these predicted trajectories against your desired path. It finds the sequence of control inputs that would most closely follow the path while respecting constraints.

Fourth: Apply only the first control command from this optimal sequence.

Fifth: Wait until the next time step, then repeat the whole process.

The key point is that the NeRF model isn't used for prediction - it's used for localization. The predictions come from a separate dynamic model of the robot's motion. The cost function doesn't directly compare actual position to predicted position; it compares predicted future positions to desired positions on your path.