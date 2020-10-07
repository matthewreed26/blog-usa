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

In short, nothing is wrong with Cucumber. For a long time now many of us have been productively using Cucumber or one of its variations for Acceptance Test Driven Development (ATDD). The English-written tests keep our applications from deploying breaking changes into Production. This approach of bridging the business and development sides is going to continue even after this blog. Yet, hardly every Product Owner is actively writing and maintaining these tests on their projects. Only the high-level test results come up anyway when the CI/CD pipeline runs, so developers are often needed to investigate no matter what.

Why does there need to be such a syntactically complex system in place for a group who looks at the feature file only every few months?

Well, good news everyone! There actually is another way to write integration tests already built into the common development testing tool, Postman. It is simple to get started and, as will be shown, they can be easily integrated with [Newman](https://learning.postman.com/docs/running-collections/using-newman-cli/command-line-integration-with-newman/) to run automatically within a CI/CD pipeline. Soon you will wonder if Cucumber is necessary for your project, and may even choose to forget it altogether.

# The Postman Alternative

The point of this blog is not to compare apples to oranges, or Cucumber to Postman tests, but instead will offer insights to utilizing the Postman alternative within an already existing API. The [finished Postman test suite](https://github.com/matthewreed26/postman-tests-tly) created for demonstration purposes adds integration tests to a pre-composed [Postman test suite](https://t.ly/docs/collection.json) provided by the [T.LY URL Shortening](https://t.ly/docs/) API. These integration tests cover a few happy path GET and POST calls as well as their error scenarios. <i>(Future post is coming more on the in's-and-out's of these tests using ECMAScript including ensuring response statuses, folder-level configuration, equating request and response data, setting variables from one and using them in subsequent tests, verifying time stamps, and more.)</i>

### T.LY API Postman Collection
![T.LY API Postman Collection]()

The finished suite contains only one Postman environment but others would be critical for testing the application if it got deployed to different development environments. As can be seen, `host` as a variable in the Postman calls presents an option for routing test traffic however needed, even to a port on `localhost` for an application running locally.

### T.LY Postman Environment
![T.LY Postman Environment]()

### T.LY Postman Shorten GET Call
![T.LY Postman Shorten GET Call]()

# Running using Newman with CI/CD



<u>Sources</u>

* [T.LY URL Shortening Docs](https://t.ly/docs/)
