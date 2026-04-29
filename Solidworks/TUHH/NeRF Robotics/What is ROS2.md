Ros is a set of tools, practices and libraries that answer a set of questions that will need to be answered regardless of developing a robot or autonomous system.

ROS2 helps us answer questions like the following:
- How do you want to write programs
- What language
- How to package code together
- How do you want to do inter process communications
- How do you want to bring up multiple processes
- How do you want to communicate with other systems
- How do you want to write drivers for sensors and motors
- How do you want to implement high level algorithms

Ros provides us with a narrowing scope of the above and more. It provides roboticists with a system that is very fast to deploy using already existing code and packages. You then slot these pieces together and focus on things that are actually important like integrating new algorithms.

ROS provides with standard packages like a path planning package and an occupancy grid package which are all part of the nav2 stack. An important point to note is that the ROS system is easily composable. You can start with a bunch of small nodes that are designed to their specific interface (topics, services and actions) and replace just those nodes. Ideally ROS provides us with a way to prototype a system really fast and then replace specific components within a system with the technologies we want. ROS allows for this plug and play interface. This is doable since autonomy for many robots follow very similar concepts (path planning, slam, etc.) which allow for easy plug and play.

At its's core, ROS can be thought of as **standardized messaging system** with a large set of standard message structures. ROS allows us to easily send data in different message structures from system to system. ROS also provides with a standard for how to package code up so it can used by other nodes and packages. Again, the whole point of ROS is provide around 80% of what's common across pretty much every robotics system so you can set that part up quickly and then dedicate your real effort to the new 20% which is unique to the problem instead of wasting your time reinventing the wheel.

Another way to think of this is for prototype or proving that an algorithm works without writing the different foundation parts from scratch. You can think of it as a way to wrap different parts into an API so they can all talk to each other easily.

Another great way to look at this is imagine you were a company that made bricks. If there wasn't a brick standard then everyone could just make whatever size brick is convenient to them. You could have 2 inch tall bricks and someone else could make 3.5 inch bricks. You could build a house with the mix of two but it would be a lot easier if everyone agreed on two inch bricks. ROS is a **standard**. A set of rules, that if followed means things should work together