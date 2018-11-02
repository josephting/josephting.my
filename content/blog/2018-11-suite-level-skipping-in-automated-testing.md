+++
date = "2018-11-02"
title = "Suite Level Skipping in Automated Testing"
categories = ["Programming"]
tags = ["javascript", "testing", "mocha"]

+++

With all the test frameworks like [Jasmine](https://jasmine.github.io/), [Mocha](https://mochajs.org/) and [Jest](https://jestjs.io/), it's now significantly easier to write tests to be run automatically for continuous integration of your software.

However, there may be times that the framework you have chosen does not work the way you want it to be intentionally due to best practices but figured that it's OK to bend some rules to best suit your case.

In my case, I wanted to have a suite of tests that sort of acts like a specific use case where it will serve no purpose to continue testing should any of the test in a specific suite fails but to skip to the next suite of tests.

Fortunately, Mocha, the framework I'm using offers the ability to specify hooks of which I can utilize to do what I want as stated above.

```javascript
let caseInSuiteFailed = false
let previousSuite = null

beforeEach(function(done) {
  if (previousSuite === null || (
    this.currentTest.parent.title === previousSuite.title &&
    this.currentTest.file === previousSuite.file)) {
    if (caseInSuiteFailed) {
      this.skip()
    }
  } else if (this.currentTest.parent.title !== previousSuite.title ||
    this.currentTest.file !== previousSuite.file) {
    caseInSuiteFailed = false
  }
  done()
})

afterEach(function(done) {
  if (this.currentTest.state !== 'passed') {
    caseInSuiteFailed = true
    previousSuite = {
      title: this.currentTest.parent.title,
      file: this.currentTest.file
    }
  }
  done()
})
```

Basically, I can store the suite's metadata onto a variable and compare against it when encountering a failed test. Then, I can decide whether or not to skip current test according to the specified condition.

The sample code for [Suite Level Skipping on Mocha](https://github.com/josephting/mocha-suite-level-skipping) is available on GitHub if there is anyone who encountered similar problem as I did.
