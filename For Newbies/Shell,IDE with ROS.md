Although this doc is titled getting started, most of these are not instructions for the absolute novice.  
I'm making this now that I've had to go through a linux reset, and a lot of the customisations I've made are things that ought to help anyone.  

## Customising the shell

zsh (prounounced "zee shell") is a superset of bash. Anything that works in bash, will work in zsh. zsh has a lot of additional features built on top of bash.  
This repo: https://gist.github.com/n1snt/454b879b8f0b7995740ae04c5fb5b7df goes through the basic steps to customise your zsh using "Oh my zsh."  
Personal recommendation: just zsh-autosuggestions zsh-syntax-highlighting zsh-autocomplete plugins will be fine.

The prompt may not be to your liking, so go ahead and edit the theme in the zshrc. You can go ahead and edit an existing theme file to suit your taste, (remove Right prompt, or changing the Left prompt to the time, for instance) since making your own theme file is probably too much work.  

Side Note: If you've already set up in bash, you must start sourcing .zsh files, not .bash files when it comes to ROS.  
At the end of the .zshrc, source ROS commands by adding  
`source /opt/ros/noetic/setup.zsh`

A terminal multiplexer, like tmux might be good idea, and if you find the idea your cuppa tea, try setting up Regelith, which makes the entire UI a multiplexer, and heavily relies on keyboard shortcuts for navigation. It is a great deal more work than setting up zsh though.  

## Aliases

Set up aliases as and when you find the need for them. They largely streamline the process by avoiding frequently used/large commands.  
Here are some of the aliases I like to use:  

```
#ROS Aliases   

alias buildthis="catkin_make;source ./devel/setup.zsh;"  
alias sourcethis="source ./devel/setup.zsh"  

BOT_UNAME=abhiyaan2  
BOT_IP=192.168.0.104  

alias bot_ros="export ROS_HOSTAME=$(hostname -I | head -c -2 -); export ROS_MASTER_URI=http://${BOT_IP}:11311;"  
alias bot_mount="sshfs ${BOT_UNAME}@${BOT_IP}:./rosserial_ws ~/rpi/mnt"  


#IDE Aliases  

py(){
	<your path here>/bin/pycharm.sh $1 &>/dev/null &! disown  
}

clion(){
	sh <your path here>/bin/clion.sh $1 &>/dev/null &! disown  
}

#Other Aliases

gedit(){
	/usr/bin/gedit $1 &! disown;  
}
```

The last 3 are custom functions, and not aliases, technically. I wish to be able to give an argument of a folder or a file to open, and that's not posible with a simple alias. $1 is the first argument, &! sends it to the background, so that I can take back control and do not have to sacrifice a tab for the IDE, disown detaches the process from the shell so that closing the terminal doesn't shut the IDE, and &>/dev/null suppresses all output and error messages. It is not good practice to suppress things unless you are confident. Do not do this for things like roscore. Do NOT disown roscore either.  
Aliases are a very custom thing, so ideally, you should make your own, as per your convenience, rather than taking someone else's and trying to get used to them. If you make em, you'll love em.  

## Setting up IDEs with ROS

By default, Pycharm uses the basic Python interpreter. This means that it doesn't recognise things like rospy to be valid commands.  
Having autocomplete, documentation jumps etc are super super helpful so this is something you *have* to enable.  

In Pycharm, at the bottom right, you'll find the python interpreter listed. Go ahead and click that, and add a new local interpreter.  
Contrary to popular belief, we do not need a virtual environment interpeter. We are going to keep the basic interpreter as the default one, so use an existing interpreter and choose the default python file in bin.  

Now that a new interpreter has been made, we can ask it to recognise ROS's existence. To do so, go back to the original screen, click the interpreter, and go to interpreter settings. Click the dropdown under the interpreter, and go to Show All.  
On the top left of the window find the button "Show interpreter paths"; this is the list of folders the interpreter scans for valid packages and commands.  

First, add all ROS packages by adding this path: /opt/ros/noetic/lib/python3/dist-packages  

Next, to configure this interpreter for a particular workspace you are in, add this: <path to ws>/devel/lib/python3/dist-packages  
This becomes very cruical when the workspace is at the vehicle level and you have so many packages working together.  
Although you can add multiple workspaces to the same interpreter in this manner, please don't. Make a workspace interpreter whenever needed. It's just a list of paths you're changing, not much of an overhead.  
