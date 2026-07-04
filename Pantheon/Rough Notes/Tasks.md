- Make a website for pantheon
	- Blog section
	- About us section
	- Update the website with information we have so far
- Initial sketches
- Mechanical Design
- Electrical Task Brief
- Source parts
- Agree on sensor selection
- Set up environment for for simulation
- Build pitch deck
- Cold email template


### How to Plan
Start from deliverables, not activities
- Brainstorming tasks bottom up is how you end up with an infinite list ("what could we work on"?)
- Find out what are the concrete things that need to exist for this robot to drive across campus to delivery food
	- Mechanical chassis
	- Wired and tested sensor suite
	- Working navigations stack
	- Project website 
	- Funding
- Deliverables are finite

Apply dependency logic
- Ask which ones block other ones?
- Can't integrate sensors until chassis exists and sensors are purchased
- Can't run SLAM until sensors are mounted and powered
- You can write a pitch deck before you have a chassis
- You can prototype ROS2 nodes in simulation before any hardware arrives

Distinguish between what's actionable now versus what's blocked
- A task is actionable right now if and only if it has zero unmet dependencies
- No parts, no mechanical design, sensor stack chosen
- Can't build the robot, but you can design it
- Can't test sensors but you can order them
- C