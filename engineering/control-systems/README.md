# Control Systems

Control Engineering is an engineering discipline that uses sensors, control devices that interpret sensor readings and make decisions, and actuators to control and stabalize dynamic processes.

## Bang-bang controls

One of the simplest kind of controllers you're probably familiar with is the thermostat that keeps your house at a comfortable temperature. It's called a bang-bang controller because it's only able to switch abpruptly between two states. In the case of your furnace, those two states are on and off. To keep your furnace from going on and off every 5 minutes, the thermostat has a setback, meaning that if you set the desired temperature (called the **setpoint** in controllers) at 69°F, the furnace might come on at 67 and turn off at 71. That way, the furnace has some time to rest while the temperature in the house drops due to natural heat loss.

In robotics, you might remember when we first started learning about line followers. One of the simplest kinds of line follower you can have is one that turns the robot left if the sensor reading is darker than the setpoint, or turns it right if it's brighter than the setpoint. A robot with a bang-bang line follower is very wiggly.

The next step up in line follower complexity was to give it a third state: drive straight if the reading is close to the setpoint, but still turn left if it gets too dark or right if it gets too bright. This is a bit like the role that the setback plays in the thermostat. It creates a range of sensor readings around the setpoint where corrective action does not need to be taken.

### Proportional control

In the three-state line follower above, you could imagine it might be even better if it could be divided into five states: hard left, gentle left, straight, gentle right, hard right. And if 5 is better, why not 10, 20 or 50 states? The problem is that as you add states, the code to test whether the sensor reading is in the associated state's range and act on it gets quite cumbersome. Fortunately, there's a simpler way. A [linear function](./math/linear-functions) lets us easily express an infinite number of intermediate states between turning hard left and hard right, and it lets us do it with just a multiplication and a subtraction.

```python
def proportional_line_follower(sensor_reading, setpoint, proportional_gain):
  error = setpoint - sensor_reading
  steering_value = error * proportional_gain
  return steering_value
```

The setpoint is found by getting a sensor reading for white, a sensor reading for black, adding those values together and dividing by two (in other words, calculating the arithmetic mean). That way, an error value of 0 means the sensor is half-on/half-off the line, right where we want it, and the robot will go straight.

The proportional gain is found by trial and error. If the robot is turning the opposite direction it should, subtract the gain from 0. If the robot doesn't react strongly enough to wandering off the line, increase the magnitude of the gain; it it's too wiggly, decrease the magnitude.

### Derivative

Even with this infinite variability, there's a tradeoff between not following the line well enough and being too wiggly. Another term for that wiggliness is overshoot. A proportional control is sort of like a spring: the farther it gets pulled from its resting position, the harder it pulls back. When that force gets large enough, it can end up pulling back beyond where it started.

To compensate for this, where springs are used, they're often coupled with dampers. The shocks on a car are a good example. Springs definitely make for a better ride than no springs, but they're still pretty bouncy. By adding a fluid-filled cylinder to the spring, like struts, the speed of travel can be limited so that the spring is less likely to overshoot and any any oscilations settle down sooner.

The term derivative comes from calculus. Just as lines have slopes, curves can have slopes too. Unlike lines, curves' slopes are not constant; curves' slopes change as you move along the curve. The derivative of a curving function is itself another function that gives you slope at each point along the curve. But you don't really need to know much about calculus to impement a controller that uses derivative control. All you need to do is find the differenct between the current error and the previous error. If the difference is large, it means that the steering correction from the proportional control changed rapidly too. 