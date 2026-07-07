### Goal
None of the project management software that are available right now are suited for how I want to be able to organize tasks. They're all too complicated. These are the things that are important to me:
- I need the application to display as a work breakdown structure in the tree diagram format similar to this 
	- I need to be able to branch the project out into each sub team and then breakdown the deliverables and the tasks for that team
	- I think I'm going to have a legend and color code each of the sub teams (mechanical, electrical, software and non-technical)

![[Pasted image 20260706170648.png]]

- From the root node which is the project, we are going to branch out into the main deliverables
- From each of the deliverables it will branch out into tasks and then subtasks and go down as far as we need it to go until tasks can be done without dependencies
- Each deliverable will be labelled with a whole number
	- A task will be that number with a decimal
	- A task from that will be that number with another decimal
	- For example 1.1 then 1.1.1
- Each task and deliverable should also have a blocked, in progress, or completed tag that can be tracked with color codes
	- Red for blocked
	- Green for completed
	- Yellow for in progress
- When you click on a task it should open to a new page that shows what other tasks was dependent on it and also show the status of that task
- It should also show a description of the task of what is considered as "done" for the task
- With all of this, we there should also be a kanban board that gets updated as well when the tasks are updated so that we have a day to day view
- The kanban should also be able to be filtered by the sub team

I need you to develop a project management tool for me. This project management tool is to serve as a way to breakdown project deliverables and tasks and to look at all the tasks that need to be done in a bigger picture view. Current tools offer too many features for project management and I only need two tools that work really well together. A tree diagram work breakdown structure and a Kanban board that integrates with the tree diagram work breakdown structure. This will be a webapp to be used by me an 4 other group members for a project that we are working on. I want the title of the website to be SimplePM. I want users to be able to enter a password before being able to use the system because I only want our group members to use the planning software. After entering the program there should be an option to create a new project or work on existing projects. After creating a new project the user should be able to enter the title of the project which will serve as the root of the tree diagram for the work breakdown structure. If they click an existing project then if there is an existing project it should list that project and when you click on it it should open into the work break down structure tree diagram

**Vision for the work breakdown structure**
![[Pasted image 20260707091735.png]]

Attached above is how I want the the tree diagram to function. The screen should start with a blank screen with only the project name as the root node with a tab at the top that we can use to switch between the WBS (Work Breakdown Structure view) and the Kanban view that will be linked to everything that happens in the work break down structure. In the WBS tab there should be a tab on the left hand side with the different blocks that can be selected. These blocks will be "Deliverable", and "Tasks." The user should be able to drag and drop each one of the boxes. A deliverable block can only be attached to the root node and a task block can only be attached to a deliverable block. You attach a block by clicking one block and then the next block and the application should automatically attach lines to the blocks and organize them accordingly as well. The space available should seem infinite and should be able to zoom in or out on the structure. To start of the user journey the user would most likely drag and drop a deliverable block. When they click on the deliverable block there should be a box that appears that allows them to edit the deliverable box. They should be able to write a title for the deliverable and number it a whole number. This number will serve as a way to connect the other tasks to this deliverable. Lets say the project title is "Pantheon" and the user drags and drops a "Deliverable" block. They will then click on the block to assign a number to it "1" and title the deliverable "Mechanical Design". In this edit box they should also be able to label it as "Mechanical", "Electrical", "Software" or "Non-Technical" for the sub team that that task belongs to. Once labelled it should change to a color dedicated to that specific sub-team. You can decide the colors just include a legend in the work breakdown structure tab. The edit box should also have space for a description of what the task is.