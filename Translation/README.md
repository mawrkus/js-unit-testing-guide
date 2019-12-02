# 使用JavaScript进行单元测试的指南

## 这是一个持续维护的文件。新思想总是受欢迎的。贡献可通过:fork, clone, branch, commit, push, pull request

### 免责声明

> 所提供的所有信息均已根据文件末尾引用的参考文献进行编辑和改编。

> 这些指导原则通过我自己的例子、我个人编写和审查单元测试的经验成果来说明。

> 非常感谢所有的信息来源和贡献者。

## 📖 目录

1. 一般原则

  + [单元测试](#unit-tests)
  + [设计原则](#design-principles)
2. 规范

  + [只要可能，就使用TDD](#whenever-possible-use-tdd)
  + [正确地组织测试](#structure-your-tests-properly)
  + [正确地命名您的测试](#name-your-tests-properly)
  + [不要注释掉测试](#dont-comment-out-tests)
  + [在测试中避免逻辑](#avoid-logic-in-your-tests)
  + [不要写不必要的测试期望](#dont-write-unnecessary-expectations)
  + [正确地设置应用于所有相关测试的操作](#properly-setup-the-actions-that-apply-to-all-the-tests-involved)
  + [考虑在测试中使用工厂模式](#consider-using-factory-functions-in-the-tests)
  + [了解你的测试框架API](#know-your-testing-framework-api)
  + [不要在同一个测试中测试多个功能点](#dont-test-multiple-concerns-in-the-same-test)
  + [涵盖一般情况和边缘情况](#cover-the-general-case-and-the-edge-cases)
  + [在应用TDD时，总是从编写最简单的失败测试开始](#when-applying-tdd-always-start-by-writing-the-simplest-failing-test)
  + [在应用TDD时，总是在每个测试优先的周期中执行一些小步骤](#when-applying-tdd-always-make-small-steps-in-each-test-first-cycle)
  + [测试行为，而不是内部实现](#test-the-behaviour-not-the-internal-implementation)
  + [不要模拟所有东西](#dont-mock-everything)
  + [为每个缺陷创建新的测试](#create-new-tests-for-every-defect)
  + [不要为复杂的用户交互编写单元测试](#dont-write-unit-tests-for-complex-user-interactions)
  + [测试简单的用户操作](#test-simple-user-actions)
  + [首先检查测试代码](#review-test-code-first)
  + [练习代码，学习结对编程](#practice-code-katas-learn-with-pair-programming)

3. [资源](#-resources)


## 一般原则

### 单元测试

**Unit = Unit of work**

这可能涉及**多个方法和类**调用一些公共API，可以:

+ 返回一个值或抛出一个异常
+ 改变系统的状态
+ 进行第三方调用(API，数据库，…)

单元测试应该测试工作单元的行为:对于给定的输入，它期望的最终结果可以是上面的任意一个。

**单元测试是相互独立的**

+ 任何给定的行为都应该在**一个且只有一个测试**中指定

+ 一个测试的执行/执行顺序**不会影响其他**

代码的设计目的就是支持这种独立性(参见下面的“设计原则”)。

**单元测试是轻量级测试**

+ 可重复的
+ 快速的
+ 一致的
+ 容易读写的

**单元测试也是代码**

它们应该达到与正在测试的代码相同的质量级别。还可以对它们进行重构，使它们更易于维护和可读。

• [Back to ToC](#user-content-table-of-contents) •

### 设计原则

好的单元测试的关键是编写**可测试代码**。应用简单的设计原则会有所帮助，特别是:

+ 使用**良好的命名**约定和**注释**您的代码(表明“为什么”这样写而不是“如何”这样写)，请记住，注释不能代替糟糕的命名或糟糕的设计

+ **DRY**:不要重复自己，避免代码重复

+ **单一职责**每个对象/函数必须专注于一个任务

+ 在同一组件中保持**单一抽象级别**(例如，不要在同一方法中混合业务逻辑和较低级别的技术细节)

+ 在组件之间使用**最小依赖**:封装组件之间的信息，减少组件之间的信息交换

+ **支持可配置性**而不是硬编码，这避免了在测试时必须复制完全相同的环境(例如:标记)。

+ 应用适当的**设计模式**，特别是**依赖项注入**，它允许将对象的创建职责与业务逻辑分离

+ 避免全局可变状态

• [Back to ToC](#user-content-table-of-contents) •

## 指南

这些指南的目的是让你的测试:

+ **可读**
+ **可维护**
+ **可信赖**

这是好的单元测试的三大支柱。

以下所有示例都假设使用了[Jasmine](http://jasmine.github.io)框架。

• [Back to ToC](#user-content-table-of-contents) •

---------------------------------------

### 只要可能，就使用TDD

TDD是一个设计过程，而不是一个测试过程。TDD是一种健壮的交互设计软件组件(“单元”)的方法，以便通过单元测试指定它们的行为。

怎么做？为什么这样做

#### 测试优先的周期

1. 编写一个简单的失败测试

2. 通过编写最少数量的代码来通过测试，而不必担心代码质量

3. 通过应用设计原则/设计模式重构代码

#### 测试优先周期的结果

+ 首先编写测试用例使代码设计实际上是可测试的
+ 只需编写实现所需功能所需的代码量就可以使生成的代码库最小化，从而提高可维护性
+ 可以使用重构机制来增强代码库，测试使您确信新代码不会修改现有功能
+ 在每个周期中清理代码使代码库更容易维护，频繁地、小幅度地更改代码要简单得多
+ 对开发人员的快速反馈，您知道您没有破坏任何东西，并且您正在朝着一个好的方向发展系统
+ 拥有添加特性、修复bug或探索新设计的信心

注意，不使用测试优先方法编写的代码通常很难测试。

• [Back to ToC](#user-content-table-of-contents) •

### 正确地组织测试

不要犹豫将您的套件嵌套在子集中，以便在逻辑上构造您的测试。

**:(**

```js
// 不好的示范
// 一组功能
describe('A set of functionalities', () => {
  // 一组功能的测试
  it('a set of functionalities should do something nice', () => {
  });
  // 一组功能子集的测试
  it('a subset of functionalities should do something great', () => {
  });

  it('a subset of functionalities should do something awesome', () => {
  });
  // 一组功能另一子集的测试
  it('another subset of functionalities should also do something great', () => {
  });
});
```

**:)**

```js
// 正确的示范
// 一组功能的测试
describe('A set of functionalities', () => {
  it('should do something nice', () => {
  });
  // 一组功能子集的测试
  describe('A subset of functionalities', () => {
    it('should do something great', () => {
    });

    it('should do something awesome', () => {
    });
  });
  // 一组功能另一子集的测试
  describe('Another subset of functionalities', () => {
    it('should also do something great', () => {
    });
  });
});
```

• [Back to ToC](#user-content-table-of-contents) •

### 正确地命名您的测试

测试用例的名称应简洁、明确、描述性强，英文正确。阅读spec runner的输出并验证它是可理解的!记住，别人也会读它。测试可以是代码的实时文档。

**:(**

```js
// 不好的示范
describe('MyGallery', () => {
  it('init set correct property when called (thumb size, thumbs count)', () => {
  });

  // ...
});
```

**:)**

```js
// 正确的示范
describe('The Gallery instance', () => {
  it('should properly calculate the thumb size when initialized', () => {
  });

  it('should properly calculate the thumbs count when initialized', () => {
  });

  // ...
});
```

为了帮助您正确地编写测试名称，您可以使用**“工作单元[unit of work]—场景/上下文[scenario/context]—期望行为[expected behaviour]”**模式:

```js
// 不好的示范
describe('[unit of work]', () => {
  it('should [expected behaviour] when [scenario/context]', () => {
  });
});
```

或者当你有很多测试遵循相同的场景或与相同的上下文相关:

```js
// 正确的示范
describe('[unit of work]', () => {
  describe('when [scenario/context]', () => {
    it('should [expected behaviour]', () => {
    });
  });
});
```

例如:

**:) :)**

```js
// 优秀的示范
describe('The Gallery instance', () => {
  describe('when initialized', () => {
    it('should properly calculate the thumb size', () => {
    });

    it('should properly calculate the thumbs count', () => {
    });
  });

  // ...
});
```

• [Back to ToC](#user-content-table-of-contents) •

### 不要注释掉测试

永不，绝不！测试是有原因的。

不要因为它们太慢、太复杂或产生错误的结果而把它们注释掉。相反，让他们快速，简单和值得信赖。如果没有，就把它们完全移除。

• [Back to ToC](#user-content-table-of-contents) •

### 在测试中避免逻辑

使用简单的语句。不要使用循环或条件语句。如果你这样做了，你就为测试本身添加了一个可能的bug点:

+ 条件:您不知道测试将采用哪条路径
+ 循环:你可能在测试之间共享状态

**:(**

```js
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

```js
it('should properly sanitize strings', () => {
  expect(sanitizeString('Avi'+String.fromCharCode(243)+'n')).toBe('Avion');
  expect(sanitizeString('The space')).toBe('The-space');
  expect(sanitizeString('Weird chars!!')).toBe('Weird-chars-');
  expect(sanitizeString('file name.zip')).toBe('file-name.zip');
  expect(sanitizeString('my.name.zip')).toBe('my-name.zip');
});
```

更好的做法是:为每种sanitizeString方法写一个测试。它将输出所有可能的情况，提高可维护性。

**:) :)**

```js
it('should sanitize a string containing non-ASCII chars', () => {
  expect(sanitizeString('Avi'+String.fromCharCode(243)+'n')).toBe('Avion');
});

it('should sanitize a string containing spaces', () => {
  expect(sanitizeString('The space')).toBe('The-space');
});

it('should sanitize a string containing exclamation signs', () => {
  expect(sanitizeString('Weird chars!!')).toBe('Weird-chars-');
});

it('should sanitize a filename containing spaces', () => {
  expect(sanitizeString('file name.zip')).toBe('file-name.zip');
});

it('should sanitize a filename containing more than one dot', () => {
  expect(sanitizeString('my.name.zip')).toBe('my-name.zip');
});
```

• [Back to ToC](#user-content-table-of-contents) •

### 不要写不必要的测试期望

请记住，单元测试是某个“行为”应该如何工作的设计规范，而不是代码碰巧要做的所有事情的观察列表。

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

这将提高可维护性。您的测试不再与实现细节绑定。

• [Back to ToC](#user-content-table-of-contents) •

### 正确地设置应用于所有相关测试的操作

**:(**

```js
describe('Saving the user profile', () => {
  let profileModule;
  let notifyUserSpy;
  let onCompleteSpy;

  beforeEach(() => {
    profileModule = new ProfileModule();
    notifyUserSpy = spyOn(profileModule, 'notifyUser');
    onCompleteSpy = jasmine.createSpy();
  });

  it('should send the updated profile data to the server', () => {
    jasmine.Ajax.install();

    profileModule.save();

    const request = jasmine.Ajax.requests.mostRecent();

    expect(request.url).toBe('/profiles/1');
    expect(request.method).toBe('POST');
    expect(request.data()).toEqual({ username: 'mawrkus' });

    jasmine.Ajax.uninstall();
  });

  it('should notify the user', () => {
    jasmine.Ajax.install();

    profileModule.save();

    expect(notifyUserSpy).toHaveBeenCalled();

    jasmine.Ajax.uninstall();
  });

  it('should properly execute the callback passed as parameter', () => {
    jasmine.Ajax.install();

    profileModule.save(onCompleteSpy);

    jasmine.Ajax.uninstall();

    expect(onCompleteSpy).toHaveBeenCalled();
  });
});
```

设置代码应该适用于所有的测试:

**:)**

```js
describe('Saving the user profile', () => {
  let profileModule;

  beforeEach(() => {
    jasmine.Ajax.install();
    profileModule = new ProfileModule();
  });

  afterEach( () => {
    jasmine.Ajax.uninstall();
  });

  it('should send the updated profile data to the server', () => {
    profileModule.save();

    const request = jasmine.Ajax.requests.mostRecent();

    expect(request.url).toBe('/profiles/1');
    expect(request.method).toBe('POST');

  });

  it('should notify the user', () => {
    spyOn(profileModule, 'notifyUser');

    profileModule.save();

    expect(profileModule.notifyUser).toHaveBeenCalled();
  });

  it('should properly execute the callback passed as parameter', () => {
    const onCompleteSpy = jasmine.createSpy();

    profileModule.save(onCompleteSpy);

    expect(onCompleteSpy).toHaveBeenCalled();
  });
});
```

考虑将设置代码保持在最小以保持可读性和可维护性

• [Back to ToC](#user-content-table-of-contents) •

### 考虑在测试中使用工厂模式

工厂模式可以:

+ 帮助减少设置代码，特别是在使用依赖项注入时
+ 使每个测试更具可读性，因为创建是一个单独的函数调用，可以在测试本身中而不是在设置中
+ 在创建新实例时提供灵活性(例如，设置初始状态)

在应用DRY原则和可读性之间需要权衡。

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
    spyOn(pubSub, 'notify');
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
    const pubSub = jasmine.createSpyObj('pubSub', ['notify']);
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

工厂模式在处理DOM时特别有用:

**:(**

```js
describe('The search component', () => {
  describe('when the search button is clicked', () => {
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

      container = document.getElementById('container');
      form = container.getElementsByClassName('js-form')[0];
      searchInput = form.querySelector('input[type=search]');
      submitInput = form.querySelector('input[type=submith]');
    });

    it('should validate the text entered', () => {
      const search = new Search({ container });
      spyOn(search, 'validate');

      search.init();

      input(searchInput, 'peace');
      click(submitInput);

      expect(search.validate).toHaveBeenCalledWith('peace');
    });

    // ...
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

  const container = document.getElementById('container');
  const form = container.getElementsByClassName('js-form')[0];
  const searchInput = form.querySelector('input[type=search]');
  const submitInput = form.querySelector('input[type=submith]');

  return {
    container,
    form,
    searchInput,
    submitInput
  };
}

describe('The search component', () => {
  describe('when the search button is clicked', () => {
    it('should validate the text entered', () => {
      const { container, form, searchInput, submitInput } = createHTMLFixture();
      const search = new Search({ container });
      spyOn(search, 'validate');

      search.init();

      input(searchInput, 'peace');
      click(submitInput);

      expect(search.validate).toHaveBeenCalledWith('peace');
    });

    // ...
  });
});
```

• [Back to ToC](#user-content-table-of-contents) •

### 了解你的测试框架API

测试框架/库的API文档应该是你的枕边书!

对API有良好的了解可以帮助您减少测试代码的大小/复杂性，并且通常在开发过程中对您有帮助。一个简单的例子:

**:(**

```js
it('should call a method with the proper arguments', () => {
  const foo = {
    bar: jasmine.createSpy(),
    baz: jasmine.createSpy()
  };

  foo.bar('qux');

  expect(foo.bar).toHaveBeenCalled();
  expect(foo.bar.calls.argsFor(0)).toEqual(['qux']);
});

/*it('should do more but not now', () => {
});

it('should do much more but not now', () => {
});*/
```

**:)**

```js
fit('should call once a method with the proper arguments', () => {
  const foo = jasmine.createSpyObj('foo', ['bar', 'baz']);

  foo.bar('baz');

  expect(foo.bar).toHaveBeenCalledWith('baz');
});

it('should do something else but not now', () => {
});

it('should do something else but not now', () => {
});
```

#### 注意

上面示例中使用的方便的`fit`函数允许您只执行一个测试，而不必注释掉下面的所有测试。`fdescribe`也适用于测试套件。这有助于在开发时节省大量时间。

更多信息，请查看 [Jasmine website](http://jasmine.github.io).

• [Back to ToC](#user-content-table-of-contents) •

### 不要在同一个测试中测试多个功能点

如果一个方法有多个最终结果，那么应该分别测试每个结果。当bug发生时，它将帮助您定位问题的根源。

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

注意，当命名您的测试时写“AND”或“OR”，感觉很不好…

• [Back to ToC](#user-content-table-of-contents) •

### 涵盖一般情况和边缘情况

“奇怪的行为”通常发生在边缘……请记住，测试可以是代码的实时文档。

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

• [Back to ToC](#user-content-table-of-contents) •

### 在应用TDD时，总是从编写最简单的失败测试开始

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

在此基础上，开始逐步构建功能。

• [Back to ToC](#user-content-table-of-contents) •

### 在应用TDD时，总是在每个测试优先的周期中执行一些小步骤

构建您的测试套件，从简单的案例到更复杂的案例。记住增量式设计。快速、增量地、短迭代地交付软件。

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

• [Back to ToC](#user-content-table-of-contents) •

### 测试行为，而不是内部实现

**:(**

```js
it('should add a user in memory', () => {
  userManager.addUser('Dr. Falker', 'Joshua');

  expect(userManager._users[0].name).toBe('Dr. Falker');
  expect(userManager._users[0].password).toBe('Joshua');
});
```

一个更好的方法是在API的同一级别进行测试:

**:)**

```js
it('should add a user in memory', () => {
  userManager.addUser('Dr. Falker', 'Joshua');

  expect(userManager.loginUser('Dr. Falker', 'Joshua')).toBe(true);
});
```

Pro:

+ 更改类/对象的内部实现并不一定会强制您重构测试

Con:

+ 如果测试失败，我们可能必须进行调试才能知道需要修复代码的哪一部分

在这里，必须找到一个平衡，单元测试的一些关键部分可能是有益的。

• [Back to ToC](#user-content-table-of-contents) •

### 不要模拟所有东西

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

此测试失败，因为调查被认为是禁用的。让我们解决这个问题:

**:)**

```js
describe('when the user has already visited the page', () => {
  // storage.getItem('page-visited', '1') === '1'
  describe('when the survey is not disabled', () => {
    // storage.getItem('survey-disabled') === null
    it('should display the survey', () => {
      const storage = jasmine.createSpyObj('storage', ['setItem', 'getItem']);
      storage.getItem.and.callFake(key => {
        switch (key) {
          case 'page-visited':
            return '1';

          case 'survey-disabled':
            return null;
        }

        return null;
      }); // ouch.

      const surveyManager = new SurveyManager(storage);
      spyOn(surveyManager, 'display');

      surveyManager.start();

      expect(surveyManager.display).toHaveBeenCalled();
    });
  });

  // ...
});
```

这样写可以……但是需要大量的代码。让我们尝试一个更简单的方法:

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

我们创建了一个永久的数据存储。如果我们不好好清洁，会发生什么?
我们可能会影响其他的测试。让我们解决这个问题:

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

这里使用的`MemoryStorage` 不持久化数据。又好又简单，没有副作用。

#### 请注意

需要记住的是，依赖项仍然可以是“真实的”对象。不要因为你可以mock所有东西而去mock一切东西。如果下列情况，请考虑使用对象的“真实”版本:

+ 它带来了一个简单、漂亮和容易的测试设置
+ 它不会在测试之间创建共享状态，从而导致意外的副作用
+ 正在测试的代码不会发出AJAX请求、API调用或重新加载浏览器页面
+ 测试的执行速度保持在您确定的范围内

• [Back to ToC](#user-content-table-of-contents) •

### 为每个缺陷创建新的测试

当发现bug时，在**接触任何代码之前**创建一个复制问题的测试。然后，您可以像往常一样应用TDD来修复它。

• [Back to ToC](#user-content-table-of-contents) •

### 不要为复杂的用户交互编写单元测试

复杂用户交互的例子:

+ 填写表单，拖放一些项目，然后提交表单

+ 点击一个选项卡，点击一个图像缩略图，然后从在数据库预加载的图像库中导航

+ (...)

这些交互可能涉及许多工作单元，应该通过**功能测试**在更高的级别上进行处理。他们需要更多的时间来执行。它们可能是不可靠的(假阴性)，并且在报告失败时需要进行调试。

对于功能测试，可以考虑使用测试自动化框架([Selenium](http://docs.seleniumhq.org/)或QA手动测试。

• [Back to ToC](#user-content-table-of-contents) •

### 测试简单的用户操作

简单用户操作示例:

+ 点击切换DOM元素可见性的链接

+ 提交触发表单验证的表单

+ (...)

这些动作可以通过模拟DOM事件**轻松测试**，例如:

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

注意测试是多么简单，因为UI (DOM)层没有与业务逻辑层混合:

+ 发生“单击”事件
+ 调用一个公共方法

下一步可能是测试在“showPreview()”或“hidePreview()”中实现的业务逻辑。

• [Back to ToC](#user-content-table-of-contents) •

### 首先检查测试代码

当评审代码时，总是从阅读测试代码开始。测试是可以深入研究的代码的迷你用例。

它将帮助您非常快速地理解开发人员的意图(可能只是通过查看测试的名称)。

• [Back to ToC](#user-content-table-of-contents) •


### 练习代码，学习结对编程

因为经验是唯一的老师。最终，伟大来自实践;一遍又一遍地应用这个理论，利用反馈每次都变得更好。

• [Back to ToC](#user-content-table-of-contents) •

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

• [Back to ToC](#user-content-table-of-contents) •
