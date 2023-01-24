## Sourcing

This contains some brief notes I made when I was doing my Application.  
It contains only extremely basic stuff, but I find it useful to come back here once I suffer bursts of amnesia.  
This is more of a quick overview of some commands or other things. A proper tutorial is always in ROSWiki.  

For starters, we will be calling a bunch of commands here : rosthis rosthat blah blah blah.
To let our shell know that these are in fact, valid commands,you'll have to walk upto the shell and be like hey lookie here, ROS exists!
source /opt/ros/noetic/setup.bash
This command does exactly that. (Source just means run this bash script. The variables and function defintions in that bash script will be retained by the terminal. That's the whole point. If we execute the script using ./, the environmental changes will not be retained.) In fact, instead of doing this every time we open a shell, we just add it to our bashrc file that runs on shell startup.  
(That is, if you use a bash shell. If you use zsh, for instance, there is a setup.zsh file in the same ros noetic directory. To find out which shell you use, echo $0)  

An important notice: Here, we are still using ros noetic. This is an awesome version of ROS 1.  
There have however been several upgrades, and ROS 2 is now en vogue.  
Once we switch to that, we can just not source noetic commands, and source ros2 instead.  

## Roscore 

This contains a ton of background processes that are essential before you can do anything with ROS. It sets the basic platform on which everyone can interact. ROS is not an operating system, even if the name seems to suggest so. I'd say it's closer to a communication protocol. ROS messages allow various codes, presumaby in different languages to interact in a seamless manner.  

You will probably need to always give a tab of the terminal for roscore. Do NOT forget the existence of roscore and then be confused about errors.  

I say probably, because when we use launch files for instance, roscore is automatically initiated in the background for us, and we can live happily ever after.  
This is more of a concern when you get code from elec who apparently have never heard of this mysterious concept called launch files. THey'll tell you a sequence of python files to run, which you can then promptly add to a launch file.  

## Nodes

Initiate a node:  
`rosrun [package_name] [node_name]`  
Happens a lot in testing stages, but make good launch files later.  

Fetch a list of all running nodes:  
`rosnode list`  

Fetch info about one particular node:  
`rosnode info [node_name]`  
Super useful, especially in complex scenarios, when combined with rosnode list. Typically we work extensively with topics, and not care about nodes, but looking at the nodes and their info is the best way to confirm if things are initiating and working in the correct manner. Most of what you want here is packaged neatly in rqt; a very good debugging tool.  
Once you build something complex, the rqt_graph is among the most beautiful things on this planet.  


## Topics

Fetch the info fed into a particular topic.  
`rostopic echo [topic]`

Multiple nodes may publish into a topic, and multiple may subscribe to the feed of that topic.  
To fetch type of the messages in the feed:  

`rostopic type [topic]`  

To publish some data into a topic ourselves,  
`rostopic pub [topic] [msg_type] [args]`  
This is rare, and is typically used only for preliminary testing.  

This gives the frequency at which the data feed gets new information:  
`rostopic hz [topic]`  
This command is just trivia at this point.  

## Services

Fetch a list of all available services  
rosservice list  

A service is essentially a way to tell a node pls do X.  
To make use of such a service ourselves, we "call" it.  

`rosservice call [service_name] [thingsyoumaywanttosay]`  

To know what are the things you should tell the service to do what you want, first use  
`rosservice info [service_name]`  
Or even just  
`rosservice type [service_name]`  

This should tell you what type of msg you must send. Now use  
`rosmsg show [typename]`  
This tells you what is expected as a feed for this type of data.Alternatively, You can just tab complete your way to the format.  

## Parameter Server

All the important parameters are stored here.  
`rosparam list`  
`rosparam get [paramname]`  
`rosparam set [paramname] [new_value]`  

`rosparam get/ ` gets all parameters. We can store these for later using `rosparam dump [file_name] [namespace]`  
A namespace is basically a grouping system for topics. for instance, a topic may be called /sbg/gps or /left\_wheel/enc\_rpm. All the topics under the sbg imu, will begin with an sbg namespace. Do not pollute the global namespace by just dumping all your topics out. Further, namespaces give these really pretty boxes when visualised in rqt.  

We can load the dumped rosparams using `rosparam load [file_name] [namespace]`

## ROSBags

Rosbags are an incredible tool when it comes to testing code, and reviewing runs. They record every ros message published to a set of topics you choose. to record data from all topics, simply do `rosbag record -a -O mycustomname.bag`  
-O renames the bagfile from the default, which is just the timestamp.  

When you play back a rosbag, all the same topics and nodes are recreated, and all the same messages are published. This way, if I say, recorded all the encoder pulse data, I can now rosrun an encoder odometry processing file on my system, seperately, while playing that rosbag which gives real life data, in the real format. This is great, because you don't have to test every piece of code live on the vehicle.  

## Making Packages

First, create a new catkin workspace. Just make an empty workspace folder with an empty src folder. Run catkin_make. It will do all the magic for you.  
And by the way, you can avoid 2 directory make commands using `mkdir -p ws_name/src` the -p means "make any parent folders if required."  

cd to the src folder of your workspace. Then run the following script to make the foundational stuff.

`catkin_create_pkg <package_name> [depend1] [depend2] [depend3]...`

Most used dependency packages are rospy roscpp std_msgs  
Now, you can cd into your package folder, and get to work.  
Standard practice is to make an src folder and fill it with cpp files that will be your nodes or whatever.  
Python scripts go in a scripts folder, even if they may be equivalent in function to cpp files. If elec puts all python files in src, please move it out to a scripts folder.  

These scripts will automatically be recognised as valid scripts of the package if you give executable permisions to all for that file.  
Finally to run your package, scripts and launch files, you must build the workspace you work in. Just run `catkin_make` and things will work out.  

Your package files are still not accessible to the terminal, or the ros commands in it, so yet again, we must execute a bash file for some setup. Oddly enough, we needn't source the file. Only ROS needs to know something from this file, I suppose.  
`. ~/catkin_workspaces/vajra_ws/devel/setup.bash`  

(If you use zsh, change that to .zsh)(I find having a catkin_workspaces directory very helpful, since we'll be making a lot of workspaces.Each project gets a workspace, and each serious modular piece of it gets a package. Do not put everything in one package; kinda defeats the point.)  

I recomend aliasing the source and catkin make commands as you'll need to do it fairly often. sourcing has to be done in each new terminal tab, so it becomes a pain to keep typing it. A terminal multiplexer can save you from that, but setup and getting used to one takes a bit of time.  

I used to have build aliased to `cd ~/catkin_ws/; catkin_make;. ~/catkin_ws/devel/setup.bash; cd -'` (Reminder: this kind of info can be found using "type cmdname")
but now I'd recommend using a relative path, and building the pwd directly instead. That's way more versatile.

## Rosdep

When packages get large and complex, we will inevitably have to import external libraries. These public packages need to be installed to run the code on a new machine. The package.xml lists the dependencies required to run that package. PLEASE maintain that properly. If you need, say, a custom rosmessage type form another one of your packages, in the same workspace, just add <depend pkgname/>   

Unless you have all dependencies, and your packages compile in catkin_make correctly, your IDE will not recognise your workspace commands to be valid, and autocomplete documentation etc won't work.  

Run this to get all the dependencies automatically installed: `rosdep install --from-paths <path to catkin_ws>/src --ignore-src -y`  

## Gazebo

Sigh.  
Here there be dragons.  

Firstly, you can go ahead and make a normal package in the workspace.  
In this package, we will need two new folders:  
-> description, which contains files describing the model of the robot you are working with. The dimensions of each and every part, the mass, the moment of inertia tensor, where the pivots and joints are etc.  
The files are in a format called URDF (Unified Robot Description Format)  

-> world, which contains .world files that contain information about the gazebo world you are working with. It may contain any number of objects, for instance, traffic cones, potholes and black methylamine barrels with a bee on it.  

Once we have both of these... well, we'll worry later.  

First, let's describe the robot.  
The URDF uses extended markup language, xml. You'll be familiar with the html stype of <Tag> stuff </Tag>. <Tag stuff/> self close tags for short tags are also here.  

Now there are two things we will describe: 1) the fundamental blocks of "stuff", of whatever shape, called "links" and 2) The "joints" between the links.  

1)Links are fairly straightforward.  
```
<link name="yournamehere">
	
	<visual>
	
		<geometry/>	(Specify the shape of the object)
		<origin/>	(Specify the origin about which the shape specified must be constructed)	
		<material/>	(Specify the material of which it is made. This specifies colour etc.)
		
	</visual>
	
	<collision>		(Here, you specify the shape of the link when it is being considered for collisions. This is what you would call a "hit box" in a game. 
				The visual of a link may be a sophisticated shape for which it's computation intense to treat it as such in a collision,
				so a simpler shape,for instance, a cuboid, could be used for collision calculations.)
		<geometry/>	
		<origin/>		
				(Of course, there is no need for a material tag here. Just the shape needs to be specified.)
	</collision>
	
	<inertial>
	
		<mass/>	(What is the mass?)
		<origin/>	(Where is the centre of mass?)
		<inertia/>	(What is the inertia Tensor?)
		
	</inertial>

</link>
```

Important notice: Inside the robot,a <material name="blah"> tag needs to be described for materials you want to use later inside the visual tags.  

2) Joints join 2 links. They may be of 4 types:  

(i) Fixed: There is absolutely no movement about this. You have put super glue and fixed the 2 links.  

(ii) Revolute: There is rotation permitted, but between a fixed start and end angle.  

For instance, I can rotate my leg about my hip joint, but it cannot rotate even a full 180 degrees. I think the most reasonable approximation, is to say that you can rotate the leg from 0 to 90 degrees. For the fancy 180 degrees apart yoga pose, you don't need some -90 degrees or anything. You just need to keep one leg’s joint unrotated, and lie down so as to place the joint near the floor, and rotate the other leg joint 90 degrees forward. hmm.. the body is weirdly flexible. Anyway, 0 to 90 sounds pretty much a perfect fit.  

(iii) Continuous : There are no limits of rotation. If we consider a ceiling fan's base and the rod as 2 links, they have a continuous joint. Full 360 is permitted.  

(iv) Prismatic : There isn't any rotation allowed, but there is some linear movement permitted. For instance, imagine a simple zip, well, not on a purse or a pant, but on a fixed, non wobbly surface. The runner can move only linearly along the railing of the zip, but cannot rotate. The runner and the zip are two links connected by a prismatic joint. (Of course, you can also imagine a 0 to 180 revolute joint between the base of the runner and the handle of the runner)  

Seeing joints in the real world is fairly simple, and helps you get the hang of it. However, specifying it in code seems daunting.  
It's not that big of a deal. In case there's some issue,just google the syntax,ros wiki probably has what you need.  
The two most important things we need to specify are the parent and the child links that the joint connects.  

Important Notice: Every link you create MUST be connected to some other link using a joint. Only one link is allowed to not have a parent, and that is the base_link.  
The base_link is a dummy link that should be named AS SUCH, and should not have any properties. It just specifies the robot's reference frame. <link name="base_link"/>  
(Well,you CAN make a base_link that is meaningful,but there are some issues if you have a base link with inertia, and every real part of your robot OUGHT to have inertia even to be displayed in Gazebo. Unless you have collision and inertia tags, you don't exist in Gazebo's eyes.)  
(and if you dont make a base_link you'll eventually hit a brick wall face first.) (like me)  

Okay.  
Now we have described a rudimentary robot.  
We can now import it in RViz (as well as in Gazebo) to admire its beauty. This involves writing a launch file.  
I don't really understand how those work. I just copied code from urdf tutorials and modified it to launch my urdf in the default empty world.  
Basically this is what is going on:  

Since every link is part of some network of joints, the states of the joints specify the state of the robot. This data needs to be published to /joint_states.  
A robot_state_publisher looks at these joint states and determines the positions of all the links, and allows you to do transformations between the reference frames of each link.  
This is extremely important, because data gathered by and delivered to different frames needs to be consolidated, and a change of coordinate bases is essential for that.  
So yeah, the robot_state_publisher looks at the urdf once, watches the/joint\_states topic, and publishes the TF (Transfer) system, and in addition to that, publishes the entire robot urdf to /robot\_description for other nodes to look at.  

Gazebo will look at /robot\_description, and begin its work. It will simulate the robot, and any changes made in the simulated world, are again fed into /joint\_states.  
We can visualise the TF system, and therefore the robot's state and all other sorts of things being published to ROS Nodes, using Rviz.  
Oh, also, there is some dependency issue that forces our hand and makes us publish static transforms just for the start of the run.  

So yeah, we are going to have to make a launch file that does all of that.  

Beyond this point, we will have to import plugins to describe functional parts of our robot, like a camera or a motor to control our vehicle. These should publish to a certain topic and then we can manipulate the data, and perhaps publish a velocity to our vehicle based on that data.   
