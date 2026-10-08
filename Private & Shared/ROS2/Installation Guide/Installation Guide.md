ROS2 has multiple versions made for different Linux versions, compatible with different simulation software versions.

As of 2026 -

The latest LTS release (meaning the developers update and fix bugs for a longer time - LTS : Long Term Support) is ROS2 **Jazzy**, which runs on **Ubuntu 24.04**

Another popular release is ROS2 **Humble**, which runs on **Ubuntu 22.04**

This is a guide on how to install and use ROS2.
I will be going through how to do this on **Ubuntu 24.04 Noble,** using **ROS2** **Jazzy ,** as those are the latest LTS versions.
You may choose to go with another version such as Ubuntu 22.04 Jammy with ROS2 Humble (this one has fewer bugs, as it has been out and supported for longer)
The guide will still be roughly the same, though I recommend you do some research of your own either way.

First, before we install ROS, we must have Ubuntu, and learn how to use it.

## **Ubuntu installation, basics, and not so basics**

Here is a small tutorial on installing it. Skip it if you have Ubuntu already.

[Ubuntu Installation Guide](Ubuntu%20Installation%20Guide.md)

Okay you have a clean Ubuntu installation ready.

Even for people who have been on Ubuntu for a while, here are some things you should know when using Ubuntu : 

[Useful things](Useful%20things.md)

Now let’s get back on track. Let’s install ROS2.

Now lets install ROS2.

## ROS2 installation

You can find a bunch of tutorials anywhere. 
And the official ros docs also work. 
Here is the document for installing ros jazzy
[https://docs.ros.org/en/jazzy/index.html](https://docs.ros.org/en/jazzy/index.html)

But honestly, that seems messy to me, and I dont think my installation had ever gone smoothly when I was starting out.
So here is a tutorial :

[ROS2 Installation Guide](ROS2%20Installation%20Guide.md)

Ros is now installed. However, to code in ROS we have to use some language.
Primarily Python or C++. (Which we have already installed along with ROS, but better safe than sorry)
I will be writing everything in python as that’s easier to understand and follow when first starting out, though both have their pros and cons. (Python has only cons actually but its easier.) (Also I don’t know cpp as well)

## Python / C++ instllation

To install the **Python** interpreter **:**
Open your terminal and type

```bash
sudo apt update
sudo apt install python3
sudo apt install python3-pip python3-dev
sudo apt update && sudo apt full-upgrade
```

Python should now be installed.
Confirm using 

```bash
python3 --version
```

To install a **C++** compiler :

```bash
sudo apt update
sudo apt install build-essential
```

g++ (common compiler for cpp) should be installed.
Confirm using

```bash
g++ --version
```

I will be coding in VScode.
The choice is yours. You can code in the text editor, nano or vim. Whatever you wish.
VSCode is easy to use and has a nice GUI, so I will be using it.

## VSCode installation

Easiest to download it from the app center.
Search for App center in your search bar, 
Then VSCode in app centers search bar.
Download the one with the blue logo which just says “code”.

Now we are done with all are installations and are ready to move ahead.

Go to [[ROS2 Index]] to see what to do next