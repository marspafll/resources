# Object-Oriented Programming

By default, in Python, the functions you define are at the top level. A function can take parameters as input and return a result. It can even return multiple results as a comma-separated list called a tuple. A standalone function is a very useful way to encapsulate a series of steps that act on some input, but it has no memory of what it's done before. We call this property statelessness. Statelessness is not necessarily a bad thing.

But there is another way of writing functions in Python too: as methods of a class. The reason for structuring your code this way is if you have a need to keep track of what's happened before, and it would be more convenient not to have to pass it in as input each time you call the function.

Before we go much farther, we need to draw a distinction between two concepts: class and instance. We don't mean class as in a body of students that meets regularly. Nor is it economic/social class (like "middle class"). A _class_ is a group of things sharing common attributes, like classification in biology. So Dog might be a class. It would have fields that can vary from dog to dog, but generally stay the same for a particular dog like fur_color, eye_color, size, methods like feed(), walk(), or train(), and fields that change based on what methods have been invoked before, like hungry or excited. An _instance_, on the other hand, is some *particular* dog, like your dog or your friend's dog. When you use a class in programming, you start by _instantiating_ it, which often involves providing it with some inputs that establish the characteristics of the instance.

For example, if I were to instantiate my dogs, it might look like this:

```python
lucy = Dog(colors=[Color.BLACK, Color.BROWN], size=Size.MEDIUM, energy_level=EnergyLevel.MEDIUM, friendliness_toward_people=Friendliness.HIGH, friendliness_toward_dogs=Friendliness.LOW, age=6)
ubu = Dog(colors=[Color.BLACK], size=Size.SMALL, energy_level=EnergyLevel.ABSURDLY_HIGH, friendliness_toward_people=Friendliness.HIGH, friendliness_toward_dogs=Friendliness.MEDIUM, age=0.3)
```

