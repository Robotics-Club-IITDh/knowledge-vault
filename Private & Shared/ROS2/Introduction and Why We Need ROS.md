# What is ROS? Why use it?

Forget about ROS.
What is ROS?
I don’t know.

Let’s start from scratch.
Say you’ve made an RC car with an Arduino - controlled by bluetooth connected to your phone and controlling 2 motors through a driver.

You had fun. Yes, it’s a lovely robot. 
But you aren’t satisfied.
You want to make it autonomous based on vision.
Okay, we can add a Raspberry Pi with a camera module to handle it.
You still aren’t satisfied.
You want to make a 3D model of the whole surrounding area, and see that map on your computer.
Okay. We can add a LiDAR, IMU and a GPS module, and send the data to your laptop.

At least that’s the plan. You went online and bought all the components. You’re all ready to get going.
You plan and connect everything. It’s all looking nice and peachy and lovely and there are rainbows in the sky.

Then you sit down to do the software. 
God oh god oh god.
You didn’t think about this part.
The Raspberry Pi, Laptop and Arduino all need to send messages to each other.
But how?
USB? WiFi? I2C? Bluetooth?
Fine, it doesn’t matter. You pick something.
Then you need to decide data formats. 
How does the camera send images? How does the LiDAR send points?
Okay there should be readymade libraries for these. You use the ones you can.
For the ones you have to do yourself, you define some structures and follow those.
Okay, that works.

But wait.
The IMU sends you data at 200Hz
The Camera sends you data at 30Hz
The LiDAR sends data at 20Hz
How will you sync all of them?
Will you keep interrupting your programme to wait for data?
Okay, you figure that waiting is better than nothing at all.

But your IMU tells you that your robot has turned to the right.
Your LiDAR is sending you data about a wall that was in front of it before it turned right.
Now your 3d model thinks that theres a wall at the right side, where you can see there’s nothing.
How do you fix this? 
You add timestamps to your structures.
Okay, safe. That’s great.
Now it seems to be working fine.

Until the walls drift. That’s weird.
Then you remember. Every component has it’s own clock. And the speeds don’t match perfectly. 
So the LiDARs timestamp of 13:05:43:01 doesn’t mean the same as your Raspberry Pis time of 13:05:43:01.
So you decide to measure their offset, and how much their speed differs in order to correct them.
Somewhat working.
But let’s just restart your camera since you need to change something.

Welp. The whole system crashed.
Why is everything in one code?
Let’s seperate all of them.
Okay. 

Now how do all of them communicate?
You set up some networking. Hours and Hours but you finally get it to work.

FINALLY. IT WORKS FINALLY.

Thank god. Let’s just upgrade the LiDAR and make it a monster of a system.
Change out the LiDAR and… of course it broke.

You give up.
It just worked once. 
You didn’t even have time to make a logging system to see where the errors were.
You had so many more plans. But it’s all so much work.

Where did the rainbows go?
Why is the sky turning red?
Why are you sinking???
Is this…. 
Hell?!?

Yes. Thankfully, you don’t need to do all this.
Someone sat, suffered and did it for you. So that it would be easy.
That is what ROS is. It takes care of all of these :

- **Communication**
	You don’t decide USB vs WiFi vs Bluetooth at the software architecture level.
	You just say:
	“This program publishes camera images.”
	“This program listens to them.”
	ROS figures out how they find each other and talk.
- **Standard data formats**
	Camera images have a standard message.
	LiDAR point clouds have a standard message.
	IMU data has a standard message.
	You don’t invent structs.
	You don’t rewrite everything when hardware changes and sends differently formatted data.
- **Different data rates**
	Your IMU can scream at 200 Hz.
	Your camera can chill at 30 Hz.
	Your LiDAR can take its time.
	ROS does asynchronous messaging by default.
	Nothing blocks. Nothing waits stupidly.
- **Time and timestamps**
	Every message has a timestamp.
	ROS gives you a shared time system, even across computers.
	No more “13:05:43 means something different everywhere”.
- **Modularity**
	Camera crashes?
	Motor control keeps running.
	Mapping crashes?
	Sensors keep publishing.
	Everything is split into independent programs by design.
- **Replaceability**
	You change the LiDAR.
	As long as it publishes the same message type,
	nothing else cares.
	No rewrites. No domino effect.
- **Logging and replay**
	ROS **r**ecords everything the robot saw and did.
	Replay it later as if the robot is alive.
	Debug without the robot.
	Test algorithms in your room.
- **Visualization**
	Want to see the map, point clouds, camera feed, robot pose?
	It already exists.
	You don’t write a viewer from scratch.
- **Scaling**
	It’s all modular.
	You can add and remove elemts as you wish with minimum hassle.
	You can go from a single arduino to a complete destroy the world with AI bot by adding stuff.
- **Other**
	It also provides other functionalities such as coordinate transforms to sync the positions of your sensors, namespaces, parameter systems, etc. which are very useful but I have not mentioned them as going over every use case for ROS would take too long.
	Just mentioned some of the reasons that motivate us to use ROS

ROS is just the result of asking “How do I stop doing all this painful work every single time?”.

And that is why we use it. 

This is usually the part where I say how I love this thing that I’m using and it’s the best thing ever because it made life so much easier for me.

But I don’t love ROS. 
I don’t love it because I have never had to work without it, and fix all the things that would pop up otherwise. So I do not have as much of an appreciation for it… because it works too well.
Maybe me not loving it is justification for it’s usefulness.

So now hopefully, even though you don’t fully believe me or don’t understand ( I don’t either. There’s a lot going on underneath ROS ) you can appreciate that ROS has some use.
And hopefully that is motivation enough to get started with it.

P.S. : I don’t think there is a perfectly linear way to learn this. So if I ever mention something that I haven’t brought up before - like “A package follows a structure that tells colcon how to build your code and install software.” - then google what it means to “build your code” in ROS.  Ask chatgpt. That would be my recommended method of learning.


Go to [[ROS2 Index]] to see what to do next