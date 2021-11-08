# A guide to unit testing in JavaScript

**This is a living document. New ideas are always welcome. Contribute: fork, clone, branch, commit, push, pull request**

> All the information provided has been compiled & adapted from many references, some of them cited at the end of the document.
> The guidelines are illustrated by my own examples, fruit of my personal experience writing and reviewing unit tests.
> Many thanks to all of the sources of information & contributors.

📅 _Last edit: November 2021._

## 📖 Table of contents

1. General principles

- [Unit tests](#unit-tests)
- [Design principles](#design-principles)

2. Guidelines

- [Whenever possible, use TDD](#-whenever-possible-use-tdd)
- [When applying TDD, always start by writing the simplest failing test](#-when-applying-tdd-always-start-by-writing-the-simplest-failing-test)
- [When applying TDD, always make baby steps in each cycle](#-when-applying-tdd-always-make-baby-steps-in-each-cycle)
- [Structure your tests properly](#-structure-your-tests-properly)
- [Name your tests properly](#-name-your-tests-properly)
- [Use the Arrange-Act-Assert pattern](#-use-the-arrange-act-assert-pattern)
- [Avoid logic in your tests](#-avoid-logic-in-your-tests)
- [Don't write unnecessary expectations](#-dont-write-unnecessary-expectations)
- [Test the behaviour, not the internal implementation](#-test-the-behaviour-not-the-internal-implementation)
- [Consider using factory functions](#-consider-using-factory-functions)
- [Don't test multiple concerns in the same test](#-dont-test-multiple-concerns-in-the-same-test)
- [Cover the general case and the edge cases](#-cover-the-general-case-and-the-edge-cases)
- [Use dependency injection](#-use-dependency-injection)
- [Don't mock everything](#-dont-mock-everything)
- [Don't write unit tests for complex user interactions](#-dont-write-unit-tests-for-complex-user-interactions)
- [Test simple user actions](#-test-simple-user-actions)
- [Create new tests for every defect](#-create-new-tests-for-every-defect)
- [Don't comment out tests](#-dont-comment-out-tests)
- [Know your testing framework API](#-know-your-testing-framework-api)
- [Review test code first](#-review-test-code-first)
- [Practice code katas, learn with pair programming](#-practice-code-katas-learn-with-pair-programming)

3. [Resources](#-resources)
4. [Translations](#translations)

## ⛩️ General principles

### Unit tests

**Unit = Unit of work**

The work can involve **multiple methods and classes**, invoked by some public API that can:

- Return a value or throw an exception
- Change the state of the system
- Make 3rd party calls (API, database, ...)

A unit test should test the behaviour of a unit of work: for a given input, it expects an end result that can be any of the above.

**Unit tests are isolated and independent of each other**

- Any given behaviour should be specified in **one and only one test**
- The execution/order of execution of one test **cannot affect the others**

The code is designed to support this independence (see "Design principles" below).

**Unit tests are lightweight tests**

- Repeatable
- Fast
- Consistent
- Easy to write and read

**Unit tests are code too**

They should meet the same level of quality as the code being tested. They can also be refactored to be more maintainable and readable.

• [Back to ToC](#-table-of-contents) •

### Design principles

The key to good unit testing is to write **testable code**. Applying simple design principles can help, in particular:

- Use a **good, consistent naming convention**.
- **D**on't **R**epeat **Y**ourself: avoid code duplication.
- **Single responsibility**: each software component (function, class, component, module) should focus on a single task.
- Keep a **single level of abstraction** in the same component. For example, do not mix business logic with lower-level technical details in the same method.
- **Minimize dependencies** between components: encapsulate, interchange less information between components.
- **Support configurability** rather than hard-coding: to prevent having to replicate the exact same environment when testing.
- Apply adequate **design patterns**: especially **dependency injection**, to be able to easily substitue the component's dependencies when testing.
- **Avoid global mutable state**: to keep things easy to understand and predictable.

• [Back to ToC](#-table-of-contents) •

## 🧭 Guidelines

The goal of these guidelines is to make your tests:

- **Readable**
- **Maintainable**
- **Trustworthy**

These are the 3 pillars of good unit testing.

All the following examples assume the usage of the [Jest testing framework](https://jestjs.io/).

• [Back to ToC](#-table-of-contents) •

### ✨ Whenever possible, use TDD

**Test-Driven Development is a design process, not a testing process.** It's a highly-iterative process in which you design, test, and code more or less at the same time. It goes like this:

1. **Think:** Figure out what test will best move your code towards completion. (Take as much time as you need. This is the hardest step for beginners.)
2. **Red:** Write a very small amount of test code. Only a few lines... Run the tests and watch the new test fail: the test bar should turn red.
3. **Green:** Write a very small amount of production code. Again, usually no more than a few lines of code. Don't worry about design purity or conceptual elegance. Sometimes you can just hardcode the answer. This is okay because you'll be refactoring in a moment. Run the tests and watch them pass: the test bar will turn green.
4. **Refactor:** Now that your tests are passing, you can make changes without worrying about breaking anything. Pause for a moment, look at the code you've written, and ask yourself if you can improve it. Look for duplication and other "code smells." If you see something that doesn't look right, but you're not sure how to fix it, that's okay. Take a look at it again after you've gone through the cycle a few more times. (Take as much time as you need on this step.) After each little refactoring, run the tests and make sure they still pass.
5. **Repeat:** Do it again. You'll repeat this cycle dozens of times in an hour. Typically, you'll run through several cycles (three to five) very quickly, then find yourself slowing down and spending more time on refactoring. Then you'll speed up again.

This process works well for two reasons:

1. You're working in baby steps, constantly forming hypotheses and checking them. Whenever you make a mistake, you catch it right away. It's only been a few lines of code since you made the mistake, which makes the mistake very easy to find and fix. We all know that finding mistakes, not fixing them, is the most expensive part of programming.
2. You're always thinking about design. Either you're deciding which test you're going to write next, which is an interface design process, or you're deciding how to refactor, which is a code design process. All of this thought on design is immediately tested by turning it into code, which very quickly shows you if the design is good or bad.

Notice also how code written without a test-first approach is often very hard to test!

• [Back to ToC](#-table-of-contents) •

### ✨ When applying TDD, always start by writing the simplest failing test

**:(**

```js
it("should properly calculate a RPN expression", () => {
  const result = RPN("5 1 2 + 4 * - 10 /");
  expect(result).toBe(-0.7);
});
```

**:)**

```js
it("should return null when the expression is an empty string", () => {
  const result = RPN("");
  expect(result).toBeNull();
});
```

From there, start building the functionalities incrementally.

• [Back to ToC](#-table-of-contents) •

### ✨ When applying TDD, always make baby steps in each cycle

Build your tests suite from the simple case to the more complex ones. Keep in mind the incremental design. Deliver new code fast, incrementally, and in short iterations:

**:(**

```js
it("should return null when the expression is an empty string", () => {
  const result = RPN("");
  expect(result).toBeNull();
});

it("should properly calculate a RPN expression", () => {
  const result = RPN("5 1 2 + 4 * - 10 /");
  expect(result).toBe(-0.7);
});
```

**:)**

```js
describe("The RPN expression evaluator", () => {
  it("should return null when the expression is an empty string", () => {
    const result = RPN("");
    expect(result).toBeNull();
  });

  it("should return the same value when the expression holds a single value", () => {
    const result = RPN("42");
    expect(result).toBe(42);
  });

  describe("Additions", () => {
    it("should properly calculate a simple addition", () => {
      const result = RPN("41 1 +");
      expect(result).toBe(42);
    });

    // ...

    it("should properly calculate a complex addition", () => {
      const result = RPN("2 9 + 15 3 + + 7 6 + +");
      expect(result).toBe(42);
    });
  });

  // ...

  describe("Complex expressions", () => {
    it("should properly calculate an expression containing all 4 operators", () => {
      const result = RPN("5 1 2 + 4 * - 10 /");
      expect(result).toBe(-0.7);
    });
  });
});
```

• [Back to ToC](#-table-of-contents) •

### ✨ Structure your tests properly

Don't hesitate to nest your suites to structure logically your tests in subsets:

**:(**

```js
describe("A set of functionalities", () => {
  it("a set of functionalities should do something nice", () => {});

  it("a subset of functionalities should do something great", () => {});

  it("a subset of functionalities should do something awesome", () => {});

  it("another subset of functionalities should also do something great", () => {});
});
```

**:)**

```js
describe("A set of functionalities", () => {
  it("should do something nice", () => {});

  describe("A subset of functionalities", () => {
    it("should do something great", () => {});

    it("should do something awesome", () => {});
  });

  describe("Another subset of functionalities", () => {
    it("should also do something great", () => {});
  });
});
```

• [Back to ToC](#-table-of-contents) •

### ✨ Name your tests properly

Tests names should be concise, explicit, descriptive and in correct English. Read the output of the test runner and verify that it is understandable!

Keep in mind that someone else will read it too and that tests can be the live documentation of the code:

**:(**

```js
describe("myGallery", () => {
  it("init set correct property when called (thumb size, thumbs count)", () => {});
});
```

**:)**

```js
describe("The Gallery instance", () => {
  it("should properly calculate the thumb size when initialized", () => {});

  it("should properly calculate the thumbs count when initialized", () => {});
});
```

In order to help you write test names properly, you can use the **"unit of work - scenario/context - expected behaviour"** pattern:

```js
describe("[unit of work]", () => {
  it("should [expected behaviour] when [scenario/context]", () => {});
});
```

Or if there are many tests that follow the same scenario or are related to the same context:

```js
describe("[unit of work]", () => {
  describe("when [scenario/context]", () => {
    it("should [expected behaviour]", () => {});
  });
});
```

For example:

**:) :)**

```js
describe("The Gallery instance", () => {
  describe("when initialized", () => {
    it("should properly calculate the thumb size", () => {});

    it("should properly calculate the thumbs count", () => {});
  });
});
```

You might also want to use this pattern to describe a class and its methods:

```js
describe("Gallery", () => {
  describe("init()", () => {
    it("should properly calculate the thumb size", () => {});

    it("should properly calculate the thumbs count", () => {});
  });

  describe("goTo(index)", () => {});

  // ...
});
```

• [Back to ToC](#-table-of-contents) •

### ✨ Use the Arrange-Act-Assert pattern

This pattern is a good support to help you read and understand tests more easily:

- The **arrange** part is where you set up the objects to be tested: initializing input variables, setting up spies, etc.
- The **act** part is where you act upon the code under test: calling a function or a class method, storing the result, ...
- The **assert** part is where you test your expectations.

```js
describe("Gallery", () => {
  describe("goTo(index)", () => {
    it("should display the proper image", () => {
      // arrange
      const myGallery = new Gallery();
      const index = 1;

      // act
      myGallery.goTo(index);

      // assert
      expect(document.getElementById("image-1")).toBeVisible();
    });
  });
});
```

This pattern is also named "Given-When-Then" or "Setup-Exercise-Verify".

• [Back to ToC](#-table-of-contents) •

### ✨ Avoid logic in your tests

Always use simple statements. Don't use loops and/or conditionals. If you do, you add a possible entry point for bugs in the test itself:

- Conditionals: you don't know which path the test will take
- Loops: you could be sharing state between tests

• [Back to ToC](#-table-of-contents) •

### ✨ Don't write unnecessary expectations

Remember, unit tests are a design specification of how a certain _behaviour_ should work, not a list of observations of everything the code happens to do:

**:(**

```js
it("should compute the result properly", () => {
  const multiplySpy = jest.spyOn(Calculator, "multiple");
  const subtractSpy = jest.spyOn(Calculator, "subtract");

  const result = Calculator.compute("(21.5 x 2) - 1");

  expect(multiplySpy).toHaveBeenCalledWith(21.5, 2);
  expect(subtractSpy).toHaveBeenCalledWith(43, 1);
  expect(result).toBe(42);
});
```

**:)**

```js
it("should compute the result properly", () => {
  const result = Calculator.compute("(21.5 x 2) - 1");

  expect(result).toBe(42);
});
```

• [Back to ToC](#-table-of-contents) •

### ✨ Test the behaviour, not the internal implementation

**:(**

```js
it("should add a user in memory", () => {
  userManager.addUser("Dr. Falker", "Joshua");

  expect(userManager._users[0].name).toBe("Dr. Falker");
  expect(userManager._users[0].password).toBe("Joshua");
});
```

A better approach is to test at the same level of the API:

**:)**

```js
it("should add a user in memory", () => {
  userManager.addUser("Dr. Falker", "Joshua");

  expect(userManager.loginUser("Dr. Falker", "Joshua")).toBe(true);
});
```

Pro:

- Changing the internal implementation will not necessarily force you to refactor the tests

Con:

- If a test is failing, you might have to debug to know which part of the code needs to be fixed

Here, a balance has to be found, unit-testing some key parts can be beneficial.

• [Back to ToC](#-table-of-contents) •

### ✨ Consider using factory functions

Factories can:

- help reduce the setup code, especially if you use dependency injection
- make each test more readable by favoring cohesion, since the creation is a single function call in the test itself instead of the setup
- provide flexibility when creating new instances (setting an initial state, for example)

**:(**

```js
describe("User profile module", () => {
  let profileModule;
  let pubSub;

  beforeEach(() => {
    const element = document.getElementById("my-profile");

    pubSub = { notify() {} };

    profileModule = new ProfileModule({
      element,
      pubSub,
      likes: 0,
    });
  });

  it('should publish a topic when a new "like" is given', () => {
    jest.spyOn(pubSub, "notify");

    profileModule.incLikes();

    expect(pubSub.notify).toHaveBeenCalledWith("likes:inc", { count: 1 });
  });

  it("should retrieve the correct number of likes", () => {
    profileModule.incLikes();
    profileModule.incLikes();

    expect(profileModule.getLikes()).toBe(2);
  });
});
```

**:)**

```js
describe("User profile module", () => {
  function createProfileModule({ likesCount = 0 } = {}) {
    const element = document.getElementById("my-profile"),;
    const pubSub = { notify: jest.fn() };

    const profileModule = new ProfileModule({
      element,
      pubSub
      likesCount,
    });

    return {
      pubSub,
      profileModule,
    };
  }

  it('should publish a topic when a new "like" is given', () => {
    const {
      profileModule,
      pubSub,
    } = createProfileModule();

    profileModule.incLikes();

    expect(pubSub.notify).toHaveBeenCalledWith("likes:inc");
  });

  it("should retrieve the correct number of likes", () => {
    const { profileModule } = createProfileModule({ likes: 40 });

    profileModule.incLikes();
    profileModule.incLikes();

    expect(profileModule.getLikes()).toBe(42);
  });
});
```

Factories can be particularly useful when dealing with the DOM:

**:(**

```js
describe("The search component", () => {
  describe("when the search button is clicked", () => {
    let container;
    let form;
    let searchInput;
    let submitInput;

    beforeEach(() => {
      fixtures.inject(`<div id="container">
        <form class="js-form" action="/search">
          <input type="search">
          <input type="submit" value="Search">
        </form>
      </div>`);

      container = document.getElementById("container");
      form = container.getElementsByClassName("js-form")[0];
      searchInput = form.querySelector("input[type=search]");
      submitInput = form.querySelector("input[type=submith]");
    });

    it("should validate the text entered", () => {
      const search = new Search({ container });
      jest.spyOn(search, "validate");

      search.init();

      input(searchInput, "peace");
      click(submitInput);

      expect(search.validate).toHaveBeenCalledWith("peace");
    });
  });
});
```

**:)**

```js
function createHTMLFixture() {
  fixtures.inject(`<div id="container">
    <form class="js-form" action="/search">
      <input type="search">
      <input type="submit" value="Search">
    </form>
  </div>`);

  const container = document.getElementById("container");
  const form = container.getElementsByClassName("js-form")[0];
  const searchInput = form.querySelector("input[type=search]");
  const submitInput = form.querySelector("input[type=submith]");

  return {
    container,
    form,
    searchInput,
    submitInput,
  };
}

describe("The search component", () => {
  describe("when the search button is clicked", () => {
    it("should validate the text entered", () => {
      const { container, searchInput, submitInput } = createHTMLFixture();

      const search = new Search({ container });

      jest.spyOn(search, "validate");

      search.init();

      input(searchInput, "peace");
      click(submitInput);

      expect(search.validate).toHaveBeenCalledWith("peace");
    });
  });
});
```

Notice that there's a trade-off to find between applying the DRY principle and readability.

• [Back to ToC](#-table-of-contents) •

### ✨ Don't test multiple concerns in the same test

If a method has several end results, each one should be tested separately so that whenever a bug occurs, it will help you locate the source of the problem directly:

**:(**

```js
it("should send the profile data to the API and update the profile view properly", () => {
  // expect(...)to(...);
  // expect(...)to(...);
});
```

**:)**

```js
it("should send the profile data to the API", () => {
  // expect(...)to(...);
});

it("should update the profile view properly", () => {
  // expect(...)to(...);
});
```

Pay attention when writing "and" or "or" in your test names ;)

• [Back to ToC](#-table-of-contents) •

### ✨ Cover the general case and the edge cases

Because "strange behaviour" usually happens at the edges... And also because tests can be the live documentation of the code. Having edge cases covered will help the future reader fully understand what the code does:

**:(**

```js
it("should properly calculate a RPN expression", () => {
  const result = RPN("5 1 2 + 4 * - 10 /");
  expect(result).toBe(-0.7);
});
```

**:)**

```js
describe("The RPN expression evaluator", () => {
  it("should return null when the expression is an empty string", () => {
    const result = RPN("");
    expect(result).toBeNull();
  });

  it("should return the same value when the expression holds a single value", () => {
    const result = RPN("42");
    expect(result).toBe(42);
  });

  it("should properly calculate an expression", () => {
    const result = RPN("5 1 2 + 4 * - 10 /");
    expect(result).toBe(-0.7);
  });

  it("should throw an error whenever an invalid expression is passed", () => {
    const compute = () => RPN("1 + - 1");
    expect(compute).toThrow();
  });
});
```

• [Back to ToC](#-table-of-contents) •

### ✨ Use dependency injection

**:(**

```js
describe("when the user has already visited the page", () => {
  // storage.getItem('page-visited', '1') === '1'
  describe("when the survey is not disabled", () => {
    // storage.getItem('survey-disabled') === null
    it("should display the survey", () => {
      const storage = window.localStorage;
      storage.setItem("page-visited", "1");
      storage.setItem("survey-disabled", "1");

      const surveyManager = new SurveyManager();
      jest.spyOn(surveyManager, "display");

      surveyManager.start();

      expect(surveyManager.display).toHaveBeenCalled();
    });
  });
});
```

We created a permanent storage of data. What happens if we do not properly clean it between tests? We might affect the result of other tests. By using dependency injection, we can prevent this behaviour:

**:)**

```js
describe("when the user has already visited the page", () => {
  // storage.getItem('page-visited', '1') === '1'
  describe("when the survey is not disabled", () => {
    // storage.getItem('survey-disabled') === null
    it("should display the survey", () => {
      // E.g. https://github.com/tatsuyaoiw/webstorage
      const storage = new MemoryStorage();
      storage.setItem("page-visited", "1");
      storage.setItem("survey-disabled", "1");

      const surveyManager = new SurveyManager(storage);
      jest.spyOn(surveyManager, "display");

      surveyManager.start();

      expect(surveyManager.display).toHaveBeenCalled();
    });
  });
});
```

• [Back to ToC](#-table-of-contents) •

### ✨ Don't mock everything

The idea to keep in mind is that **dependencies can still be real objects**. Don't mock everything because you can. Consider using the real version if:

- it leads to a simple, nice and easy tests setup,
- it does not create a shared state between the tests, causing unexpected side effects,
- the code being tested does not make HTTP requests, API calls or browser page reloads,
- the speed of execution of the tests stays within the limits you fixed.

• [Back to ToC](#-table-of-contents) •

### ✨ Don't write unit tests for complex user interactions

Examples of complex user interactions:

- Filling a form, drag and dropping some items then submitting the form.
- Clicking a tab, clicking an image thumbnail then navigating through a gallery of images loaded on-demand from an API.

These interactions involve many units of work and should be handled at a higher level by **end-to-end tests**. They will usually take more time to execute, they could be flaky (false negatives) and they will require debugging whenever a failure is reported.

For these complex user scenarios, consider using a test automation framework like the excellent [Cypress](https://www.cypress.io/), or manual QA testing.

• [Back to ToC](#-table-of-contents) •

### ✨ Test simple user actions

Example of simple user actions:

- Clicking on a link that toggles the visibility of a DOM element
- Clicking on a button that performs an API call (like sending a tracking event).

These actions can be easily tested **by simulating DOM events**, for example:

```js
describe('clicking on the "Preview profile" link', () => {
  it("should show the profile preview if it is hidden", () => {
    const { profileModule, previewLink } = createProfileModule({
      previewIsVisible: false,
    });

    jest.spyOn(profileModule, "showPreview");

    click(previewLink);

    expect(profileModule.showPreview).toHaveBeenCalled();
  });

  it("should hide the profile preview if it is visible", () => {
    const { profileModule, previewLink } = createProfileModule({
      previewIsVisible: true,
    });

    jest.spyOn(profileModule, "hidePreview");

    click(previewLink);

    expect(profileModule.hidePreview).toHaveBeenCalled();
  });
});
```

Note how simple the tests are because the UI (DOM) layer does not mix with the business logic layer:

- a "click" event occurs
- a public method is called

The next step could be to test the logic implemented in `showPreview()` or `hidePreview()`.

• [Back to ToC](#-table-of-contents) •

### ✨ Create new tests for every defect

Whenever a bug is found, create a test that replicates the problem **before touching any code**. Then fix it.

• [Back to ToC](#-table-of-contents) •

### ✨ Don't comment out tests

Never. Ever. Tests have a reason to be or not.

Don't comment them out because they are too slow, too complex or produce false negatives. Instead, make them fast, simple and trustworthy. If not, remove them completely.

• [Back to ToC](#-table-of-contents) •

### ✨ Know your testing framework API

Take time to read the API documentation of the testing framework that you have chosen to work with.

Having a good knowledge of the framework will help you in reducing the size and complexity of your test code and, in general, will help you during development.

### ✨ Review test code first

When reviewing code, always start by reading the code of the tests. Tests are mini use cases of the code that you can drill into.

It will help you understand the intent of the developer very quickly (could be just by looking at the names of the tests).

• [Back to ToC](#-table-of-contents) •

### ✨ Practice code katas, learn with pair programming

Because **experience is the only teacher**. Ultimately, greatness comes from practicing; applying the theory over and over again, using feedback to get better every time.

• [Back to ToC](#-table-of-contents) •

## 📙 Resources

There's a ton of resources available out there, here are just a few I've found useful...

### Reading

- Yoni Goldberg - "Writing clean JavaScript tests with the BASIC principles": https://yonigoldberg.medium.com/fighting-javascript-tests-complexity-with-the-basic-principles-87b7622eac9a
- Testim - "Unit Testing Best Practices: 9 to Ensure You Do It Right": https://www.testim.io/blog/unit-testing-best-practices/
- Vladimir Khorikov - "DRY vs DAMP in Unit Tests": https://enterprisecraftsmanship.com/posts/dry-damp-unit-tests/
- Georgina McFadyen - "TDD - From the Inside Out or the Outside In?": https://8thlight.com/blog/georgina-mcfadyen/2016/06/27/inside-out-tdd-vs-outside-in.html
- Sandi Metz - "Make Everything The Same": https://sandimetz.com/blog/2016/6/9/make-everything-the-same
- Varun Vachhar - "How to actually test UIs": https://storybook.js.org/blog/how-to-actually-test-uis/
- Rebecca Murphy - "Writing Testable JavaScript": http://alistapart.com/article/writing-testable-javascript
- James Shore - "Red-Green-Refactor
  ": https://www.jamesshore.com/v2/blog/2005/red-green-refactor
- Martin Fowler - "Refactoring": https://refactoring.com/

### Listening

- Making Tech Better Podcast - "When is a test not a test? with Daniel Terhorst-North": https://www.madetech.com/resources/podcasts/episode-18-daniel-terhorst-north/

### Watching

- Assert(js) Conference (2019): https://www.youtube.com/watch?v=UOOuW5tqT8M&list=PLZ66c9_z3umMtAboEKsHXQWB0YMJje7Tl
- Assert(js) Conference (2018): https://www.youtube.com/watch?v=zqdCM8zR6Mc&list=PLZ66c9_z3umNSrKSb5cmpxdXZcIPNvKGw
- James Shore - "TDD Lunch & Learn
  ": https://www.jamesshore.com/v2/projects/lunch-and-learn
- Roy Osherove - "JS Unit Testing Good Practices and Horrible Mistakes": https://www.youtube.com/watch?v=iP0Vl-vU3XM
- José Armesto - "Unit Testing sucks (and it’s our fault)": https://www.youtube.com/watch?v=GZ9iZsMAZFQ

### Tools

- Jest: https://jestjs.io/
- Mocha: https://mochajs.org/
- Node TAP: https://github.com/tapjs/node-tap
- Tape: https://github.com/substack/tape
- Cypress: https://www.cypress.io/

• [Back to ToC](#-table-of-contents) •

### Katas

- https://kata-log.rocks/index.html
- http://codekata.com/
- https://github.com/gamontal/awesome-katas
- https://github.com/cesalberca/katas

## 🌐 Translations

This style guide is also available in other languages:

- 🇨🇳 [Chinese (Simplified)](https://github.com/mawrkus/js-unit-testing-guide/tree/master/translations/zh-cn/README.md) - Thanks to [GabrielchenCN](https://github.com/GabrielchenCN)

• [Back to ToC](#-table-of-contents) •
