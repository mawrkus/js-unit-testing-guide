# A guide to unit testing in JavaScript

## This is a living document. New ideas are always welcome. Contribute: fork, clone, branch, commit, push, pull request.

### Disclaimer

> All the information provided has been compiled & adapted from the references cited at the end of the document.
> The guidelines are illustrated by my own examples.
> Many thanks to all of the sources of information & contributors.

## Table of content

1. General principles
  + [Unit tests](#unit-tests)
  + [Design principles](#design-principles)
2. Guidelines
  + [Whenever possible, use TDD](#whenever-possible-use-tdd)
  + [Structure your tests properly](#structure-your-tests-properly)
  + [Name your tests properly](#name-your-tests-properly)
  + [Don't comment tests](#dont-comment-tests)
  + [Avoid logic in your tests](#avoid-logic-in-your-tests)
  + [Don't write unnecessary expectations](#dont-write-unnecessary-expectations)
  + [Setup properly the actions that apply to all the tests involved](#setup-properly-the-actions-that-apply-to-all-the-tests-involved)
  + [Consider using factory functions in the tests](#consider-using-factory-functions-in-the-tests)
  + [Know your testing framework API](#know-your-testing-framework-api)
  + [Don't test multiple concerns in the same test](#dont-test-multiple-concerns-in-the-same-test)
  + [Cover the general case and the edge cases](#cover-the-general-case-and-the-edge-cases)
  + [When applying TDD, always start by writing the simplest failing test](#when-applying-tdd-always-start-by-writing-the-simplest-failing-test)
  + [When applying TDD, always make small steps in each test-first cycle](#when-applying-tdd-always-make-small-steps-in-each-test-first-cycle)
  + [Test the behaviour, not the internal implementation](#test-the-behaviour-not-the-internal-implementation)
  + [Consider using fake objects](#consider-using-fake-objects)
  + [Create new tests for every defect](#create-new-tests-for-every-defect)
  + [Don't write unit tests for complex user interactions](#dont-write-unit-tests-for-complex-user-interactions)
  + [Test simple user actions](#test-simple-user-actions)
  + [Review test code first](#review-test-code-first)
  + [Practice code katas, learn with pair programming](#practice-code-katas-learn-with-pair-programming)
3. [References](#references)

## General principles

### Unit tests

**Unit = Unit of work**

This could involve **multiple methods and classes** invoked by some public API that can:

+ Return a value or throw an exception
+ Change the state of the system
+ Make 3rd party calls

A unit test should test the behaviour of a unit of work: for a given input, it expects an end result that can be any of the above.

**Unit tests are isolated and independent of each other**

+ Any given behaviour should be specified in **one and only one test**
+ The execution/order of execution of one test **cannot affect the others**

The code is designed to support this independence (see "Design principles" below).

**Unit tests are lightweight tests**

+ Repeatable
+ Fast
+ Consistent
+ Easy to write and read

**Unit tests are code too**

They must meet the same level of quality as the code being tested. They can be refactored as well to make them more maintainable and/or readable.

### Design principles

The key to good unit testing is to write **testable code**. Applying simple design principles can help, in particular:

+ Use a **good naming** and **comment** your code (the "why?" not the "how"), keep in mind that comments are not a substitute for bad naming or bad design
+ **DRY**: Don't Repeat Yourself, avoid code duplication
+ **Single responsibility**: each object/function must focus on a single task
+ Keep a **single level of abstraction** in the same component (for example, do not mix business logic with lower-level technical details in the same method)
+ **Minimize dependencies** between components: encapsulate, interchange less information between components
+ **Support configurability** rather than hard-coding, this prevents from having to replicate the exact same environment when testing (e.g.: markup)
+ Apply adequate **design patterns**, especially **dependency injection** that allows to separate objects creation responsibility from business logic
+ Avoid global mutable state

## Guidelines

The goal of these guidelines is to make your tests:

+ **Readable**
+ **Maintainable**
+ **Trustworthy**

These are the 3 pillars of good unit testing.

All the following examples assume the usage of the [Jasmine](http://jasmine.github.io) framework.

---------------------------------------

### Whenever possible, use TDD

TDD is a _design process_, not a testing process. TDD is a robust way of designing software components ("units") interactively so that their behaviour is specified through unit tests.

How? Why?

#### Test-first cycle

1. Write a simple failing test
2. Make the test pass by writing the minimum amount of code
3. Refactor the code by applying design principles/patterns

During phase 2, don't bother with quality.

#### Consequences of the test-first cycle

+ Writing a test first makes the code design more testable
+ Writing just the amount of code needed to implement the required functionality makes the resulting codebase minimal, thus more maintainable
+ The codebase can be enhanced using refactoring mechanisms, the tests give you confidence that the new code is not modifying the existing functionalities
+ Cleaning the code in each cycle makes the codebase more maintainable, it is much cheaper to change the code frequently and in small increments
+ Fast feedback for the developers, you know that you don't break anything and that you are evolving the system in a good direction
+ Generates confidence to add features, fix bugs, or explore new designs

Note that code written without a test-first approach is often very hard to test!

### Structure your tests properly

Don't hesitate to nest your suites to structure logically your tests in subsets.

**:(**

```js
describe('A set of functionalities', () =>
{
  it('a set of functionalities should do something nice', () =>
  {
  });

  it('a subset of functionalities should do something great', () =>
  {
  });

  it('a subset of functionalities should do something awesome', () =>
  {
  });

  it('another subset of functionalities should also do something great', () =>
  {
  });
});
```

**:)**

```js
describe('A set of functionalities', () =>
{
  it('should do something nice', () =>
  {
  });

  describe('A subset of functionalities', () =>
  {
    it('should do something great', () =>
    {
    });

    it('should do something awesome', () =>
    {
    });
  });

  describe('Another subset of functionalities', () =>
  {
    it('should also do something great', () =>
    {
    });
  });
});
```

### Name your tests properly

Tests names should be concise, explicit, descriptive and in correct English. Read the output of the spec runner and verify that it is understandable! Keep in mind that someone else will read it too. Tests can be the live documentation of the code.

**:(**

```js
describe('myGallery', () =>
{
  it('init set correct property when called (thumb size, thumbs count)', () =>
  {
  });

  // ...
});
```

**:)**

```js
describe('The Gallery instance', () =>
{
  it('should properly calculate the thumb size when initialized', () =>
  {
  });

  it('should properly calculate the thumbs count when initialized', () =>
  {
  });

  // ...
});
```

In order to help you write test names properly, you can use the **"unit of work - scenario/context - expected behaviour"** pattern:

```js
describe('[unit of work]', () =>
{
  it('should [expected behaviour] when [scenario/context]', () =>
  {
  });
});
```

Or whenever you have many tests that follow the same scenario or are related to the same context:

```js
describe('[unit of work]', () =>
{
  describe('when [scenario/context]', () =>
  {
    it('should [expected behaviour]', () =>
    {
    });
  });
});
```

For example:

**:) :)**

```js
describe('The Gallery instance', () =>
{
  describe('when initialized', () =>
  {
    it('should properly calculate the thumb size', () =>
    {
    });

    it('should properly calculate the thumbs count', () =>
    {
    });
  });

  // ...
});
```

### Don't comment tests

Never. Ever. Tests have a reason to be or not.

Don't comment them because they are too slow, too complex or produce false negatives. Instead, make them fast, simple and trustworthy. If not, remove them completely.

### Avoid logic in your tests

Always use simple statements. Loops and conditionals must not be used. If they are, you add a possible entry point for bugs in the test itself:

+ Conditionals: you don't know which path the test will take
+ Loops: you could be sharing state between tests

**:(**

```js
it('should properly sanitize strings', () =>
{
  let result;
  const testValues = {
    'Avion'         : 'Avi' + String.fromCharCode(243) + 'n',
    'The-space'     : 'The space',
    'Weird-chars-'  : 'Weird chars!!',
    'file-name.zip' : 'file name.zip',
    'my-name.zip'   : 'my.name.zip'
  };

  for (result in testValues)
  {
    expect( sanitizeString(testValues[result]) ).toEqual(result);
  }
});
```

**:)**

```js
it('should properly sanitize strings', () =>
{
  expect( sanitizeString('Avi'+String.fromCharCode(243)+'n') ).toEqual('Avion');
  expect( sanitizeString('The space') ).toEqual('The-space');
  expect( sanitizeString('Weird chars!!') ).toEqual('Weird-chars-');
  expect( sanitizeString('file name.zip') ).toEqual('file-name.zip');
  expect( sanitizeString('my.name.zip') ).toEqual('my-name.zip');
});
```

Better: write a test for each type of sanitization. It will give a nice output of all possible cases, improving readability and maintainability.

**:) :)**

```js
it('should sanitize a string containing non-ASCII chars', () =>
{
  expect( sanitizeString('Avi'+String.fromCharCode(243)+'n') ).toEqual('Avion');
});

it('should sanitize a string containing spaces', () =>
{
  expect( sanitizeString('The space') ).toEqual('The-space');
});

it('should sanitize a string containing exclamation signs', () =>
{
  expect( sanitizeString('Weird chars!!') ).toEqual('Weird-chars-');
});

it('should sanitize a filename containing spaces', () =>
{
  expect( sanitizeString('file name.zip') ).toEqual('file-name.zip');
});

it('should sanitize a filename containing more than one dot', () =>
{
  expect( sanitizeString('my.name.zip') ).toEqual('my-name.zip');
});
```

### Don't write unnecessary expectations

Remember, unit tests are a design specification of how a certain *behaviour* should work, not a list of observations of everything the code happens to do.

**:(**

```js
it('should multiply the number passed as parameter and subtract one', () =>
{
  const multiplySpy = spyOn(Calculator, 'multiple').and.callThrough();
  const subtractSpy = spyOn(Calculator, 'subtract').and.callThrough();

  const result = Calculator.compute(21.5);

  expect(multiplySpy).toHaveBeenCalledWith(21.5);
  expect(subtractSpy).toHaveBeenCalledWith(43, 1);
  expect(result).toBe(42);
});
```

**:)**

```js
it('should multiply the number passed as parameter and subtract one', () =>
{
  const result = Calculator.compute(21.5);
  expect(result).toBe(42);
});
```

This will improve maintainability. Your test is no more tight to implementation details.

### Setup properly the actions that apply to all the tests involved

**:(**

```js
describe('Saving the user profile', () =>
{
  let profileModule;
  let notifyUserSpy;
  let onCompleteSpy;

  beforeEach(() =>
  {
    profileModule = new ProfileModule();
    notifyUserSpy = spyOn(profileModule, 'notifyUser');
    onCompleteSpy = jasmine.createSpy();
  });

  it('should send the updated profile data to the server', () =>
  {
    jasmine.Ajax.install();

    profileModule.save();

    const request = jasmine.Ajax.requests.mostRecent();

    expect(request.url).toBe('/profiles/1');
    expect(request.method).toBe('POST');
    expect(request.data()).toEqual({ username: 'mawrkus' });

    jasmine.Ajax.uninstall();
  });

  it('should notify the user', () =>
  {
    jasmine.Ajax.install();

    profileModule.save();

    expect(notifyUserSpy).toHaveBeenCalled();

    jasmine.Ajax.uninstall();
  });

  it('should properly execute the callback passed as parameter', () =>
  {
    jasmine.Ajax.install();

    profileModule.save(onCompleteSpy);

    jasmine.Ajax.uninstall();

    expect(onCompleteSpy).toHaveBeenCalled();
  });
});
```

The setup code should apply to all the tests:

**:)**

```js
describe('Saving the user profile', () =>
{
  let profileModule;

  beforeEach(() =>
  {
    jasmine.Ajax.install();
    profileModule = new ProfileModule();
  });

  afterEach( () =>
  {
    jasmine.Ajax.uninstall();
  });

  it('should send the updated profile data to the server', () =>
  {
    profileModule.save();

    const request = jasmine.Ajax.requests.mostRecent();

    expect(request.url).toBe('/profiles/1');
    expect(request.method).toBe('POST');

  });

  it('should notify the user', () =>
  {
    spyOn(profileModule, 'notifyUser');

    profileModule.save();

    expect(profileModule.notifyUser).toHaveBeenCalled();
  });

  it('should properly execute the callback passed as parameter', () =>
  {
    const onCompleteSpy = jasmine.createSpy();

    profileModule.save(onCompleteSpy);

    expect(onCompleteSpy).toHaveBeenCalled();
  });
});
```

Consider keeping the setup code minimal to preserve readability and maintainability.

### Consider using factory functions in the tests

It will help reducing the setup code and make each test more readable. The reader of the test does not have to look at multiple places to understand what's going on. In some cases, it will also provide some flexibility when creating new instances (setting an initial state, for example).

**:(**

```js
describe('User profile module', () =>
{
  let profileModule;

  beforeEach(() =>
  {
    profileModule = new ProfileModule({ views: 0 });
  });

  it('should return the current views count', () =>
  {
    const viewsCount = profileModule.getViewsCount();
    expect(profileModule.getViewsCount()).toBe(0);
  });

  it('should increase the views count properly', () =>
  {
    profileModule.incViewsCount();
    expect(profileModule.getViewsCount()).toBe(1);
  });

  it('should set the views count properly', () =>
  {
    profileModule.setViewsCount(42);
    expect(profileModule.getViewsCount()).toBe(42);
  });
});
```

**:)**

```js
function createProfileModule({ views = 0 } = {})
{
  return new ProfileModule({ views });
}

describe('User profile module', () =>
{
  it('should return the current views count', () =>
  {
    const profileModule = createProfileModule({ views: 3 });
    expect(profileModule.getViewsCount()).toBe(3);
  });

  it('should increase the views count properly', () =>
  {
    const profileModule = createProfileModule({ views: 41 });
    profileModule.incViewsCount();
    expect(profileModule.getViewsCount()).toBe(42);
  });

  it('should set the views count properly', () =>
  {
    const profileModule = createProfileModule();
    profileModule.setViewsCount(14);
    expect(profileModule.getViewsCount()).toBe(14);
  });
});
```

### Know your testing framework API

The API documentation of the testing framework should be your bedside book!

Having a good knowledge of the testing framework API can help you reducing the size/complexity of your test code and, in general, help you during development. For example:

**:(**

```js
it('should call a method with the proper arguments', () =>
{
  const foo = {
    bar: jasmine.createSpy()
  };

  foo.bar('baz');

  expect(foo.bar).toHaveBeenCalled();
  expect(foo.bar.calls.argsFor(0)).toEqual(['baz']);
});

/*it('should do more but not now', () =>
{
});

it('should do much more but not now', () =>
{
});*/
```

**:)**

```js
fit('should call once a method with the proper arguments', () =>
{
  const foo = {
    bar: jasmine.createSpy()
  };

  foo.bar('baz');

  expect(foo.bar).toHaveBeenCalledWith('baz');
});

it('should do something else but not now', () =>
{
});

it('should do something else but not now', () =>
{
});
```

Note: the handy `fit` function used in the example above allows you to execute only one test without having to comment all the tests below. `fdescribe` does the same for test suites. This could help saving a lot of time when developing.

More information on the [Jasmine website](http://jasmine.github.io).

### Don't test multiple concerns in the same test

If a method has several end results, each one should be tested separately. Whenever a bug occurs, it will help you locate the source of the problem.

**:(**

```js
it('should send the profile data to the server and update the profile view properly', () =>
{
  // expect(...)to(...);
  // expect(...)to(...);
});
```

**:)**

```js
it('should send the profile data to the server', () =>
{
  // expect(...)to(...);
});

it('should update the profile view properly', () =>
{
  // expect(...)to(...);
});
```

Beware that writing "AND" or "OR" when naming your test smells bad...

### Cover the general case and the edge cases

"Strange behaviour" usually happens at the edges... Remember that your tests can be the live documentation of your code.

**:(**

```js
it('should properly calculate a RPN expression', () =>
{
  const result = RPN('5 1 2 + 4 * - 10 /');
  expect(result).toBe(-0.7);
});
```

**:)**

```js
describe('The RPN expression evaluator', () =>
{
  it('should return null when the expression is an empty string', () =>
  {
    const result = RPN('');
    expect(result).toBeNull();
  });

  it('should return the same value when the expression holds a single value', () =>
  {
    const result = RPN('42');
    expect(result).toBe(42);
  });

  it('should properly calculate an expression', () =>
  {
    const result = RPN('5 1 2 + 4 * - 10 /');
    expect(result).toBe(-0.7);
  });

  it('should throw an error whenever an invalid expression is passed', () =>
  {
    const compute = () => RPN('1 + - 1');
    expect(compute).toThrow();
  });
});
```

### When applying TDD, always start by writing the simplest failing test

**:(**

```js
it('should suppress all chars that appear multiple times', () =>
{
  expect(keepUniqueChars('Hello Fostonic !!')).toBe('HeFstnic');
});
```

**:)**

```js
it('should return an empty string when passed an empty string', () =>
{
  expect(keepUniqueChars('')).toBe('');
});
```

From there, start building the functionalities incrementally.

### When applying TDD, always make small steps in each test-first cycle

Build your tests suite from the simple case to the more complex ones. Keep in mind the incremental design. Deliver software fast, incrementally, and in short iterations.

**:(**

```js
it('should return null when the expression is an empty string', () =>
{
  const result = RPN('');
  expect(result).toBeNull();
});

it('should properly calculate a RPN expression', () =>
{
  const result = RPN('5 1 2 + 4 * - 10 /');
  expect(result).toBe(-0.7);
});
```

**:)**

```js
describe('The RPN expression evaluator', () =>
{
  it('should return null when the expression is an empty string', () =>
  {
    const result = RPN('');
    expect(result).toBeNull();
  });

  it('should return the same value when the expression holds a single value', () =>
  {
    const result = RPN('42');
    expect(result).toBe(42);
  });

  describe('Additions-only expressions', () =>
  {
    it('should properly calculate a simple addition', () =>
    {
      const result = RPN('41 1 +');
      expect(result).toBe(42);
    });

    it('should properly calculate a complex addition', () =>
    {
      const result = RPN('2 9 + 15 3 + + 7 6 + +');
      expect(result).toBe(42);
    });
  });

  // ...

  describe('Complex expressions', () =>
  {
    it('should properly calculate an expression containing all 4 operators', () =>
    {
      const result = RPN('5 1 2 + 4 * - 10 /');
      expect(result).toBe(-0.7);
    });
  });
});
```

### Test the behaviour, not the internal implementation

**:(**

```js
it('should add a user in memory', () =>
{
  userManager.addUser('Dr. Falker', 'Joshua');

  expect(userManager._users[0].name).toBe('Dr. Falker');
  expect(userManager._users[0].password).toBe('Joshua');
});
```

A better approach is to test at the same level of the API:

**:)**

```js
it('should add a user in memory', () =>
{
  userManager.addUser('Dr. Falker', 'Joshua');

  expect(userManager.loginUser('Dr. Falker', 'Joshua')).toBe(true);
});
```

Advantage:

+ Changing the internal implementation of a class/object will not necessarily force you to refactor the tests

Disadvantage:

+ If a test is failing, we might have to debug to know which part of the code needs to be fixed

Here, a balance has to be found, unit-testing some key parts can be beneficial.

### Consider using fake objects

**:(**

```js
describe('when the survey is not disabled', () =>
{
  it('should show the survey if the user has already visited the page', () =>
  {
    const storage = jasmine.createSpyObj('storage', ['setItem', 'getItem']);
    const surveyManager = new SurveyManager(storage);

    spyOn(surveyManager, 'display');
    storage.getItem.and.returnValue('1'); // ouch.

    surveyManager.start();

    expect(surveyManager.display).toHaveBeenCalled();
  });
});
```

**:)**

```js
describe('when the survey is not disabled', () =>
{
  it('should show the survey if the user has already visited the page', () =>
  {
    const storage = jasmine.createSpyObj('storage', ['setItem', 'getItem']);
    const surveyManager = new SurveyManager(storage);

    spyOn(surveyManager, 'display');

    storage.getItem.and.callFake(key =>
    {
      let result = null;

      if (key === 'page-visited')
      {
        result = '1';
      }
      else if (key === 'disable-survey') // correct.
      {
        result = null;
      }

      return result;
    });

    surveyManager.start();

    expect(surveyManager.display).toHaveBeenCalled();
  });
});
```

**:) :)**

```js
describe('when the survey is not disabled', () =>
{
  it('should show the survey if the user has already visited the page', () =>
  {
    // Note: have a look at https://github.com/tatsuyaoiw/webstorage
    const storage = new MemoryStorage();
    const surveyManager = new SurveyManager(storage);

    spyOn(surveyManager, 'display');
    storage.setItem('page-visited', '1'); // correct.

    surveyManager.start();

    expect(surveyManager.display).toHaveBeenCalled();
  });
});
```

### Create new tests for every defect

Whenever a bug is found, create a test that replicates the problem **before touching any code**. From there, you can apply TDD as usual to fix it.

### Don't write unit tests for complex user interactions

Examples of complex user interactions:

+ Filling a form, drag and dropping some items then submitting the form
+ Clicking a tab, clicking an image thumbnail then navigating through a gallery of images previously loaded from a database
+ (...)

These interactions might involve many units of work and should be handled at a higher level by **functional tests**. They will take more time to execute. They could be flaky (false negatives) and they need debugging whenever a failure is reported.

For functional testing, consider using a test automation framework ([Selenium](http://docs.seleniumhq.org/), ...) or QA manual testing.

### Test simple user actions

Example of simple user actions:

+ Clicking on a link that toggles the visibility of a DOM element
+ Submitting a form that triggers the form validation
+ (...)

These actions can be easily tested **by simulating DOM events**, for example:

```js
describe('When clicking on the "Preview profile" link', () =>
{
  it('should show the profile preview if it is hidden', () =>
  {
    const previewLink = document.createElement('a');
    const profileModule = createProfileModule({ previewLink, previewIsVisible: false });

    spyOn(profileModule, 'showPreview');

    click(previewLink);

    expect(profileModule.showPreview).toHaveBeenCalled();
  });

  it('should hide the profile preview if it is displayed', () =>
  {
    const previewLink = document.createElement('a');
    const profileModule = createProfileModule({ previewLink, previewIsVisible: true });

    spyOn(profileModule, 'hidePreview');

    click(previewLink);

    expect(profileModule.hidePreview).toHaveBeenCalled();
  });
});
```

### Review test code first

When reviewing code, always start by reading the code of the tests. Tests are mini use cases of the code that you can drill into.

It will help you understand the intent of the developer very quickly (could be just by looking at the name of the tests).

### Practice code katas, learn with pair programming

Because experience is the _only_ teacher. Ultimately, greatness comes from practicing ; applying the theory over and over again, using feedback to get better every time.

## References

+ Roy Osherove - "JS Unit Testing Good Practices and Horrible Mistakes" : https://www.youtube.com/watch?v=iP0Vl-vU3XM
+ Enrique Amodeo - "Learning Behavior-driven Development with JavaScript" : https://www.packtpub.com/application-development/learning-behavior-driven-development-javascript
+ Steven Sanderson - "Writing Great Unit Tests: Best and Worst Practices" : http://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/
+ Rebecca Murphy - "Writing Testable JavaScript" : http://alistapart.com/article/writing-testable-javascript
+ YUI Team - "Writing Effective JavaScript Unit Tests with YUI Test" : http://yuiblog.com/blog/2009/01/05/effective-tests/
+ Colin Snover - "Testable code best practices" : http://www.sitepen.com/blog/2014/07/11/testable-code-best-practices/
+ Miško Hevery - "The Clean Code Talks -- Unit Testing" : https://www.youtube.com/watch?v=wEhu57pih5w
+ Addy Osmani - "Learning JavaScript Design Patterns" : http://addyosmani.com/resources/essentialjsdesignpatterns/book/
+ Robert C: Martin - "Design principles and design patterns" : http://www.objectmentor.com/resources/articles/Principles_and_Patterns.pdf
+ José Armesto - "Unit Testing sucks (and it’s our fault) " : https://www.youtube.com/watch?v=GZ9iZsMAZFQ

## Contributors

Ruben Norte: https://github.com/rubennorte
