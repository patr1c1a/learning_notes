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

## Chapter 2: A first unit test

Unit testing frameworks help developers test their code and also run tests as part of an automated build. They provide base classes or interfaces, attributes to recognize which methods are tests, assertion classes, a test runner to run tests and indicate the status, reporting tools to analyze the results.

## NUnit

Use NuGet to `Install-Package NUnit` (a reference to _NUnit.Framework.dll_ will show up in the project). Alternatively, there's a GUI that can be used to understand how the framework works (for this we need to install _NUnit.Runners_ or download the whole bundle from NUnit's website).

Then add nunit.framework.dll as a reference to your test project.

NUnit needs two attributes to know what to run:

- \[TestFixture]: denotes a class that holds automated NUnit tests.

- \[Test]: denotes it's an automated test to be invoked.

Test classes should be named \[ClassName]Tests. Test methods should be named \[UnitOfWorkName]_\[ScenarioUnderTest]_\[ExpectedBehavior]. There are three parts to the test method name:

- UnitOfWorkName: name of the method or group of methods or classes under test.

- Scenario: conditions under which the unit is tested.

- ExpectedBehavior: what the tested method should do under the specified conditions: return a value or exception, change the state of the system, or call a third-party system.

## Example project: LogAn

Your company has many internal products it uses to monitor its applications at customer sites. All these products write log files and place them in a special directory. The log files are written in a proprietary format that your company has come up with that can't be parsed by any existing third-party tools. Youre tasked with building a product, _LogAn_, that can analyze these log files and find special cases and events in them. When it finds these cases and events, it should alert the appropriate parties.

## First unit test

Let's say we have to test this class:

```csharp
public class LogAnalyzer
{
    public bool IsValidLogFileName(string fileName)
    {
        if(!fileName.EndsWith(".SLF"))
        {
            return false;
        }
        return true;
    }
}
```

Given the class under test is named _LogAnalyzer_, then the test class will be called _LogAnalyzerTests_. And a first test method chould be called IsValidLogFileName_BadExtension_ReturnsFalse(). The test class should have te \[TestFixture] attribute right before its declaration. And each test method should have the \[Test] attribute before the method signature.

As a general rule, test methods should be public, void and accept no parameters.

So far the test looks like this:

```csharp
[TestFixture]
public class LogAnalyzerTests
{
    [Test]
    public void IsValidFileName_BadExtension_ReturnsFalse()
    {
    }
}
```

Unit tests should have 3 parts:

- Arrange: create and set up objects as necessary.

- Act: on an object, execute methods.

- Assert: that something is as expected.

Example:

```csharp
[Test]
public void IsValidFileName_BadExtension_ReturnsFalse() {
    LogAnalyzer analyzer = new LogAnalyzer();
    bool result = analyzer.IsValidLogFileName("filewithbadextension.foo");
    Assert.False(result);
}
```

The test can be run:

- Using the NUnit GUI

- Using Visual Studio's test runner with a NUnit Runner extension, (called NUnit Test Adapter in the NUget Gallery)

- Using the ReSharper test runner (a VS plugin)

- Using the TestDriven.Net test runner (another VS plugin)

To use the NUnit GUI, you need to provide it with a .dll assembly, created by building the project.

### NUnit's Assert class

This class is the bridge between your code and the NUnit framework. It has static methods and it's supposed to declare that a specific asusmption is supposed to exist. The main method is `Assert.True`, which verifies a boolean condition. Some other methods are:

`Assert.AreEqual(expectedObject, actualObject, message);`: it verifies the values of both, the expected and the actual object, match.

`Assert.AreSame(expectedObject, actualObject, message);`: it verifies that the expected and actual object are the same (it checks the reference to the object).

The `message` parameter is optional, but using it indicates the need to rename the test to make it clearer.

(To be continued)
