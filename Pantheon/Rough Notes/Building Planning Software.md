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

I need you to develop a project management tool for me. This project management tool is to serve as a way to breakdown project deliverables and tasks and to look at all the tasks that need to be done in a bigger picture view. Current tools offer too many features for project management and I only need two tools that work really well together. A tree diagram work breakdown structure and a K
