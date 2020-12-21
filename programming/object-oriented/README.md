# Object-Oriented Programming

By default, in Python, the functions you define are at the top level. A function can take parameters as input and return a result. It can even return multiple results as a comma-separated list called a tuple. A standalone function is a very useful way to encapsulate a series of steps that act on some input, but it has no memory of what it's done before. We call this property statelessness. Statelessness is not necessarily a bad thing.

But there is another way of writing functions in Python too: as methods of a class. The reason for structuring your code this way is if you have a need to keep track of what's happened before, and it would be more convenient not to have to pass it in as input each time you call the function.

Before we go much farther, we need to draw a distinction between two concepts: class and instance. We don't mean class as in a body of students that meets regularly. Nor is it economic/social class (like "middle class"). A **class** is a group of things sharing common attributes, like classification in biology. So Dog might be a class. It would have **fields** that can vary from dog to dog, but generally stay the same for a particular dog like fur_color, eye_color, size, **methods** like feed(), walk(), pet(), or train(), and fields that change based on what methods have been invoked before, like hungry or excited. An **instance**, on the other hand, is some _particular_ dog, like your dog or your friend's dog. When you use a class in programming, you start by **instantiating** it, which often involves providing it with some inputs that establish the characteristics of the instance.

For example, if I were to instantiate my dogs, it might look like this:

```python
lucy = Dog(colors=[Color.BLACK, Color.BROWN], size=Size.MEDIUM, energy_level=EnergyLevel.MEDIUM, friendliness_toward_people=Friendliness.HIGH, friendliness_toward_dogs=Friendliness.LOW, age=6)
ubu = Dog(colors=[Color.BLACK], size=Size.SMALL, energy_level=EnergyLevel.ABSURDLY_HIGH, friendliness_toward_people=Friendliness.HIGH, friendliness_toward_dogs=Friendliness.MEDIUM, age=0.3)
```

Once the objects are instantiated, I can interact with them by invoking methods and inspecting the values of fields

```python
if lucy.hungry && !lucy.is_barking:
  lucy.feed()
```

We end up using classes quite a lot in EV3 micropython programming. DriveBase is a great example of a class that we use in the pybricks library. The fields that get assigned at instantiation, but don't change, are things like axle_track and wheel_diameter. If you call the straight(distance) method, it not only drives straight for the distance you specify by using wheel_diameter to calculate the number of times to turn the motors attached to the wheels; it also keeps track of how far it's driven in total so that you can retrieve that by calling distance() after you've made any number of moves.

But we don't have to confine ourselves to only consuming classes written by others; we can produce our own as a convenient way of organizing our code too. Forklift is a great example. Before we can reliably set it to a desired height, it needs to establish its upper and lower limits of travel. Class fields are a great place to remember what those limits are so that we only need to calibrate the forklift once.

A word of caution: sometimes people get carried away with making everything object-oriented, even when it's not the best tool for the job. This is [one of the criticisms](http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html) of the Java programming language, which forces virtually everything to be part of a class. If keeping track of state isn't important to solving a problem, classes will just get in your way.
