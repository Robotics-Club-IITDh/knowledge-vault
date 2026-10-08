
This will go over how to install ros2 jazzy.
For other versions, the general approach will be the same but you will have to check the specifics yourself.

## Installation

First, open your terminal.
Type this command to check if you have UTF-8

```bash
locale  # check for UTF-8
```

UTF-8 is something that maps characters to their codes in binary. $, A, 7, 😎 all have UTF-8 codes. It is the standard, and most programmes assume that its being used. So if another code was being used instead, it would create problems. 

If it’s present, you will see something like this 

```bash
tushar@victy:~$ locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
...
...
```

If you **don’t** see UTF-8, run this command

```bash
sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
```

Now run the command to check if it’s there again and confirm.

Let us now add the relevant repositories.

```bash
sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update
```

Let us then add the gpg key ( something to do with encryption and security, not important )
And let’s add the ros2 repository to apts source.

```bash
sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

Let us also install ros tools that we may use later

```bash
sudo apt update && sudo apt install ros-dev-tools
```

Now to install ROS2, type in the following commands

```bash
sudo apt update
sudo apt full-upgrade
sudo apt install ros-jazzy-desktop
```

You should now have ros installed. 
However there is some additional setting up we need to do.

## Setup

As it is right now, if you run ros2 with the command

```bash
ros2
```

Then you might see an error saying “ros2 not found” or so.

This is because the terminal you are in still doesn’t know where to look for the command, what to execute, where to save and import files, and a thousand other things.
And that shouldn’t be surprising, because how should it know automatically?

To handle this and set everything up, ros comes with a file .
In this case,

```bash
/opt/ros/jazzy/setup.bash
```

(Replace .bash with the appropriate file for your terminal. For eg if you’re using zsh. If you don’t know what that means you dont need to do anything)
This file contains a bunch of commands that set up your environment (environment meaning the terminal you are using- the places it checks, the commands it can access, etc)
However just running this file would open a new terminal, run all the commands there to set it up, and then close it. But we want the set up to remain in the terminal we are using.

Hence, we have to use the command

```bash
source /opt/ros/jazzy/setup.bash
```

What this does is run all the commands in the terminal you are currently using and keeps it open so that you can use it with the environment setup.
(You can also set up the environment to be how you want it, and such that it doesn’t interfere with any other environment. You will learn about it later under workspaces)

Now if you run

```bash
ros2
```

You should see a bunch of options. ros2 works.

That’s all you need to do.
Every new terminal you open, you will have to set up the environment using the source command so that you can use ros2. 

If you don’t want to write that command every time, you can make the terminal run it automatically every time a new terminal opens, by adding it to the file 

```bash
~/.bashrc
```

This file contains commands that are run every time the terminal launches.
**Do not** mess with any of the commands here. Only add the source command to the **end of the file**. 
You can add it using a text editor or using this command

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
```

That’s the setup done. 

Ros is ready to use. Let’s make sure it works.

## Test

In a fresh terminal, source the environment if necessary and run

```bash
ros2 run demo_nodes_cpp talker
```

Open another terminal at the same time and (after sourcing) run

```bash
ros2 run demo_nodes_py listener
```

If it works, you will see the data being sent from the talker node recieved by the listener

You are done with your installation and setup. You can now use ROS.

