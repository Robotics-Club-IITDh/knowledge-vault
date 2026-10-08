## Basic Structure

Ros basically has - I would say - 3 main levels

- **A workspace** **-**
	A workspace’s main function is to keep track of all the libraries/packages that your robot would need, and make sure that they are set up.
	You say that you are “working in a workspace” when you source all the software specified by a workspace (source meaning keep it prepared and accessible to use), and are working in a specific folder. 
	What this means will become clear when you start building something.
- **A package -** 
	A “package” in ros is a directory with logically consistent nodes that work together to do one job.
	For eg you might have a package called read_data which has nodes read_lidar, read_cam and read_encoder.
	It also follows a structure that tells colcon how to build your code and install software.
	It contains the source code, list of dependencies, etc.
- **A node -** 
	A node is a single process/programme that runs in ros.
	A node can only be defined when your programme is running, though you can say you are making a node/ coding it when you write the code that it will run during runtime.

Imagine you have just recieved a beyblade in a box. And you have to build it on your desk.
You can think of your workspace as your table and tools - the screwdriver, wrench, etc that you need to make it work.
You can think of a package as the box. It has all the parts of the beyblades inside and the instruction manual on how to put it together, using the tools you have.
And you can think of a node as a singular part of a beyblade, that does something specific.

These 3 are all we need to know for now.
Later on you will work with multiple of each, and there is a lot under the hood that we don’t see right now, but you can configure.
So overall - the “3 main levels” explanation may not hold in the long run. 
But for right now, it does. And this is a basic heirarchy that is used everywhere.

Now you know some theory. But how to actually do it?
Analogies of beyblades and desks and boxes are great, but what does that mean in your computer?
Let’s take a look by building your first package. Starting with :

## Setting up a Workspace

A workspace in ros is basically a folder where you will work.
It contains your code,list of dependencies, etc.
However, ros is quite complex internally. 
So for it to work like we want it to, we have to follow it’s structure of a workspace.

To create a workspace, let us first make a folder.
I am making a workspace folder with the location

```bash
~/ros/ros2_ws
```

You can make it anywhere you wish but you must then be careful of the relative file locations as it will not be a direct comparision to mine.

Lets create it with commands

```bash
cd  
mkdir ros
cd ros
mkdir ros2_ws
cd ros2_ws
```

You have created the folder and are working in it.

Now we must make a source folder inside this, which will store all the programmes you write.

```bash
mkdir src
```

Any programme you write will exist in this src folder.

To complete the workspace’s structure, we need to run another command when in the ros2_ws directory.

```bash
colcon build
```

This command handles everything else.
It is pre-defined in the ros-dev-tools package we installed earlier.
colcon stands for “collective construction”.
It checks your src folder for all your programmes, custom message types, etc.
And it creates the actual executable files.

If you check the ros2_ws folder now with the ls command, you will see

```bash
~/ros/ros2_ws$ ls

build  install  log  src
```

There are 3 folders other than the src we created.
These folders have been automatically created when colcon build was run.

- **The build folder** contains compiler internals - cache, object files, headers, etc
- **The install folder** contains all the actual files that ros runs upon execution.
It is the resul of compiling your src folder.
Even now - with nothing in the src folder - you will see a bunch of files here.
Including a setup.bash file (familiar?)
Yes, we must source this file as well. Like previously either every time we open a new terminal or we can add it to the ~/.bashrc file.
This file contains all the commands it needs to set up the environment. You can also stack/source multiple setup files from different workspaces. You will see about it when you make multiple workspaces.
- **The log folder** contains logs of how your programme is running, any errors that pop up etc.
You can refer to this to check what went wrong if some problem arises.

All the work you do will be in the **src** folder.
You need not touch the others.

Let us source the setup file

```bash
source ./install/setup.bash
```

To add it to your terminal startup file (optional), 

```bash
echo "source ~/ros/ros2_ws/install/setup.bash" >> ~/.bashrc
```

Your workspace is set up now.
We can start using it.

## Creating a package

Let us create a package in our src folder in the workspace we created earlier.

Navigate to your src folder in terminal, and type the following command

```bash
ros2 pkg create my_py_pkg --build-type ament_python --dependencies rclpy
```

Let us see what this command is made of.

It starts with 

```bash
ros2 pkg create my_py_pkg
```

this works like
ros2 commands → commands for package → create  package → name it my_py_pkg
You can choose another name if you like.

Then there is 

```bash
--build-type ament_python
```

Ament is ros2’s build and package indexing system.
You will see more of it later.
For now, just know that this command tells ros to build the package (that includes python files) in a way that ros can handle and manage later.
I myself am not completely sure of what exactly it does  :’)  Will update this doc when I learn.

Then

```bash
--dependencies rclpy
```

The dependencies command specifies dependencies of the package your creating on other package/libraries. Basically what tools it will use.
You can always add dependencies later but right now I have added *rclpy -* The library which enables ros to use python files. (ie The python library which lets your code communicate with and convert to what ros wants)

When you execute the whole command to create your package, you will see many files being created. 
You might also see a warning which says you have not chosen a liscence. You can ignore that. That’s only important if you want to publish your work and make sure no one steals it (or something like that).

Okay your package should have been created. 
Let’s take a look in VSCode at whats inside
Note that python packages do not have the same folder structure as cpp packages.

```bash
tushar@victy:~/ros/ros2_ws/src$ code .
```

tushar@victy is my user in my laptop :) 
code . is the command to open vscode with the current folder in the explorer

Checking in the folder, we see

![image.png](Getting%20Started%20-%20Workspaces%20and%20Packages/image.png)

The .vscode folder is just something for vscode. Not important.

Other than that, in our package named my_py_pkg,
We see that there is a folder with the same name. This will contain your code.

We see also a resource folder. We don’t need to pay attention to it since ros will handle that by itself, but what it does is essentialy store info on dependencies.

There is a test folder which we also don’t need to worry about. But you can guess what it’s for by the name.

Then there is a package.xml file. Take a look inside and you will see more formalities. Dependencies (you can also add more here), liscence, name, version.
Not important that we learn about it right now, though it is important that it exists.

We also see a setup.cfg and setup.py, which we will come back to once we create a node.

That’s the outline of your package.
Navigate back to the ros2 workspace folder (not the src folder) and let’s build our package with

```jsx
colcon build
```

This makes your code a functioning executable file.
However, we have no code written down right now  so there’s no point.
But it does still build.

You have successfully made and built a package.

That’s it for this section
Move ahead to making your [[First Node]]
Or go to [[ROS2 Index]] to see what to do next
