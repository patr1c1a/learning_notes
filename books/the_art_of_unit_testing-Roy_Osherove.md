# "The art of unit testing, with examples in C#"

**Book citation:** Osherove, R. (2014). The art of unit testing : with examples in C#. Manning.

## Chapter 1: The basics of unit testing

A "unit" can be understood as a "unit of work" or a "use case" in the system.

A unit test is a piece of code that invokes a unit of work being tested, and checks some assumptions about a single end result of that unit. It's consistent in its results as long as production code hasn't changed.

Unit tests isolate the unit of work from its dependencies so the results are consistent and they can easily control and simulate any aspect of the unit's behavior.

A _unit of work_ is the sum of actions that take place between the invocation of a public method in the system and a single noticeable end result by a test of that system, such as:

- The invoked public method returns a value;

- There's a noticeable change to the state or behavior of the system before and after invocation;

- There's a callout to a third-party system over which the test has no control, and whatever that third-party system returns is ignored (e.g.: logging system).

A unit test's scope can span as little as a method or as much as multiple classes. If you try to minimize the size of a unit of work you might end up faking things that aren't end results to the user.

### Properties of a good unit test

- It should be automated and repeatable.

- It should be easy to implement.

- It should be relevant tomorrow.

- Anyone should be able to run it at the push of a button.

- It should run quickly.

- It should be consistent in its results (it always returns the same result if you don't change anything between runs).

- It should have full control of the unit under test.

- It should be fully isolated (runs independently of other tests).

- When it fails, it should be easy to detect what was expected and determine how to pinpoint the problem.

### Integration tests

They are tests that use one or more real dependencies of the units under test (e.g.: the real filesystem, a real database, etc.).

If a test doesn't have control of the system time and it uses DateTime.Now, then every time it runs it uses a different time, so it's not consistent.

Integration tests are usually slower than unit tests, since they might need to run against something they have no control over.

### Test-driven development

It's about writing tests before the production code is written. TDD means test-first development, with design taking a secondary role.

TDD takes time to learn and to implement, but it's worth it.

The technique of TDD is as follows:

1. Write a test that fails: prove that some code or functionality is missing from the end product. The test is written as if the production code were already working.

2. Move on to creating production code that makes the test pass: the production code should meet the expectations of the test.

3. Refactor your code(without changing functionality): if it needs to be more readable, remove duplication, etc. If not needed, move on to the next (failing) unit test.

TDD is also about testing the test itself. Trying to get the test fail at first and then seeing it pass without changing the test means that if you expect it to fail and it passes, there's a bug in the test or you're testing the wrong thing.

(To be continued)
