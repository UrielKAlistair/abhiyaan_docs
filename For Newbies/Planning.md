This is a high level overview of how we plan the path of the vehicle autonomously.  

![Flowchart](Images/PlanningFlow.png?raw=true "Title")

For the uninitiated, we create a grid of voxels (3D pixels) and assign to each a cost based on the occupancy of that cell. The vehicle will incur that cost should it attempt to travel through that cell. Given a start and an end, we can treat this like a graph theory problem, and use dijkstra's algorithm to navigate by finding the path of the least cost. There are other algorithms to navigate, but that is what the planning level is all about.  

The control level, where we make the veicle behave smoothly, appears to be unnecessary in our case. We do have some pretty good controller code, but it will likely turn out that we can do without them, and the path prescribed by the local planner will suffice. Further work here can be done once more pressing issues are taken care of.  

The Hardware level... well, we just say "Elec's job" and move on. But really, there is a lot of code that elec writes. They need to learn ROS, same as software, and use python. It would be legitimately great if software can make a move and cover some of that bridge instead of forcing elec to come all the way here. Even as it is, you'll need to eventually work quite close to elec layer and bridge to the rest of the software.

The behavioural level is the biggest deal. We are just making strides on this. Our discussions on the research papers we read and how we are proceeding lie here: https://docs.google.com/document/d/1Y_bfDrV-j5SgPnXCq7r_w8bvP2EhKZe3TSKMxfq1O4g/edit?usp=sharing

The perception that knits tightly with these levels is also a very big deal, but it's not my submodule, so I can't comment much about it at the moment. 
