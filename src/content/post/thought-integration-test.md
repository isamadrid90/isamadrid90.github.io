---
title: "A thought on Integration tests"
publishDate: "19 May 2024"
cuid: clwdplifs000009l1dlwd8t2m
slug: a-thought-on-integration-tests
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1716125588406/f87172de-5d81-416a-9e72-2ba8d42d4886.png
tags: ["technology", "developer", "testing", "code-quality", "api-testing"]

---

The initial idea of this article was to continue my [Upload a file to S3](https://isabeliita90.hashnode.dev/series/aws-sdk-kotlin-examples) series, this time writing about Integration tests. But in the end, it was more about how I understand this type of testing and it makes sense to have it as something completely separated.

![Gif integration test, two windows that cannot open](https://i.gifer.com/sq5.gif align="center")

Let's talk a bit more about Integration Tests

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Disclaimer: What I'm going to explain here is based on my own experience creating APIs that integrate through HTTP APIs with third-party services.</div>
</div>

## Integration Tests

![Testing pyramid from https://www.ministryoftesting.com/articles/the-mobile-test-pyramid. This pyramid has 3 levels: the base is Unit Test, second level is Integration test and third level is E2E Tests. It also includes a mention to Manual Tests at the top but outside the pyramid](https://d2h1nbmw1jjnl.cloudfront.net/ckeditor/pictures/data/000/000/158/content/typical_pyramid-1024x938.jpg align="left")

If we ask different people what is an integration test and how to do it? The answer could be completely different depending on each person.

If we understand a unit as one class in unit testing, we could say that an integration test ensures the proper behavior between two classes working together. Personally, this is not my view because I see a unit as a single behavior, as I explained in [my previous article](https://hashnode.com/post/cltpzd5dr000008jqd8803bdb).

Apart from that, we could say that an integration test is a way to ensure that our code works against another service, in this case, AWS. Even with this definition, we have two different approaches. One that executes the integration tests against a functional environment provided for the third party and another approach that uses a controlled version of the third party towards integrating. Both methods have their advantages and disadvantages. Let's review them before I share which one I prefer.

## Integration tests with real environment

A straightforward approach for integration tests will be to execute our tests against a real environment provided by the third party. I strongly recommend choosing a sandbox or test environment for this task. These environments are specifically designed for such purposes. Also calls to the production environment could result in cost increases.

One of the advantages is that we'll realize something has changed in the third-party contract the moment we execute those tests. While this immediate feedback might appear beneficial, if our test suit is required to pass to deploy our code to production, anything that could break it is a potential blocker of the deployment pipeline.

Moreover, it's essential to recognize that the outcome of our tests depends on changes completely out of our control, done by the third party to their environment.

## Integration test with a controlled environment

I didn't know how to title this section. Here I'll explain some advantages and disadvantages that I find of doing integration tests against a version of a third party controlled by us, it could be a mock server or a test double of the library that we use to communicate with the third-party. Both options have in common that the definition of the available endpoints and their responses depend solely on the developer who creates the test. Another option could be to have a mock server for the third-party API shared by a team, this isn't the focus of this article but if it's interesting we could talk more about it in the following articles.

The primary benefit is that the results of our tests are solely influenced by the mock we create. There won't be any unexpected requests or responses beyond what we've defined. While this provides a controlled testing environment, it also means that developers integrating with the new service will need to invest more effort. If there are changes in the third-party contract, these modifications must be reflected in the mock. Additionally, the mock may not include all potential responses, typically only the ones we thought about.

## Do you know FIRST principles?

At this point you probably are thinking but which approach will you prefer? (*mójate*, as we'll say in Spanish)

The FIRST principles mainly inspire my preference. FIRST is the acronym for Fast, Independent, Repeatable, Self-validating, and Thorough. I'm not going to talk about them because thousands of resources explain them in depth (ex: [FIRST principles of testing](https://medium.com/@tasdikrahman/f-i-r-s-t-principles-of-testing-1a497acda8d6)). Those principles were written focused on unit tests but I find some of them extensible to other types of testing.

One of these principles that I don't see applies to Integration tests is Through. Integration tests have a lot of advantages but one disadvantage is the execution time. This type of test is slower than unit tests, that's the reason we rank it higher in the Testing Pyramid because we should have fewer Integration tests than Unit tests. Having that in mind, we cannot say these tests should be through because we need to create only the ones that we cannot validate using a faster method, like Unit Test.

Having that in mind, I prefer the second approach where we do our integration tests against a controlled environment that we have defined. Because in that way our tests are independent of any external factor, and they are repeatable, the result of our tests is deterministic, if we execute the same test several times there will be always the same outcome due to the behavior of the third-party service will always be the same that we have defined.

What approach do you prefer? I'm very interested in listening to different opinions on the topic!

I hope you find it useful. My name is Isabel Garrido, and I'm a Senior Kotlin server-side developer. You can follow me on [Twitter](https://twitter.com/isabeliita90), [Linkedin](https://www.linkedin.com/in/isabel-garrido-4000164a/) and [GitHub](https://github.com/isamadrid90)
