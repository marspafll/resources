# Control Systems

Control Engineering is an engineering discipline that uses sensors, control devices that interpret sensor readings and make decisions, and actuators to control and stabalize dynamic processes.

## Bang-bang controls

One of the simplest kind of controllers you're probably familiar with is the thermostat that keeps your house at a comfortable temperature. It's called a bang-bang controller because it's only able to switch abpruptly between two states. In the case of your furnace, those two states are on and off. To keep your furnace from going on and off every 5 minutes, the thermostat has a setback, meaning that if you set the desired temperature (called the **setpoint** in controllers) at 69°F, the furnace might come on at 67 and turn off at 71. That way, the furnace has some time to rest while the temperature in the house drops due to natural heat loss.

In robotics, you might remember when we first started learning about line followers. One of the simplest kinds of line follower you can have is one that turns the robot left if the sensor reading is darker than the setpoint, or turns it right if it's brighter than the setpoint. A robot with a bang-bang line follower is very wiggly.

The next step up in line follower complexity was to give it a third state: drive straight if the reading is close to the setpoint, but still turn left if it gets too dark or right if it gets too bright. This is a bit like the role that the setback plays in the thermostat. It creates a range of sensor readings around the setpoint where corrective action does not need to be taken.

## Proportional control

In the three-state line follower above, you could imagine it might be even better if it could be divided into five states: hard left, gentle left, straight, gentle right, hard right. And if 5 is better, why not 10, 20 or 50 states? The problem is that as you add states, the code to test whether the sensor reading is in the associated state's range and act on it gets quite cumbersome. Fortunately, there's a simpler way. A [linear function](./math/linear-functions) lets us easily express an infinite number of intermediate states between turning hard left and hard right, and it lets us do it with just a multiplication and a subtraction.

```python
def proportional_line_follower(sensor_reading, setpoint, proportional_gain):
  error = setpoint - sensor_reading
  steering_value = error * proportional_gain
  return steering_value
```

The setpoint is found by getting a sensor reading for white, a sensor reading for black, adding those values together and dividing by two (in other words, calculating the arithmetic mean). That way, an error value of 0 means the sensor is half-on/half-off the line, right where we want it, and the robot will go straight.

The proportional gain (K<sub>p</sub>) is found by trial and error. If the robot is turning the opposite direction it should, subtract the gain from 0. If the robot doesn't react strongly enough to wandering off the line, increase the magnitude of the gain; it it's too wiggly, decrease the magnitude.

## PID Control

You can get pretty far in LEGO robotics only knowing about how to implement proportional control. But there are some enhancements to proportional control that you should know about because the PyBricks package we use to program the robots has some internal PID controllers. Functions like DriveBase.straight(distance), DriveBase.turn(angle) and Motor.run_target(angle) use the internal PID controller, so it's a good idea to have some familiarity with how they work.

PID stands for proportional, integral, derivative. You can think of proportional control as being about reacting to the present error, integral as reacting to past error, and derivative as reacting to future error.

We already covered proportional above, we'll look at the other two next.

### Derivative

Even with the infinite variability of proportional control, there's a tradeoff between not following the line well enough (falling off of it on tight turns) and being too wiggly. A proportional control is sort of like a spring: the farther it gets pulled from its resting position, the harder it pulls back. When that force gets large enough, it can end up pulling back beyond where it started bouncing back and forth. That bouncing is called **oscillation**.

To compensate for this, where springs are used as a means of control, they're often coupled with dampers. The shocks on a car are a good example, as are the door closers in commercial buildings. Springs definitely make for a better ride in a car than no springs, but they're still pretty bouncy. By adding a fluid-filled cylinder to the spring, the speed that the wheels travel up and down in response to bumps can be limited so that the spring is less likely to overshoot and any oscilations settle down sooner. When a car's shocks are worn out, pushing down hard on one corner of a car (while it's parked) will cause the car to keep bouncing longer than it should. Good shocks stop the oscillations very quickly.

The term **derivative** comes from calculus. Just as lines have slopes, curves can have slopes too. Unlike lines' constant slopes, curves' slopes change as you move along the curve. But you don't really need to know much about calculus to impement a controller that uses derivative control. All you need to do is find the difference between the current error and the previous error, or the current sensor reading and the previous sensor reading; it's the same difference. Then you multiply that difference by the derivative gain (K<sub>d</sub>) and add it to the result of the proportional calculation. What does that have to do with the idea of a slope? Well, if you were to graph the error values of a controller over time (meaning time on the x-axis, and error on the y-axis), you would see the places where there is a large difference between the current reading and the previous reading as having a steep slope. Time so often ends up being on the x-axis that the concept of slope on a graph is closely associated with **rate of change** of a variable.

The reason we're interested in the steepness of the slope is it lets us predict the future. A slope points in the direction that a trend is headed. If the error is rapidly heading toward zero, we want to start reducing the steering input before it crosses zero. Crossing zero error is called **overshoot**.

### Integral

The integral component of PID controllers isn't usually very important for line followers, though it does come into play when turning a motor encoder to a specific target angle. As the error gets closer to zero, the amount of force coming from proportional control decreases to a point where it may never actually make it all the way to the target. Integral control is there to address error that persists over time. It particularly comes into play when there's a constant force, like gravity, acting on the system. Imagine a motor trying to keep an arm held out horizontally. Gravity keeps wanting to pull the arm down, and the motor has to apply an opposite force to get it to where it's supposed to be and keep it there. Gravity isn't the only force that can persistently act against a controlled system. Heat loss when you're trying to maintain a stable temperature is another example. Line followers that use proportional and derivative control with little if any integral are unusual when it comes to controllers. Most controllers end up using primarily proportional and integral control.

The term **integral**, once again, comes from calculus. It's a way of finding an area between a curve and the x-axis. But what's the point of finding an area like that? The EV3 gyro sensors are a good example. Unlike real gyroscopes with spinning wheels, they don't directly measure the direction they're pointed. What they directly measure is their turn rate. To figure out what direction they're pointed, they need to take frequent samples of their turn rate and add them up to see how far they've turned in total. It's not as accurate as a real gyroscope, but it is a lot cheaper. Likewise, if your car's odometer were broken, you could estimate how far you'd gone if you had a data set showing your speed every 5 seconds.

  As an aside, integrals and derivatives are linked in a way that isn't immediately obvious: they're more or less inverse operations of one another. If you have a data set with samples of odometer readings, you can work out the speed that the car was going by looking at the slope because speed is the rate of change of distance. And if you had the speeds sampled, you could look at _their_ slopes and work out the acceleration because acceleration is the rate of change of speed. As discussed above, you can work out the distance travelled (though not the total odometer reading because you don't know its starting point) from samples of the speedometer by adding up the area under the curve. With an accelerometer you can work out the speed if you know the starting point. This relationship between integrals and derivatives is called the Fundamental Theorem of Calculus.

So what does adding up the area under the curve look like in robotics code? Just as with finding the derivative, it's much simpler if we assume that each loop iteration takes the same amount of time. For an integral, all you need to do is keep a running total of the past error values. Remember, error can be positive or negative, so error accumulated in one direction can be cancelled out by error in the other direction. The integral component's contribution to the PID's output is found by taking this running sum of the error values and multiplying it by a gain factor (K<sub>i</sub>).

### Tuning

The really tricky part of using a PID is tuning it properly. The most accurate tuning methods depend on having a mathematical model of the system and how it responds to inputs. But that approach isn't always possible, especially when you don't know college-level math yet. Fortunately, there are also trial-and-error techniques for tuning PIDs too. Here's the basic process:

1. Zero out the integral and derivative gains, effectively turning off their contribution to the controller. We're going to start with tuning the proportional gain alone.
2. Attempt to find a proportional gain that responds strongly enough, but not too strongly. In the case of a line follower, you want it to follow the line through the turns, even if it oscillates along the line. It's too high if the oscillations are so strong that the robot ends up falling off the line. It maybe be necessary to slow the robot down to achieve adequate adherence to the line. After a rough tuning is found, it might be possible to turn up the speed later.
3. Start out the derivative gain at about 10-20X the proportional gain. The difference between subsequent error readings is typically much smaller than the difference between an error reading and zero, which is what proportional is concerned with, so that's why the K<sub>d</sub> ends up being so much bigger than the K<sub>p</sub>. If increasing the derivative gain actually makes the robot _less_ stable, you may need to try flipping the sign. Adding in derivative control should noticeably smooth out the osciallations compared with when proportional control alone was used. You can tell that derivative gain is set too high if you start to see a lot of **chatter**, meaning that the robot is shaking at higher frequency than the proportional osciallations.
4. Try increasing the speed at which the robot follows the line. Its following performance will certainly get worse when you do that, but you can try tuning the P and D gains to smoothe it out. There is a speed beyond which a robot cannot reliably follow a line because the error sample rate per distance travelled gets too low.
5. For line followers, integral control is usually not used. But if you're trying to set up a controller to have a motor move part of the robot to a specific spot, integral becomes very important because there's often gravity acting against the desired action. Because the sum of errors can be so much larger than any particular error, the integral gain is usually set at 1/10 - 1/20 of the proportional gain.

### External Resources

1. This video shows [an FLL team tuning](https://www.youtube.com/watch?v=Z-RWZTJI8sU) an EV3 robot line follower
2. This is the [fastest LEGO line following robot](https://www.youtube.com/watch?v=9YLxIUn7avY) I've ever seen. The video has almost no details about how he did it, but this is useful to understand what's possible.
3. This video shows a [college lecture on tuning a PID control](https://www.youtube.com/watch?v=uXnDwojRb1g).
4. This is a simple demonstration of [tuning a PID with a robot arm](https://www.youtube.com/watch?v=fusr9eTceEo). It does a great job of showing what the different terms do.
5. This [StackExchange Q&A](https://robotics.stackexchange.com/questions/167/what-are-good-strategies-for-tuning-pid-loops) discusses mothods for tuning a PID.
6. This [forum discussion](https://forum.pololu.com/t/questions-about-pid-actually-pd-tuning/3263) talks about how to tune a PD line controller. They're working with robots that aren't EV3s, but most of the same techniques still apply.
7. If you have a good way of logging error and output values on each iteration of a loop, you might be able to use [this tool](https://pidtuner.com/) to help you tune a controller.
8. This is an [academic paper](https://www.researchgate.net/publication/283347335_Optimization_of_PID_Control_for_High_Speed_Line_Tracking_Robots) about going beyond PIDs to handle special cases that arise with line followers. The math formulas are advanced, but the implementation of the ideas need not be that complicated.
  a. Start by tuning the proportional gain of a PID controller
  b. Add derivative tuning to dampen the oscillations
  c. Add in a little bit of integral control so that persistent error doesn't cause problems in the P and D control
  d. Manage integral windup by only looking at the sum of the 10 most recent error values for the integral control
  e. Use the sum of the absolute values of the 10 most recent error values and an additional gain factor to slow down the forward drive speed in turns
  f. Handle the special case when the line is lost completely by stopping all forward drive and rotating in place until the line is reacquired.
