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
- Can't run real SLAM but can get simulation environment running
- Can't cold email without a pitch deck but you can build a pitch deck

Size tasks to be completable, not just desirable
- Built the robot is a deliverable, not a task
- "Design the chassis mounting plate for the Livox Mid-360 in CAD" is a task and has a clear done state, one person can own it and you can estimate how long it takes
- If a task takes more than two weeks of work for one person its actually a group of tasks hiding 
- Only break down the stuff that's actionable in the near term
- Tasks three months out can stay coarse grained for now

Separate workstreams that can run in parallel
- Mechanical and electrical is bottle necked on designing
- Software is bottlenecked by not having hardware
- Outreach, website, and pitch deck have no dependencies

Apply a time horizon
- Don't plan the whole project at task level granularity right now
- Plan the next 3-4 weeks in detail (specific tasks, owners, deadlines), the next 2-3 months at the deliverable level (what milestones do we need to hit), and the rest of the project at the goal level (what does the final system look like)
- Planning horizon or rolling wave approach

Sequence summary
1. List your major deliverables (the things that need to exist)
2. Map dependencies between them (what blocks what)
3. Identify what's actionable today given your current state
4. Break only those near-term items into specific, ownable tasks
5. Assign based on parallel workstreams so no one is idle
6. Leave future work coarse - refine it as we go