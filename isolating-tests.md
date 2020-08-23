# Isolating Tests

## What is isolation?
Isolating tests is a way of testing parts of your code independently of one another.  Often objects in your code will rely on the functionality of other objects to do their job, for example a Checkout class might rely on a Basket class to provide the Items and quantities to checkout, and also rely on the Items to provide their prices. If you break something about the Basket class, the Checkout class's tests will start to fail, but the reason for this could be obscure - after all, you haven't changed the Checkout class. By isolating the tests for Checkout, Checkout can continue to pass its tests, while the Basket class is failing its own isolated tests - which indicates to you where the real problem lies.

## How do you isolate?
The specifics of how to isolate your tests will depend on the specific dependencies in your code - how you are injecting or forwarding dependencies to other classes for example. But in general you should identify what objects your code receives or creates and what methods it passes to those objects. These are its dependencies, and these are what you need to remove to isolate your tests.

The process of removing these dependencies is called *mocking*. In mocking, you replace objects that your code depends on with *mocks* or *doubles* (the two terms are interchangeable).

A mock or double is like a fake of the real object's code, and like all fakes, it only needs to be detailed enough to pass the level of scrutiny it will receive. In practical terms, this means it needs to be ready to respond to any methods it is passed, but it doesn't need to do so dynamically - it's enough that it provides a single pre-determined valid response.

The process of making your mock "good enough" to fake its way through the code that depends on it is called *stubbing*.  A *stub* represents a method - it has the same name as a real method, but it doesn't contain all the code of the real method. Instead, when creating a stub, you just specify the return value the stub should give, for the scenario in the test. You don't need to stub every method that the object you're mocking has - you only need to stub the methods that each test will call upon.

Test frameworks like rspec provide a toolkit for creating mocks and stubs.

### Example (using the m-spec framework):

**Non-isolated**  
```ruby
cover_story = CoverStory.new("Businessman","Universal Exports")
james_bond = Spy.new(name: "Bond, James Bond", cover_story: cover_story)
passport_control = PassportControl.new(country: "Switzerland")
  
expect(passport_control.process(james_bond)).to eq "Welcome to Switzerland, Bond, James Bond."
```

**Isolated**  
```ruby
james_bond = test_double('A spy called James Bond') 
allow(james_bond).to receive(:name) { "Bond, James Bond" } 
passport_control = PassportControl.new(country: "Switzerland")
  
expect(passport_control.process(james_bond)).to eq "Welcome to Switzerland, Bond, James Bond."
```

*Notice that the passport_control.process method only interacts with the spy's name, and not its cover story. This means that the mock spy (james_bond) doesn't need any stubs for its cover story, and there's no need to make a mock CoverStory at all. The only stub needed is :name, because that's the only method that passport_control.process needs.*
  
  
**Non-isolated**  
```ruby
cover_story = CoverStory.new("Businessman","Universal Exports")
james_bond = Spy.new(name: "Bond, James Bond", cover_story: cover_story)
goldfinger = Villain.new(paranoia: 100)
  
expect(goldfinger.interrogate(james_bond)).to eq "Come now, Mr Bond, James Bond, you don't expect me to believe you're a Businessman, do you?"
```

**Isolated**  
```ruby
james_bond = test_double('A spy called James Bond') 
allow(james_bond).to receive(:name) { "Bond, James Bond" } 
allow(james_bond).to receive(:profession) { "Businessman" } 
goldfinger = Villain.new(paranoia: 100)
  
expect(goldfinger.interrogate(james_bond)).to eq "Come now, Mr Bond, James Bond, you don't expect me to believe you're a Businessman, do you?"
```

*The Villain.interrogate method needs access to the james_bond mock's profession as well as its name. Notice, though, that we still don't need to mock the CoverStory class because Villain only interacts with the Spy class. This means we only need to know what method is called on the spy (:profession) and what it would return ("Businessman"). For the purposes of an isolated test we don't care how :profession actually does this, it's enough that our mock spy can give a valid answer (even if the villain doesn't believe it!)*

  
## What are the risks of isolation?
Isolating tests can create problems if you haven't written all the tests you need. Imagine in the example above you made a change to the Spy class that affected its :name method, so that instead of just returning "Bond, James Bond" it now returns "The name's Bond, James Bond". This would make a mess of the outputs for PassportControl.process and Villain.interrogate. But you isolated your tests, so both PassportControl and Villain still seem to be working properly because their mocks and stubs haven't changed. 

However, if you've written your tests for the Spy class correctly, this shouldn't be an issue. There should have been a test for :name that showed the required output was "Bond, James Bond", with no extra fluff.  This would be an indication that you shouldn't just change the output of the :name method, but instead make a new one, something like :cool_greeting. *Your tests represent expected behaviour, and that behaviour should always be expected for a reason. If you break a test, you should assume that it will also break something that depends on the functionality in that test.*
  
Another way you can detect when a code change has had unintended consequences on its dependents is by using **feature tests**. Your isolated tests will mainly be **unit tests** - testing small bits of specific functionality to ensure they give the correct outputs for the given inputs. Unit tests are targetted at the level of individual methods. Feature tests, on the other hand, look at the bigger picture. They describe how a user would actually interact with the program using all those smaller processes.
  
For example, you might write a feature test in which a Spy meets a Villain. The Spy is given a real CoverStory this time, and interacts with the Villain using several methods, all of which gradually increase the Villain's paranoia. At the end of the feature test, Villain calls :interrogate on Spy, and returns his declaration "Come now, Mr Bond, James Bond, you don't expect me to believe you're a Businessman, do you?"  The feature test explores the full behaviour of the program, where all the classes interact together, and checks that everything is pulling together as expected.

At this point, if you *did* botch up your Spy class's :name method, it would become clear, because you'd no longer pass the feature test. The output would be "Come now, Mr The name's Bond, James Bond, you don't expect me to believe you're a Businessman, do you?". But, crucially, you'd quickly be able to work out that the problem must be in your Spy class, not your Villain class, since all your Villain tests are isolated and still passing.
  
#### Test Types
* **Unit tests** are isolated to demonstrate that code runs as expected regardless of other code that it depends on.
* **Feature tests** remove isolation in order to demonstrate that the dependencies within the code interact as expected.


## Navigating TDD of dependent classes

It can be tricky to know where to begin writing isolated tests for a domain that has several interdependent classes. It's best not to try to write a whole class and mocks in isolation.  Instead, try to be responsive and move around your code base writing the tests and source code that you need right now. A reasonable process would be:

1. Pick any class to start with. We'll call this ClassOne.
1. Write the first test for ClassOne.
1. If that test's set-up looks like it requires another object (e.g. an instance of ClassTwo), immediately make a mock for that object (class_two), but **don't** try to guess what stubs it might need yet. Don't add anything to your mock until you need it.
1. Start writing your source code.
1. If your source code calls a method on ClassTwo STOP!
1. Go and write a test for ClassTwo for that method. Run the test, write the source code and refactor until it passes (this might mean running through steps 3 - 6 again, until you get to the furthest point of your dependency structure.)
1. Go back to your ClassOne test, and add the stub for the new ClassTwo method to your class_two mock.
1. Go back to your ClassOne source code and finish it up.
1. Run your test till it passes, and refactor as normal.
1. Write your next test for ClassOne, and repeat.
