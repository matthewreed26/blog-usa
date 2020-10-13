---
authors:
- Matthew Reed
tags:
- API
- TDD
- Testing
- Postman

date: 2020-10-06T00:00:00.000Z
title: "Forget Cucumber, Start Automating Integration Tests with Postman"
image: 
---

# What is wrong with Cucumber?

In short, nothing is wrong with Cucumber. For a long time now many of us have been productively using Cucumber or one of its variations for Acceptance Test Driven Development (ATDD). The English-written tests are the gates to our applications which keep breaking changes out of the codebase. This approach of bridging the business and development sides is going to continue even after this blog. Yet, hardly every Product Owner is actively writing and maintaining these tests on their projects. Only the high-level test results come up anyway when the CI/CD pipeline runs, so developers are often needed to investigate no matter what.

Why does there need to be such a syntactically complex system in place for a group who looks at the feature file only every few months?

Well, good news everyone! There actually is another way to write integration tests already built into the common development testing tool, [Postman](https://www.guru99.com/postman-tutorial.html). It is simple to get started and, as will be shown, they can be easily integrated with [Newman](https://learning.postman.com/docs/running-collections/using-newman-cli/command-line-integration-with-newman/) to run automatically within a CI/CD pipeline. Soon you will wonder if Cucumber is necessary for your project, and may even choose to forget it altogether.

# The Postman Alternative

The point of this blog is not to compare apples to oranges, or Cucumber to Postman tests, but instead will offer insights to utilizing the Postman alternative within an already existing API. For a great overview of writing test basics, the ["Getting started with tests"](https://learning.postman.com/docs/writing-scripts/script-references/test-examples/#getting-started-with-tests) from Postman is an essential resource. Basically, JavaScript-based functions placed under the "Tests" tab within a request can be executed in succession. The built-in `pm` library is not always intuitive to use but there is ample documentation and code examples. It uses [the BDD Chai testing framework](https://www.chaijs.com/) to make assertions via chains of `to.have` like so:

```javascript
pm.test("Status code is 200", function () {
  pm.response.to.have.status(200);
});
```

(For more on writing tests, check out another [similar blog](https://dev.to/scampiuk/using-postman-s-cli-tool-for-api-testing-newman-5fn1) however note it does not go all the way into CI/CD pipelines with Newman like will be covered later here.)

For demonstration purposes, integration tests were added to a series of pre-composed [Postman requests](https://t.ly/docs/collection.json) provided by the [T.LY URL Shortening](https://t.ly/docs/) API. The [finished Postman test suite](https://github.com/matthewreed26/postman-tests-tly) covers a few happy path GET and POST calls as well as their error scenarios. There are examples including ensuring correct response statuses, folder-level configuration, equating request and response data, setting variables from one and using them in subsequent tests, verifying time stamps, setting Pre-request Scripts, and more.

### T.LY API Postman Collection
![T.LY API Postman Collection]()

The finished suite contains only one Postman environment but others would be critical for testing the application if it got deployed to different development environments. As can be seen, `host` as a variable in the Postman calls presents an option for routing test traffic however needed, even to a port on `localhost` for an application running locally.

### T.LY Postman Environment
![T.LY Postman Environment]()

### T.LY Postman Shorten GET Call
![T.LY Postman Shorten GET Call]()

# Running within Postman

To ensure all the tests are passing in the Postman app before we move any further, under the T.LY API right-pointing arrow that opens the expanded menu hit the blue "Run" button as is shown below.

### How to Run Written Tests
![How to Run Written Tests]()

Select any of the available options located in the next screen including desired tests for the run order.

### Tests Options Screen
![Tests Options Screen]()

Hit the blue "Run T.LY API" button to start running the tests. The outcome should be all green/passed. It is important to note how each of the individual outcomes have a high-level description. These are the same outputs that will be displayed in the pipeline's console later.

### Test Suite Passing
![Test Suite Passing]()

If the tests fail here, there is a good likelihood that they will not pass in the pipeline either. This is the place to debug/modify any failing tests and can be returned to later on.

# Running using Newman with CI/CD



<u>Sources</u>

* [T.LY URL Shortening Docs](https://t.ly/docs/)
