---
title: "Mutation Testing in Kotlin II"
description: "Mutation Testing in Kotlin II"
publishDate: "15 January 2024"
slug: mutation-testing-in-kotlin-2
tags: ["kotlin", "mutation-testing", "pitest", "github-actions"]
---

# Mutation Testing in Kotlin II

Some months ago I wrote about how to use mutation testing in a Kotlin project, you can read it [here](https://medium.com/seat-code/mutation-testing-in-kotlin-a8834771e85e)

After some months of having Mutation Testing in production as part of our CI we have learned a lot, I’m going to summarise the takeaways. First of all, Mutation Testing is a measure and, one of the most interesting things we can obtain from it is the trend, because of that, it’s more useful if we execute it regularly than if we do it from time to time. Having that in mind we decided to include it as part of our GitHub Actions pipeline but we encounter some difficulties:

*   we need something that allows us to check quickly the relevant values, like Mutation Score Indicator


*   we also need to have the complete report available to identify possible improvements

*   we didn’t find any GitHub action that executed Pitest so everything needed to be done manually


## What we are using

I thought that being interested in the topic will make all of these things not a big deal but, if I wanted to spread the use of this technique I needed to make it easier, that’s why I created a GitHub Action that executes Pitest, upload the HTML report as an artifact and leave the summary as a comment in the pull request, all the requirements are in the Readme but please, let me know if you tried it have some doubt

[https://github.com/isamadrid90/gradle-pitest-comment-action](https://github.com/isamadrid90/gradle-pitest-comment-action)

If you use Gradle and Pitest you just need to create a new job like this

```jsx
mutation_test:
    runs-on: ubuntu-latest
    steps:
        - uses: actions/checkout@v2

        - name: Set up JDK 11
          uses: actions/setup-java@v2
          with:
            java-version: '11'
            distribution: 'adopt'

        - name: Execute PITest
          uses: isamadrid90/gradle-pitest-comment-action@v1.0.0-beta
          with:
            repo-token: ${{ secrets.GITHUB_TOKEN }}
```

And that’s all, you will be executing Pitest inside the workflow that contains it

![Malevolent saying easy peasy](https://media.giphy.com/media/zFHrGWOaMrr6OmzbvC/giphy-downsized.gif align="middle")

Malevolent saying easy peasy

if you want to see some examples, I have two small projects using it available

[Review algorithms](https://github.com/isamadrid90/review-algorithms)

[Fizzbuzz kotlin](https://github.com/isamadrid90/fizz-buzz-kotlin)

## What we have learned

Apart from that, we also took a deeper look at the reports and found out that some of the mutants that were not captured didn’t make sense to us because they come from the Java code that is generated when you use Kotlin, but we do not have access to it or make any change, so we investigated a bit 🧐 and decided to add an exclusion to Pitest configuration

```kotlin
pitest {
        ...
        setProperty("avoidCallsTo", listOf("kotlin.jvm.internal"))
    }
```

With just this line we decreased the mutants generated and improved the execution time 🎉

## What we need to improve

But even with those changes, we are far from done.

Thanks to the comments in the pull request we can see that the trend is decreasing but we cannot see it in the same pull request, we need to check the previous pull requests and proactively evaluate how we are doing compared to those numbers, to be honest, we just do it from time to time so we realize about the decreasing when it’s too late to fix it easily.

In that sense, our next steps will include doing something to visually track the mutation testing trend of the project and see how that impacts the metric, I’ll write about it when it’s finished and tested.

I hope you find this interesting and inspiring, please let me know any questions related to it.
