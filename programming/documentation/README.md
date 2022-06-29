# Mars Robotics FLL

# Documenting Your Code

Unless you have a trivially simple program, the code you write isn't nearly as useful in the long term as it could be if you don't document it. Documentation is there for any programmer who comes after you so that what you did can be understood. Remember, the programmer who comes after you to maintain the code will often be YOU. Code you wrote a year ago can be just as baffling as code written by somebody else.

## Kinds of documentation

Documentation for code can come in several forms. Let's look at a few that are relevant to robotics programming in Python

### External documents

The most formal way of documenting your programs is to write a document in a word processor, text editor, or a web page that describes, generally in full sentences and paragraphs (i.e. prose), how to use your program. One common format nowadays is Markdown files, like the one you're reading now. This will often be the first thing that someone wanting to get started with using your program reads. If you're writing programs for yourself, to run on a robot, external documentation might not be that important because the reason for the program and how to use it would be obvious. But almost any software intended for use by others should have at least some explantion of its rationale and how to get started using it.

### Python Docstrings

Python has a feature where you can write documentation and keep it right with the code it describes. What's more, IDEs like Visual Studio Code (with the right extensions like PyLance) are able to show docstrings to consumers of your code while they're coding. You've probably see this before from libraries like PyBricks that we use in robotics programming. You might not have known that we can easily write help text in our own code that shows up in the same way. Another good thing about docstrings is that tools can take them and convert them into an html manual.

Docstrings are created by writing a triple-double quoted string directly below a function definition line.

```python
def add_numbers(a, b):
  """Finds the sum of the numbers a and b"""
  return a + b
```

This is a silly example. It's probably not worth breaking out a function this simple, let alone documenting it.

### Comments

Comments are only read by people who are reading your source code. In Python, a comment starts with a #. Comments are important hints for other programmers who are trying to understand why you did what you did. Programmers with a little bit of experience tend to over-comment their code (over-compensating for the tendency of absolute beginners to _never_ comment their code). A good heuristic that it's time to include a comment is if you had to look up how to do something. A surprisingly large amount of what professional developers do all day is Google how to do things and copy code from StackOverflow.com. It's good to include a comment with a link to the page you copied from because it typically has a good write-up of why it's done that way.

### Writing code for readability

One problem with comments is that they don't execute. Sometimes the code that the comment was describing gets changed and the comment describing it doesn't get changed or deleted to reflect that, so comments can end up being misleading. Often, there's no substitute for reading the code itself. For that reason, the computer isn't the only audience you're writing the code for: you're writing it to be read by programmers too. Choose good, descriptive (without being overly wordy) names for your variables, functions and classes. In many cases, there's a way to accomplish what you're trying to do in just one line, but one-liners aren't always the most readable things. Breaking out the steps and giving the intermediate results good names can function as a kind of comment that also makes it easier to inspect values during debugging. Code written to be readable is, to other programmers, the best kind of documentation there is.
