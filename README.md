# 📙 A guide to unit testing in JavaScript

Translations: 🇨🇳 [Chinese (Simplified)](https://github.com/mawrkus/js-unit-testing-guide/tree/master/translations/zh-cn/README.md) (thanks to [GabrielchenCN](https://github.com/GabrielchenCN))

This is a living document, **new ideas are always welcome**. Contribute: fork, clone, branch, commit, push, pull request!

*All the information provided has been compiled & adapted from the references cited at the end of the document. The guidelines are illustrated by my own examples, fruit of my personal experience writing and reviewing unit tests. Many thanks to all of the sources of information & contributors!*

## 📖 Table of contents

1. General principles
  + [Unit tests](#unit-tests)
  + [Design principles](#design-principles)
2. Guidelines
  + [Whenever possible, use TDD](#whenever-possible-use-tdd)
  + [Structure your tests properly](#structure-your-tests-properly)
  + [Name your tests properly](#name-your-tests-properly)
  + [Don't comment out tests](#dont-comment-out-tests)
  + [Avoid logic in your tests](#avoid-logic-in-your-tests)
  + [Don't write unnecessary expectations](#dont-write-unnecessary-expectations)
  + [Properly setup the actions that apply to all the tests involved](#properly-setup-the-actions-that-apply-to-all-the-tests-involved)
  + [Consider using factory functions in the tests](#consider-using-factory-functions-in-the-tests)
  + [Know your testing framework API](#know-your-testing-framework-api)
  + [Don't test multiple concerns in the same test](#dont-test-multiple-concerns-in-the-same-test)
  + [Cover the general case and the edge cases](#cover-the-general-case-and-the-edge-cases)
  + [When applying TDD, always start by writing the simplest failing test](#when-applying-tdd-always-start-by-writing-the-simplest-failing-test)
  + [When applying TDD, always make small steps in each test-first cycle](#when-applying-tdd-always-make-small-steps-in-each-test-first-cycle)
  + [Test the behaviour, not the internal implementation](#test-the-behaviour-not-the-internal-implementation)
  + [Don't mock everything](#dont-mock-everything)
  + [Create new tests for every defect](#create-new-tests-for-every-defect)
  + [Don't write unit tests for complex user interactions](#dont-write-unit-tests-for-complex-user-interactions)
  + [Test simple user actions](#test-simple-user-actions)
  + [Review test code first](#review-test-code-first)
  + [Practice code katas, learn with pair programming](#practice-code-katas-learn-with-pair-programming)
3. [Resources](#-resources)

## General principles

### Unit tests

**Unit = Unit of work**

This could involve **multiple methods and classes** invoked by some public API that can:

+ Return a value or throw an exception
+ Change the state of the system
+ Make 3rd party calls (API, database, ...)

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

They should meet the same level of quality as the code being tested. They can be refactored as well to make them more maintainable and/or readable.

• [Back to ToC](#-table-of-contents) •

### Design principles

The key to good unit testing is to write **testable code**. Applying simple design principles can help, in particular:

+ Use a **good naming** convention and **comment** your code (the "why?" not the "how"), keep in mind that comments are not a substitute for bad naming or bad design
+ **DRY**: Don't Repeat Yourself, avoid code duplication
+ **Single responsibility**: each object/function must focus on a single task
+ Keep a **single level of abstraction** in the same component (for example, do not mix business logic with lower-level technical details in the same method)
+ **Minimize dependencies** between components: encapsulate, interchange less information between components
+ **Support configurability** rather than hard-coding, this prevents having to replicate the exact same environment when testing (e.g.: markup)
+ Apply adequate **design patterns**, especially **dependency injection** that allows separating an object's creation responsibility from business logic
+ Avoid global mutable state

• [Back to ToC](#-table-of-contents) •

## Guidelines

The goal of these guidelines is to make your tests:

+ **Readable**
+ **Maintainable**
+ **Trustworthy**

These are the 3 pillars of good unit testing.

All the following examples assume the usage of the [Jasmine](http://jasmine.github.io) framework.

• [Back to ToC](#-table-of-contents) •

---------------------------------------

### Whenever possible, use TDD

TDD is a _design process_, not a testing process. TDD is a robust way of designing software components ("units") interactively so that their behaviour is specified through unit tests.

How? Why?

#### Test-first cycle

1. Write a simple failing test
2. Make the test pass by writing the minimum amount of code, don't bother with code quality
3. Refactor the code by applying design principles/patterns

#### Consequences of the test-first cycle

+ Writing a test first makes the code design testable de facto
+ Writing just the amount of code needed to implement the required functionality makes the resulting codebase minimal, thus more maintainable
+ The codebase can be enhanced using refactoring mechanisms, the tests give you confidence that the new code is not modifying the existing functionalities
+ Cleaning the code in each cycle makes the codebase more maintainable, it is much cheaper to change the code frequently and in small increments
+ Fast feedback for the developers, you know that you don't break anything and that you are evolving the system in a good direction
+ Generates confidence to add features, fix bugs, or explore new designs

Note that code written without a test-first approach is often very hard to test.

• [Back to ToC](#-table-of-contents) •

### Structure your tests properly

Don't hesitate to nest your suites to structure logically your tests in subsets.

**:(**

```js
describe('A set of functionalities', () => {
  it('a set of functionalities should do something nice', () => {
  });

  it('a subset of functionalities should do something great', () => {
  });

  it('a subset of functionalities should do something awesome', () => {
  });

  it('another subset of functionalities should also do something great', () => {
  });
});
```

**:)**

```js
describe('A set of functionalities', () => {
  it('should do something nice', () => {
  });

  describe('A subset of functionalities', () => {
    it('should do something great', () => {
    });

    it('should do something awesome', () => {
    });
  });

  describe('Another subset of functionalities', () => {
    it('should also do something great', () => {
    });
  });
});
```

• [Back to ToC](#-table-of-contents) •

### Name your tests properly

Tests names should be concise, explicit, descriptive and in correct English. Read the output of the spec runner and verify that it is understandable! Keep in mind that someone else will read it too. Tests can be the live documentation of the code.

**:(**

```js
// Classes are PascalCase
describe('NameOfClass', () => {
	// functions are camelCase
	describe('nameOfFunction', () => {
		// behavior is in the queen's english
		it('it should do somethings with all of the success', () => {
		});
    });

	// getters and setters are also camelCase
	describe('nameOfGetterOrSetter', () => {
		it('init set correct property when called', () => {
		});
	});
});
```

• [Back to ToC](#-table-of-contents) •

### Don't comment out tests

Never. Ever. Tests have a reason to be or not.

Don't comment them out because they are too slow, too complex or produce false negatives. Instead, make them fast, simple and trustworthy. If not, remove them completely.

• [Back to ToC](#-table-of-contents) •

### Avoid logic in your tests

Always use simple statements. Don't use loops and/or conditionals. If you do, you add a possible entry point for bugs in the test itself:

+ Conditionals: you don't know which path the test will take
+ Loops: you could be sharing state between tests

**:(**

```ts
it('should properly sanitize strings', () => {
  let result;
  const testValues = {
    'Avion'         : 'Avi' + String.fromCharCode(243) + 'n',
    'The-space'     : 'The space',
    'Weird-chars-'  : 'Weird chars!!',
    'file-name.zip' : 'file name.zip',
    'my-name.zip'   : 'my.name.zip'
  };

  for (result in testValues) {
    expect(sanitizeString(testValues[result])).toBe(result);
  }
});
```

**:)**

```ts
it('should properly sanitize strings', () => {
  expect(sanitizeString('Avi'+String.fromCharCode(243)+'n')).toBe('Avion');
  expect(sanitizeString('The space')).toBe('The-space');
  expect(sanitizeString('Weird chars!!')).toBe('Weird-chars-');
  expect(sanitizeString('file name.zip')).toBe('file-name.zip');
  expect(sanitizeString('my.name.zip')).toBe('my-name.zip');
});
```

Better: Use  `it.each`

**:) :)**

```ts
it.each`
    stringInput         | expectedResult
    ${'Avion'}          | ${'Avi'+String.fromCharCode(243)+'n'}
    ${'The-space'}      | ${'The space'}
    ${'Weird-chars-'}   | ${'Weird chars!!'}
    ${'file-name.zip'}  | ${'file name.zip'}
    ${'my-name.zip'}    | ${'my.name.zip'}
`(`should properly sanitize '${stringInput}' to '${expectedResult}'`, ({ stringInput, expectedResult }) => {
	expect(sanitizeString(stringInput)).toBe(expectedResult);
});
```

• [Back to ToC](#-table-of-contents) •

### Don't test implementation details

Remember, unit tests are a design specification of how a certain *behaviour* should work, not a list of observations of everything the code happens to do.

It is acceptable to test implementation details when we are "leaving" the system under test.
That is to say, if we are making an ajax request or querying the database, it is acceptable to
make sure that functions were invoked with specific parameters or called x number of times.

**:(**

```js
it('should multiply the number passed as parameter and subtract one', () => {
  const multiplySpy = spyOn(Calculator, 'multiple').and.callThrough();
  const subtractSpy = spyOn(Calculator, 'subtract').and.callThrough();

  const result = Calculator.compute(21.5);

  expect(multiplySpy).toHaveBeenCalledWith(21.5, 2);
  expect(subtractSpy).toHaveBeenCalledWith(43, 1);
  expect(result).toBe(42);
});
```

**:)**

```js
it('should multiply the number passed as parameter and subtract one', () => {
  const result = Calculator.compute(21.5);
  expect(result).toBe(42);
});
```

This will improve maintainability. Your test is no longer tied to implementation details.

• [Back to ToC](#-table-of-contents) •

### Properly setup the actions that apply to all the tests involved

**:(**

```js
describe('Saving the user profile', () => {
  let profileModule;
  let notifyUserSpy;
  let mockOnComplete;

  beforeEach(() => {
    profileModule = new ProfileModule();
    notifyUserSpy = jest.spyOn<any, any>(profileModule, 'notifyUser');
    mockOnComplete = jest.fn();
  });

  it('should send the updated profile data to the server', () => {
    profileModule.save();

    expect(notifyUserSpy).toHaveBeenCalledWith({
        url: '/profiles/1',
        method: 'POST',
        data: {
        	username: 'mawrkus',
        }
    });
  });

  it('should notify the user', () => {
    profileModule.save();
    expect(notifyUserSpy).toHaveBeenCalledTimes(1);
  });

  it('should properly execute the callback passed as parameter', () => {
    profileModule.save(onCompleteSpy);
    expect(onCompleteSpy).toHaveBeenCalledTimes(1);
  });
});
```

The setup code should apply to all the tests:

**:)**

```ts
describe('Saving the user profile', () => {
  let profileModule;

  beforeEach(() => {
    profileModule = new ProfileModule();
  });

  it('should send the updated profile data to the server', () => {
    const result = profileModule.save();
    expect(result.username).toBe('theUserName');
    expect(result.description).toBe('this is a description');

  });

  it('should notify the user', () => {
    jest.spyOn(profileModule, 'notifyUser');
    profileModule.save();

    expect(profileModule.notifyUser).toHaveBeenCalled();
  });

  it('should properly execute the callback passed as parameter', () => {
    const onCompleteSpy = jest.fn();

    profileModule.save(onCompleteSpy);

    expect(onCompleteSpy).toHaveBeenCalled();
  });
});
```

Consider keeping the setup code minimal to preserve readability and maintainability.

• [Back to ToC](#-table-of-contents) •

### Consider using factory functions in the tests

Factories can:

+ help reduce the setup code, especially if you use dependency injection
+ make each test more readable, since the creation is a single function call that can be in the test itself instead of the setup
+ provide flexibility when creating new instances (setting an initial state, for example)

There's a trade-off to find here between applying the DRY principle and readability.

**:(**

```js
describe('User profile module', () => {
  let profileModule;
  let pubSub;

  beforeEach(() => {
    const element = document.getElementById('my-profile');
    pubSub = new PubSub({ sync: true });

    profileModule = new ProfileModule({
      element,
      pubSub,
      likes: 0
    });
  });

  it('should publish a topic when a new "like" is given', () => {
    jest.spyOn(pubSub, 'notify');
    profileModule.incLikes();
    expect(pubSub.notify).toHaveBeenCalledWith('likes:inc', { count: 1 });
  });

  it('should retrieve the correct number of likes', () => {
    profileModule.incLikes();
    profileModule.incLikes();
    expect(profileModule.getLikes()).toBe(2);
  });
});
```

**:)**

```js
describe('User profile module', () => {
  function createProfileModule({
    element = document.getElementById('my-profile'),
    likes = 0,
    pubSub = new PubSub({ sync: true })
  }) {
    return new ProfileModule({ element, likes, pubSub });
  }

  it('should publish a topic when a new "like" is given', () => {
    const pubSub = {
    	notify: jest.fn(),
    };
    const profileModule = createProfileModule({ pubSub });

    profileModule.incLikes();

    expect(pubSub.notify).toHaveBeenCalledWith('likes:inc');
  });

  it('should retrieve the correct number of likes', () => {
    const profileModule = createProfileModule({ likes: 40 });

    profileModule.incLikes();
    profileModule.incLikes();

    expect(profileModule.getLikes()).toBe(42);
  });
});
```

• [Back to ToC](#-table-of-contents) •

### Know your testing framework API

The API documentation of the testing framework/library should be your bedside book!

Having a good knowledge of the API can help you in reducing the size/complexity of your test code and, in general, help you during development. A simple example:

**:(**

```js
it('should call a method with the proper arguments', () => {
  const foo = {
    bar: jest.fn(),
    baz: jest.fn(),
  };

  foo.bar('qux');

  expect(foo.bar).toHaveBeenCalled();
  expect(foo.bar.calls[0][0]).toEqual('qux');
});

/*it('should do more but not now', () => {
});

it('should do much more but not now', () => {
});*/
```

**:)**

```js
it.only('should call once a method with the proper arguments', () => {
	const foo = {
		bar: jest.fn(),
		baz: jest.fn(),
	};

  foo.bar('baz');

  expect(foo.bar).toHaveBeenCalledWith('baz');
});

it('should do something else but not now', () => {
});

it('should do something else but not now', () => {
});
```

#### Note

The handy `.only` function used in the example above allows you to execute only one test without having to comment out all the tests below. `describe.only` does the same for test suites. This could help save a lot of time when developing.

• [Back to ToC](#-table-of-contents) •

### Don't test multiple concerns in the same test

If a method has several end results, each one should be tested separately. Whenever a bug occurs, it will help you locate the source of the problem.

**:(**

```js
it('should send the profile data to the server and update the profile view properly', () => {
  // expect(...)to(...);
  // expect(...)to(...);
});
```

**:)**

```js
it('should send the profile data to the server', () => {
  // expect(...)to(...);
});

it('should update the profile view properly', () => {
  // expect(...)to(...);
});
```

Beware that writing "AND" or "OR" when naming your test smells bad...

• [Back to ToC](#-table-of-contents) •

### Cover the general case and the edge cases

"Strange behaviour" usually happens at the edges... Remember that your tests can be the live documentation of your code.

**:(**

```js
it('should properly calculate a RPN expression', () => {
  const result = RPN('5 1 2 + 4 * - 10 /');
  expect(result).toBe(-0.7);
});
```

**:)**

```js
describe('The RPN expression evaluator', () => {
  it('should return null when the expression is an empty string', () => {
    const result = RPN('');
    expect(result).toBeNull();
  });

  it('should return the same value when the expression holds a single value', () => {
    const result = RPN('42');
    expect(result).toBe(42);
  });

  it('should properly calculate an expression', () => {
    const result = RPN('5 1 2 + 4 * - 10 /');
    expect(result).toBe(-0.7);
  });

  it('should throw an error whenever an invalid expression is passed', () => {
    const compute = () => RPN('1 + - 1');
    expect(compute).toThrow();
  });
});
```

• [Back to ToC](#-table-of-contents) •

### When applying TDD, always start by writing the simplest failing test

**:(**

```js
it('should suppress all chars that appear multiple times', () => {
  expect(keepUniqueChars('Hello Fostonic !!')).toBe('HeFstnic');
});
```

**:)**

```js
it('should return an empty string when passed an empty string', () => {
  expect(keepUniqueChars('')).toBe('');
});
```

From there, start building the functionalities incrementally.

• [Back to ToC](#-table-of-contents) •

### When applying TDD, always make small steps in each test-first cycle

Build your tests suite from the simple case to the more complex ones. Keep in mind the incremental design. Deliver software fast, incrementally, and in short iterations.

**:(**

```js
it('should return null when the expression is an empty string', () => {
  const result = RPN('');
  expect(result).toBeNull();
});

it('should properly calculate a RPN expression', () => {
  const result = RPN('5 1 2 + 4 * - 10 /');
  expect(result).toBe(-0.7);
});
```

**:)**

```js
describe('The RPN expression evaluator', () => {
  it('should return null when the expression is an empty string', () => {
    const result = RPN('');
    expect(result).toBeNull();
  });

  it('should return the same value when the expression holds a single value', () => {
    const result = RPN('42');
    expect(result).toBe(42);
  });

  describe('Additions-only expressions', () => {
    it('should properly calculate a simple addition', () => {
      const result = RPN('41 1 +');
      expect(result).toBe(42);
    });

    it('should properly calculate a complex addition', () => {
      const result = RPN('2 9 + 15 3 + + 7 6 + +');
      expect(result).toBe(42);
    });
  });

  // ...

  describe('Complex expressions', () => {
    it('should properly calculate an expression containing all 4 operators', () => {
      const result = RPN('5 1 2 + 4 * - 10 /');
      expect(result).toBe(-0.7);
    });
  });
});
```

• [Back to ToC](#-table-of-contents) •

### Test the behaviour, not the internal implementation

**:(**

```js
it('should add a user in memory', () => {
  userManager.addUser('Dr. Falker', 'Joshua');

  expect(userManager._users[0].name).toBe('Dr. Falker');
  expect(userManager._users[0].password).toBe('Joshua');
});
```

A better approach is to test at the same level of the API:

**:)**

```js
it('should add a user in memory', () => {
  userManager.addUser('Dr. Falker', 'Joshua');

  expect(userManager.loginUser('Dr. Falker', 'Joshua')).toBe(true);
});
```

Pro:

+ Changing the internal implementation of a class/object will not necessarily force you to refactor the tests

Con:

+ If a test is failing, we might have to debug to know which part of the code needs to be fixed

Here, a balance has to be found, unit-testing some key parts can be beneficial.

• [Back to ToC](#-table-of-contents) •

### Don't mock everything

**:(**

```js
describe('when the user has already visited the page', () => {
  // storage.getItem('page-visited', '1') === '1'
  describe('when the survey is not disabled', () => {
    // storage.getItem('survey-disabled') === null
    it('should display the survey', () => {
      const storage = jasmine.createSpyObj('storage', ['setItem', 'getItem']);
      storage.getItem.and.returnValue('1'); // ouch.

      const surveyManager = new SurveyManager(storage);
      spyOn(surveyManager, 'display');

      surveyManager.start();

      expect(surveyManager.display).toHaveBeenCalled();
    });
  });

  // ...
});
```

This test fails, because the survey is considered disabled. Let's fix this:

**:)**

```js
describe('when the user has already visited the page', () => {
  // storage.getItem('page-visited', '1') === '1'
  describe('when the survey is not disabled', () => {
    // storage.getItem('survey-disabled') === null
    it('should display the survey', () => {
      const storage = {
      	setItem: jest.fn(),
        getItem: jest.fn(),
      };
      
      storage.getItem.mockImplementation((key) => {
        switch (key) {
          case 'page-visited':
            return '1';

          case 'survey-disabled':
            return null;
        }

        return null;
      });

      const surveyManager = new SurveyManager(storage);
      spyOn(surveyManager, 'display');

      surveyManager.start();

      expect(surveyManager.display).toHaveBeenCalled();
    });
  });

  // ...
});
```

This will work... but needs a lot of code. Let's try a simpler approach:

**:(**

```js
describe('when the user has already visited the page', () => {
  // storage.getItem('page-visited', '1') === '1'
  describe('when the survey is not disabled', () => {
    // storage.getItem('survey-disabled') === null
    it('should display the survey', () => {
      const storage = window.localStorage; // ouch.
      storage.setItem('page-visited', '1');

      const surveyManager = new SurveyManager();
      spyOn(surveyManager, 'display');

      surveyManager.start();

      expect(surveyManager.display).toHaveBeenCalled();
    });
  });

  // ...
});
```

We created a permanent storage of data. What happens if we do not properly clean it?
We might affect the other tests. Let's fix this:

**:) :)**

```js
describe('when the user has already visited the page', () => {
  // storage.getItem('page-visited', '1') === '1'
  describe('when the survey is not disabled', () => {
    // storage.getItem('survey-disabled') === null
    it('should display the survey', () => {
      const storage = new MemoryStorage(); // see https://github.com/tatsuyaoiw/webstorage
      storage.setItem('page-visited', '1');

      const surveyManager = new SurveyManager(storage);
      spyOn(surveyManager, 'display');

      surveyManager.start();

      expect(surveyManager.display).toHaveBeenCalled();
    });
  });
});
```

The `MemoryStorage` used here does not persist data. Nice and easy, with no side effects.

#### Takeaway

The idea to keep in mind is that *dependencies can still be "real" objects*. Don't mock everything because you can.
In particular, consider using the "real" version of the objects if:

+ it leads to a simple, nice and easy tests setup
+ it does not create a shared state between the tests, causing unexpected side effects
+ the code being tested does not make AJAX requests, API calls or browser page reloads
+ the speed of execution of the tests stays *within the limits you fixed*

• [Back to ToC](#-table-of-contents) •

### Create new tests for every defect

Whenever a bug is found, create a test that replicates the problem **before touching any code**. From there, you can apply TDD as usual to fix it.

• [Back to ToC](#-table-of-contents) •

### Don't write unit tests for complex user interactions

Examples of complex user interactions:

+ Filling a form, drag and dropping some items then submitting the form
+ Clicking a tab, clicking an image thumbnail then navigating through a gallery of images previously loaded from a database
+ (...)

These interactions might involve many units of work and should be handled at a higher level by **functional tests**. They will take more time to execute. They could be flaky (false negatives) and they need debugging whenever a failure is reported.

For functional testing, consider using a test automation framework ([Selenium](http://docs.seleniumhq.org/), ...) or QA manual testing.

• [Back to ToC](#-table-of-contents) •

### Test simple user actions

Example of simple user actions:

+ Clicking on a link that toggles the visibility of a DOM element
+ Submitting a form that triggers the form validation
+ (...)

These actions can be easily tested **by simulating DOM events**, for example:

```js
describe('clicking on the "Preview profile" link', () => {
  it('should show the profile preview if it is hidden', () => {
    const previewLink = document.createElement('a');
    const profileModule = createProfileModule({ previewLink, previewIsVisible: false });

    spyOn(profileModule, 'showPreview');

    click(previewLink);

    expect(profileModule.showPreview).toHaveBeenCalled();
  });

  it('should hide the profile preview if it is displayed', () => {
    const previewLink = document.createElement('a');
    const profileModule = createProfileModule({ previewLink, previewIsVisible: true });

    spyOn(profileModule, 'hidePreview');

    click(previewLink);

    expect(profileModule.hidePreview).toHaveBeenCalled();
  });
});
```

Note how simple the test is because the UI (DOM) layer does not mix with the business logic layer:

+ a "click" event occurs
+ a public method is called

The next step could be to test the business logic implemented in "showPreview()" or "hidePreview()".

• [Back to ToC](#-table-of-contents) •

### Review test code first

When reviewing code, always start by reading the code of the tests. Tests are mini use cases of the code that you can drill into.

It will help you understand the intent of the developer very quickly (could be just by looking at the names of the tests).

• [Back to ToC](#-table-of-contents) •

### Practice code katas, learn with pair programming

Because experience is the _only_ teacher. Ultimately, greatness comes from practicing; applying the theory over and over again, using feedback to get better every time.

• [Back to ToC](#-table-of-contents) •

## 📙 Resources

### Best practices

+ Roy Osherove - "JS Unit Testing Good Practices and Horrible Mistakes": https://www.youtube.com/watch?v=iP0Vl-vU3XM
+ Steven Sanderson - "Writing Great Unit Tests: Best and Worst Practices": http://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/
+ Rebecca Murphy - "Writing Testable JavaScript": http://alistapart.com/article/writing-testable-javascript
+ YUI Team - "Writing Effective JavaScript Unit Tests with YUI Test": http://yuiblog.com/blog/2009/01/05/effective-tests/
+ Colin Snover - "Testable code best practices": http://www.sitepen.com/blog/2014/07/11/testable-code-best-practices/
+ Miško Hevery - "The Clean Code Talks -- Unit Testing": https://www.youtube.com/watch?v=wEhu57pih5w
+ José Armesto - "Unit Testing sucks (and it’s our fault)": https://www.youtube.com/watch?v=GZ9iZsMAZFQ
+ TDD - From the Inside Out or the Outside In?: https://8thlight.com/blog/georgina-mcfadyen/2016/06/27/inside-out-tdd-vs-outside-in.html

### Clean code

+ Clean code cheat sheet: http://www.planetgeek.ch/2014/11/18/clean-code-cheat-sheet-v-2-4/
+ Addy Osmani - "Learning JavaScript Design Patterns": http://addyosmani.com/resources/essentialjsdesignpatterns/book/

### BDD

+ Enrique Amodeo - "Learning Behavior-driven Development with JavaScript": https://www.packtpub.com/application-development/learning-behavior-driven-development-javascript

### Events

+ Assert(js) Testing Conference 2018: https://www.youtube.com/playlist?list=PLZ66c9_z3umNSrKSb5cmpxdXZcIPNvKGw

### Libraries

+ Jasmine: https://jasmine.github.io/
+ Jest: https://jestjs.io/
+ Mocha: https://mochajs.org/
+ Tape: https://github.com/substack/tape

• [Back to ToC](#-table-of-contents) •
