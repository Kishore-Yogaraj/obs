I talked to Eddy about containerizing code and learned about the different kinds of workflows for **development** and for **deployment** in robotics. based on this conversation, I'm going to start using one container instead of 3 for the nerf navigation project since we only need one container. We don't run into a lot of dependency issues with our projects since the only dependency heavy node is the occupancy node and the other nodes can use the dependencies used in that node.

We use multiple containers for Wato because we have such a large team and run into multiple dependency conflicts with a team as large as Wato. Especially when everyone is working on a variety of different tasks. 
### Development Workflow
Development workflows should be simple and reproducible. I currently do this using **Docker** however **Rosdeps** make dependency management even easier. The ideal workflow is as follows:
-  Clone the repo
- Run rosdep install to get dependencies
- Use colcon build to compile the code
- Launch the ROS system
The key point here is that we want to avoid manually installing packages with apt outside of what rosdep handles. The makes the project more portable and easier for other developer to work with.


> [!Question] What is Rosdep?
> You can think of rosdep as smart package manager that is specifically designed for ROS projects. It's like a specialized shopping assistant that knows exactly what ingredients (dependencies) your robots "recipe" needs to work properly.
> 
> A ROS project typically depends on many different packages (some from ROS itself, some from ubuntu's package manager (apt), and others from various sources (pip)). Manually tracking and installing all these dependencies would be time consuming and error-prone. This is where rosdep comes in.
> 
> ROS packages declare their dependencies in special files called pacakge.xml. These files list everything the package needs to function, similar to how a recipe lists its ingredients. A computer vision package might specify it needs OpenCV, while a motion planning package might require certain mathematical libraries.
> 
> When you run `rosdep install` it reads these package.xml files and does something clever: It translates these ROS style dependency names into the actual package for the specific operating system. 
> 
> For instance, if a package needs "opencv", rosdep knows that on Ubuntu this translates to "libopencv-dev", while on another operating system it might have a different name. The real power of rosdep comes from its ability to handle this translation automatically. It maintains a database of rules that map ROS dependencies to the correct system packages across different platforms. This means the same ROS code can work on different operating systems without developers needing to worry about the specific package names on each platform.
> 
> The advantages of using rosdep become clear when you're sharing code with others or deploying to different robots. Instead of writing long installation instructions listing every dependency, you can simply tell users to run rosdep. It's like giving someone a shopping list that automatically translates into the correct product names for whatever store they're using.




> [!Question]   What's the difference between using rosdep and just installing every package from a requirements.txt when building a docker image

# Rosdep vs requirements.txt in ROS Development
This is a great question that gets at the heart of how dependency management differs between ROS and traditional Python projects. Let me explain the key differences and why they matter.

## Understanding requirements.txt
Let's start with requirements.txt, which is the more familiar approach if you're coming from Python development. When you use requirements.txt, you're specifically managing Python package dependencies. For example, you might have:

```txt
numpy==1.21.0
scipy==1.7.0
opencv-python==4.5.3.56
```

When building a Docker image, you'd install these with pip. This works well for pure Python projects, but ROS projects have more complex needs that make this approach less suitable.

## The Complexity of ROS Dependencies

ROS projects typically need three different types of dependencies:

First, there are system-level packages, which include build tools and system libraries. Second, you'll need ROS-specific packages, such as tf2, rviz, or other ROS components. Finally, you'll need Python packages like numpy and scipy.

This is where rosdep shines. When you declare dependencies in package.xml, rosdep handles all three types seamlessly. Here's an example of what your package.xml might look like:

```xml
<package>
  <!-- System dependency -->
  <depend>libopencv-dev</depend>
  
  <!-- ROS package -->
  <depend>tf2_ros</depend>
  
  <!-- Python package -->
  <depend>python3-numpy</depend>
</package>
```
## Advantages of rosdep

### System Integration
Rosdep installs packages through the system package manager (apt on Ubuntu). This ensures better integration with the operating system and can prevent conflicts that might arise from mixing pip-installed and system-installed packages.
### Cross-Platform Compatibility
Rosdep automatically translates dependency names to the appropriate package names for different operating systems. A package.xml will work the same way whether you're on Ubuntu, Debian, or other supported platforms.
### Dependency Resolution
Rosdep understands the relationships between ROS packages and can automatically install all required dependencies, including indirect ones. If package A needs package B, which needs package C, rosdep will figure this out automatically.
### Version Management
When using rosdep with ROS, you get the versions of packages that are known to work with your ROS distribution. This reduces the likelihood of version conflicts that can occur when manually specifying versions in requirements.txt.

## A Practical Example

Let's look at a concrete example. Suppose you have a computer vision ROS node that needs OpenCV.

With requirements.txt, you might write:

```txt
opencv-python==4.5.3.56
```


But with rosdep, you'd write in package.xml:

```xml
<depend>libopencv-dev</depend>
```

The rosdep approach is better here for several reasons. First, it installs OpenCV as a system package, which typically performs better than the pip version. Second, it includes development headers needed for compiling ROS packages. Finally, it maintains consistency with other ROS packages that might use OpenCV.

## Choosing the Right Tool
That said, requirements.txt isn't wrong - it's just optimized for a different use case. If you're building a pure Python application, requirements.txt is probably the right choice. But for ROS development, rosdep provides a more comprehensive solution that better handles the complexities of robotics software dependencies.

Think of rosdep as a specialized tool designed specifically for the unique needs of robotics development, while requirements.txt is a general-purpose tool for Python development. Each has its place, but in the ROS ecosystem, rosdep's broader capabilities make it the more suitable choice.

Making changes for commit 