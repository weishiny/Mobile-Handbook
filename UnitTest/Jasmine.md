## Jasmine Syntax

**Basic**

```javascript
describe("A suite", function() {
  it("contains spec with an expectation", function() {
    expect(true).toBe(true);
  });
});
```

**Suites:** *describe* **Your Tests**

A test suite begins with a call to the global Jasmine function `describe` with two parameters: a string and a function. The string is a name or title for a spec suite - usually what is being tested. The function is a block of code that implements the suite.

**Specs**

Specs are defined by calling the global Jasmine function `it`, which, like `describe` takes a string and a function. The string is the title of the spec and the function is the spec, or test. A spec contains one or more expectations that test the state of the code. An expectation in Jasmine is an assertion that is either true or false. A spec with all true expectations is a passing spec. A spec with one or more false expectations is a failing spec.

**It's Just Functions**

Since `describe` and `it` blocks are functions, they can contain any executable code necessary to implement the test. JavaScript scoping rules apply, so variables `declared` in a describe are available to any `it` block inside the suite.

```javascript
describe("A suite is just a function", function() {
  var a;

  it("and so is a spec", function() {
    a = true;

    expect(a).toBe(true);
  });
});
```

**Expectations**

Expectations are built with the function `expect` which takes a value, called the actual. It is chained with a Matcher function, which takes the expected value.

**Matchers**

Each matcher implements a boolean comparison between the actual value and the expected value. It is responsible for reporting to Jasmine if the expectation is true or false. Jasmine will then pass or fail the spec.

```javascript
it("and has a positive case", function() {
    expect(true).toBe(true);
});
```

Any matcher can evaluate to a negative assertion by chaining the call to `expect` with a `not` before calling the matcher.

```javascript
it("and can have a negative case", function() {
    expect(false).not.toBe(true);
});
```

**Setup and Teardown**

To help a test suite DRY up any duplicated setup and teardown code, Jasmine provides the global `beforeEach`, `afterEach`, `beforeAll`, and `afterAll` functions.

As the name implies, the `beforeEach` function is called once before each spec in the `describe` in which it is called, and the `afterEach` function is called once after each spec.

Here is the same set of specs written a little differently. The variable under test is defined at the top-level scope -- the describe block -- and initialization code is moved into a `beforeEach` function. The `afterEach` function resets the variable before continuing.

```javascript
describe("A spec using beforeEach and afterEach", function() {
  var foo = 0;

  beforeEach(function() {
    foo += 1;
  });

  afterEach(function() {
    foo = 0;
  });

  it("is just a function, so it can contain any code", function() {
    expect(foo).toEqual(1);
  });

  it("can have more than one expectation", function() {
    expect(foo).toEqual(1);
    expect(true).toEqual(true);
  });
});
```

**The** *this* **keyword**

Another way to share variables between a `beforeEach`, it, and `afterEach` is through the `this` keyword. Each spec's `beforeEach`/`it`/`afterEach` has the this as the same empty object that is set back to empty for the next spec's `beforeEach`/`it`/`afterEach`.

```javascript
describe("A spec", function() {
  beforeEach(function() {
    this.foo = 0;
  });

  it("can use the `this` to share state", function() {
    expect(this.foo).toEqual(0);
    this.bar = "test pollution?";
  });

  it("prevents test pollution by having an empty `this` created for the next spec", function() {
    expect(this.foo).toEqual(0);
    expect(this.bar).toBe(undefined);
  });
});
```

**Disabling Suites**

Suites can be disabled with the `xdescribe` function. These suites and any specs inside them are skipped when run and thus their results will show as pending.

```javascript
xdescribe("A spec", function() {
  var foo;

  beforeEach(function() {
    foo = 0;
    foo += 1;
  });

  it("is just a function, so it can contain any code", function() {
    expect(foo).toEqual(1);
  });
});
```

**Pending Specs**

Pending specs do not run, but their names will show up in the results as `pending`.

Any spec declared with `xit` is marked as pending.

Any spec declared without a function body will also be marked pending in results.

And if you call the function `pending` anywhere in the spec body, no matter the expectations, the spec will be marked `pending`. A string passed to `pending` will be treated as a reason and displayed when the suite finishes.

```javascript
describe("Pending specs", function() {

    xit("can be declared 'xit'", function() {
        expect(true).toBe(false);
    });

    it("can be declared with 'it' but without a function");

    it("can be declared by calling 'pending' in the spec body", function() {
        expect(true).toBe(false);
        pending('this is why it is pending');
    });

});
```
- Jasmine Testing Result (No Error)
![2017-05-12-001](https://cloud.githubusercontent.com/assets/14101724/25978811/3d648d5a-36f7-11e7-95f6-8ec7f08203e4.png)

- Jasmine Testing Result (Error Exist)
![2017-05-12-002](https://cloud.githubusercontent.com/assets/14101724/25978835/61cf8cc6-36f7-11e7-9235-a464ac5a55c5.png)

- Jasmine Testing Result (Examine Detail Error Message)
![2017-05-12-003](https://cloud.githubusercontent.com/assets/14101724/25978842/6ce12818-36f7-11e7-9653-25381c327800.png)