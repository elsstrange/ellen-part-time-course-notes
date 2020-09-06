# STILL WORK IN PROGRESS
* More on coupling and delegation
* Needs examples
* Needs Ruby terminology
  * Classes and Instances

# Object Oriented Programming (OOP)

Object Oriented Programming structures the functionality of a program through the use of **objects**. The idea is that a program will have multiple objects, which each have clear and limited responsibilities. These responsibilities might be to do with holding information about the program state, or to do with carrying out a process to affect that state. 

Essential to effective Object Oriented Design (OOD) is the idea that, much like employees at a company, no single object should be responsible for too much. Instead an object can delegate resposibility for a process to another object that is specialised for the purpose. 

Doing this can help reduce complexity in code, by avoiding processes tangling or overlapping and creating contradictory or unexpected outcomes. It also helps make code more modifiable and extendable. If a change in functionality is required, only one or two objects may need amending to implement the change, rather than needing to rewrite throughout the codebase. If new functionality is introduced, it can be done through new objects that handle that functionality, with minimal impact on existing working code.

## Encapsulation and Cohesion
One of the key challenges in OOD is working out what separate objects are required. Objects don't necessarily directly represent objects in the real world - they can represent categories that might embrace several real world objects, or represent roles that in the real world might be performed by a single person or thing. Capturing functionality into a class is referred to as **encapsulation**.

Objects should be tightly focused on a single responsibility. The more effectively you do this, the more you reap the benefits of modifiabiliy and extendability that are OOD's strengths. An object with tightly focused responsibility is referred to as having **high cohesion**. 

A useful way to tackle this challenge is to think of OOD being all about modelling behaviour:

1. Describe briefly all the separate things your program needs to do. Don't let yourself use the word "and"!
1. Diagram which tasks relate most closely to each other. 
1. You should find clusters of similar tasks grouping together. If any of these clusters are noticably larger than the others, really push to think about what sub-groups exist within each cluster (but don't stress too much - you can always refactor later.)
1. These clusters of tasks are your first indication of what might be separate objects in your program. Now add to your diagram how they relate to each other - which objects need to rely on other objects to do things for them? What information needs to pass back and forth to fulfil this? This may change how you think about your structure.

## Dependency, Coupling and Delegation
Once you have your structure, and you've written a feature test that describes at the top level how the program will function, you will start writing unit tests and source code for your objects. At this point, you will need to think about how the objects will make use of each others functionality. Having one object rely on another to carry out a process or hold some information about program state is referred to as a **dependency**.

Your top consideration in implementing dependencies should always be to avoid hard-coding whereever possible. An object that is depended upon should be provided to its dependent object in a way that can be swapped in and out readily. This makes it easier to isolate functionality in testing, and also offers more options in the future for passing in alternative dependencies.

There are three main options for passing dependencies to avoid hard coding:
* Injecting a whole class into an instance of another class when it's initialized. This is most useful where the instance that receives the injection needs to create multiple new instances of the injected class.
* Injecting a single instance of a class into an instance another class when it's initialized. This is most useful where the instance that is injected is the subject of most of the functionality of the instance that it's injected into.
* Passing a single instance of a class as an argument to a method on another class. This is most useful where the method that's called needs to handle multiple objects (different instances or even different classes) in similar ways (see also Polymorphism).

Ruby also offers inheritance and module mixins as ways to pass the entire functionality of one class into another, but these are a different sort of dependency.

When taking a TDD approach to writing programs with multiple objects, it's essential to isolate the unit tests for different objects from the other classes they depend on. LINK TO ISOLATED TEST DOC.
