# What is Test Driven Design? (TDD)
In Test Driven Design, a program is planned, designed and written with reference to criteria it must fulfil. Given the criteria (often as **user stories**, see below) the software developer writes test code that, when passed, will demonstrate that the program fulfils those criteria and is therefore fit for purpose (see below). Only when those tests are written does the developer write the source code. They then execute the tests on the source code to ensure the source code passes.

#### What are user stories?

Program requirements may be expressed as user stories. These identify a stakeholder in the software (for example, an ecommerce platform might have a customer and a retailer) and an action the stakeholder wants to perform. The user story may also include an explanation of why the user wants to do that action, which helps the developer properly understand the problem. Sometimes a user story is equivalent to a single test, but often you may need multiple tests (unit tests) to demonstrate the fulfilment of a user story.

## Why do TDD?

Writing code to pass specific tests helps reduce scope-creep (adding unnecessary complexity) or the developer misunderstanding the purpose of the source code they are writing. Functionality becomes the priority, and the simplest route to achieving this is taken.

Imagine being back at school, facing an end of year exam. You don't know the exam questions, so you have to revise everything you learned that year, to be confident of passing with full marks. Imagine that instead, your teacher gave you the question paper in advance (this is like being given user stories) - you can now focus just on revising the topics that will come up. Imagine if, after that, the teacher (this teacher is *super lazy*) asked you to write the mark scheme for the questions. Now you don't even have to revise general information - now you can pre-plan all your answers to get full marks! Your teacher even lets you try the answers out a few times before sitting the exam, so when you finally sit it you're not at all surprised to score 100%. Of course, you have to make sure your mark scheme is suitably stringent, otherwise you won't have demonstrated that you know anything...

Another advantage of TDD is that a well-written set of tests makes future updates and changes to the code safer to implement. By re-running your pre-existing tests alongside tests for the new functionality you've introduced, you can see if your new code has had any unintended consequences and broken pre-existing functionality (regression). Using **isolation**, it's also possible to write your tests in a way that can immediately point you towards where your breaking change has been made (see below).

## What are the risks of TDD?

When working in small units (writing code to pass a single test before moving onto the next test) programs can expand in complexity, as one development path is pursued without consideration of others. Code that makes sense in the narrow frame of reference of a single test may be problematic in a larger codebase. To mitigate this, prior to writing tests the developer needs to understand the domain of the project - effectively a summary of the objects and actions that need to exist and execute in the program. Writing or diagramming this can identify potential areas of conflict that can be accounted for in the tests. Another mitigation is robust refactoring and peer review of code, to avoid duplication, conflicts or issues of intelligibility.

Another risk (particularly acute in the context of machine learning algorithms, since the inner workings of the source code are obscured), is that the tests do not adequately describe success criteria.  This could be by failing to anticipate edge case scenarios (Black Swans as economists would call them) leading to code that breaks or performs unexpectedly ([a funny example](https://www.wired.com/2015/11/null/)). Or it could be that the tests themselves are biased to anticipate particular outputs ([not a funny example - though unclear if it involved TDD](https://www.foxglove.org.uk/news/c6tv7i7om2jze5pxs409k3oo3dyel0)). To mitigate this, tests themselves should be a critical part of code review, particularly when there is ambiguity in the requirements provided to the developer.

## What does a (basic) test look like?

A test should describe what it is testing for in plain human language.

```
It is pessimistic when the weather is rainy
```

The test will have a set up phase, creating all the Objects needed for the test, and giving them the properties and states required by the test scenario.

```ruby
pessimist = Outlook.new
glass = Glass.new(contents: 0.5)
```

The test will have an execute phase, where the actual process/action being tested is carried out. It will return a value that demonstrates that the action has successfully achieved the desired effect. Note that sometimes the requirements may mean that additional methods and objects are necessary to demonstrate success. In this example, imagine a previous test has already specified that the defining characteristic of a pessimistic outlook object is that is sees a partially full glass as half empty.

```ruby
pessimist.weather("rainy")
result = pessimist.assess_amount(glass)
expected = "This glass is half empty"
```

The test will verify if the execute step produced the expected result, pronouncing the test successful if so.

```ruby
result == expected
```

## Summarise the TDD process
1. Define the domain - identify objects and properties, methods and outputs under different scenarios
1. Write a test or set of closely related tests. Write the code you want to execute with its expected output, then write any set-up it requires.
1. Run the test
1. Write source code, re-running the test regularly until it passes
1. Refactor the source code
1. Re-run the test
1. Return to step 2 and write your next test


## Continue to isolating-tests.md
