We can see that regardless of where the rod is - 0 degrees or 89 degrees - the voltage and hence torque provided is the same. 
But we do not need the same amount of torque to move 90 degrees as we do to move a single degree. 
With this insight in mind, we can then propose a better solution - one that will be a foundational block in control systems - The proportionality constant Kp.
Let there be a constant named Kp, which is a constant of proportionality between the difference of the desired and current angle , and the voltage output.
ie Kp is the constant of proportionality between the "error" and output torque.
Putting it into an equation, we get
V_out = Kp * (Desired Angle - Current Angle)

This error will be our lifeline. It will tell us by how much we are off. 
On this we can base our calculations.

Let us put this into the code with a sandbox function like so 
```python
def compute_motor_voltage(current_angle, time_elapsed):
    error = TARGET_ANGLE - current_angle
    Kp = 0.4
    voltage = Kp * error
    return voltage
```

Finally.. did that work?
![[Tutorial_1_Image_5.png]]
Nope it did not.
It's pretty much the same as the last graph. 
It's just doing simple harmonic motion, and in our case there's no friction to damp it either, so it just oscillates.
Well.. yeah. But let me tell you- this is the right path. 
We just need to improve it , bit by bit, and we will get what we want.

Let us start with a problem. 
We have a DC motor. Geared, low rpm, high rpm - doesn't matter.
There's a shaft connected to this motor and a rod which spins with it like so 
	![[Tutorial_1_Image_1.png|300]]
Now we have to rotate it to a certain position. Let us say that that the rod (looking at it straight down) starts at 0 degrees, and needs to move to the target 90 degrees
	![[Tutorial_1_Image_2.png|300]]

This is what we know about the system :
- We can provide any voltage in the range of +12 to -12 to the motor, which corresponds to torque in the Anticlockwise and Clockwise direction respectively. The torque provided is proportional to the voltage provided.
- We can read/see the angle of the rod clearly. This information is available to us as data.
- We know how torque is related to angular acceleration.

Right now we are ignoring gravity, friction and other such forces.
With this information, a few systems come to mind with which we can get it to 90 degrees.
Think of your own solutions for a minute before moving forward.

Firstly, what comes to my mind is this - If a voltage corresponds to acceleration in some direction, then can I not just power the motor for a seconds or so and have it move to the position?
Let us give that a try with this code which simulates everything and gives us a nice graph


```python

import numpy as np 
import matplotlib.pyplot as plt
#Physical parameters
DT = 0.001          # Simulation time step (1 millisecond)
SIM_TIME = 3.0      # Total simulation time in seconds
INERTIA = 0.3       # Rotational inertia of the heavy joint
TARGET_ANGLE = 90.0 # Target setpoint in degrees
#Sandbox function
def compute_motor_voltage(current_angle, time_elapsed): 
	if time_elapsed < 0.5: 
		voltage = 12.0 # Kick forward 
	else: 
		voltage = 0.0 # Turn off power and hope it stays
	return voltage

#Simulation
time_steps = int(SIM_TIME / DT)
time_history = np.zeros(time_steps)
angle_history = np.zeros(time_steps)
voltage_history = np.zeros(time_steps)
angle = 0.0
velocity = 0.0

for i in range(time_steps): 
	t = i * DT
	voltage = compute_motor_voltage(angle, t)
	voltage = np.clip(voltage, -12.0, 12.0) # Physical battery limit 
	# Physics: 
	torque = voltage * 3.0 
	acceleration = torque / INERTIA 
	# Numerical integration
	velocity += acceleration * DT 
	angle += velocity * DT 
	# Record data 
	time_history[i] = t 
	angle_history[i] = angle 
	voltage_history[i] = voltage
	
#Visualisation
plt.figure(figsize=(10, 5))
plt.plot(time_history, angle_history, label="Robot Joint Position", color="red", lw=2)
plt.axhline(y=TARGET_ANGLE, color="black", linestyle="--", label="Target Goal (90°)")
plt.title("Session 1: Testing Intuitive Control Laws")
plt.xlabel("Time (seconds)")
plt.ylabel("Angle (degrees)")
plt.legend(loc="upper right")
plt.grid(True)
plt.show()
```

Well did that work? 
![[Tutorial_1_Image_3.png|659]]
Ehh.. It reached 90 degrees at some point, but the joint kept moving even past it, because we had no stopping acceleration.
Okay so we need to improve this or go a different route. 
I can think of a version where this works by setting the timing for starting and stopping acceleration perfectly. 
But even then, if it starts in a different position or encounters some resistance, it would screw the whole thing up.

Let's try a different method. 
A method where we check whether the rod is at the angle or not, and not just do it blindly. 
Okay then this seems logical - 
If the rod is at an angle <90, then it has ACW torque
If it is at an angle >90, it has CW torque
Let us test if this works by replacing the sandbox function with this
```python
def compute_motor_voltage(current_angle, time_elapsed): 
    if current_angle < TARGET_ANGLE:
        voltage = 12.0
    else:
         voltage = -12.0
    return voltage
```
And if we run it now and increase the simulation time, does that work?

![[Tutorial_1_Image_4.png|686]]
Eh... again. 
Now it at least seems to have some direction of where its supposed to be, oscillating about that point but the oscillations are way too big to serve any purpose where we would need 90 degrees.
Take a minute to think about what the problem could be, and then move on.

So, how do we improve this?
What was the problem with the last trial?

It was that as the rod was approaching the desired angle - 90 degrees, it picked up speed.
And with its speed and momentum it went past that point. When the code then saw an error, it tried to correct it, send the rod flying back and the same thing happened. Then again and again and again and it kept oscillating.
How do we fix this?

We can fix it by reducing our speed when it gets too high.
When the speed changes too fast, we can add a "damping constant" of sorts. 
So let us take the time derivative of the Angular position, and add it to the desired output. When the error is decreasing really fast, the derivative would be negative and reduce the output.
We come to the formula
$$\text{Output} = (+- 12V ) - K_d\left(\frac{d(Theta)}{dt}\right)$$
Where Kd is the relevant proportionality constant.

Let us simulate this by making these changes/additions to the code
```python
prev_angle = None
def compute_motor_voltage(current_angle, time_elapsed):
    global prev_angle
    Kd = 0.8
    error = TARGET_ANGLE - current_angle
    angle_derivative = (current_angle - prev_angle) / DT 
    prev_angle = current_angle
    d_voltage = -Kd * angle_derivative
    p_voltage = 12*(error)/abs(error)
    voltage = d_voltage + p_voltage
    return voltage
```
Okay.. did that work?

![[Tutorial_1_Image_6.png]]

Wow. Wonderful. Whatta beauty.
Yes, it works.

However we can see that it takes over 6 seconds to get close to the desired output, and there are some small oscillations towards the end.
Let's play with some values and see how the response changes.
Take some time to tweak the values of Kd and the multiplier of p_voltage (currently set to the max of 12). Try it.

We see a general trend :
The greater the value of Kd, the longer it takes to get to the desired output and the faster it settles down at the desired output
The greater the value of the p_voltage multiplier, the faster it gets to the desired output but the longer it takes to settle down.

Here is a run with them set to 0.2 and 8
![[Tutorial_1_Image_7.png]]
Pretty decent run. 
However here we see the oscillations more.
Is there any way we can minimize them? Take a minute to think about why it oscillates in the first place and how we could fix it.

We can see that we need the rod to move fast when its far away from the target angle and settle down faster when it is closer to the target angle.
There are a couple of ways to do this. As we can see from the trends we noted earlier, we can change Kd and p_voltages output to match what we want - reach faster or settle faster.
What if we had a system where we could dynamically choose their value based on what we want?
This is what I propose : We measure the error (Target angle - Current angle) and we scale p_voltages output proportionally to that. 
That way, when the error is large, it moves to the target fast and when its smaller the oscillations die quickly.
Let us try implementing this using the error and a new constant Kp to control the output with this code :

```python
prev_angle = None
def compute_motor_voltage(current_angle, time_elapsed):
    global prev_angle
    if prev_angle is None:
        prev_angle = current_angle
    Kd = 2.5
    Kp = 6
    error = TARGET_ANGLE - current_angle
    angle_derivative = (current_angle - prev_angle) / DT 
    prev_angle = current_angle
    d_voltage = -Kd * angle_derivative
    p_voltage = Kp * error
    voltage = d_voltage + p_voltage
    return voltage
```

![[Tutorial_1_Image_8.png]]
Woah okay. That seems to be miles better.

It works. It's great. We are so smart fr.

That's all for tutorial 1.
We have rediscovered the P and the D in 'PID'.
Next tutorial will be going over ...

Homework : 
- Play around with the values of Kp and Kd. What do you notice? 
- Is there merit to the method we were using before Kp?
- This is an ideal case - No friction, no gravity, no power consumption. In the real world what problems might we face with this simplistic system?