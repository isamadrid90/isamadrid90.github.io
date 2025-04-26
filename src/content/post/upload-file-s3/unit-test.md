---
title: "Upload a file to S3 - Unit test"
description: "Upload a file to S3 - Unit test"
publishDate: "13 March 2024"
cuid: cltpzd5dr000008jqd8803bdb
slug: upload-a-file-to-s3-unit-test
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1710345167080/1fe89195-303e-4224-8d0e-313113b0addc.png
tags: ["unit-testing", "aws", "testing", "kotlin", "kotlin-coroutines"]
seriesId: upload-file-s3
orderInSeries: 2
---

Testing is one of the things I've dedicated more time to in my professional life. Even though it's an essential part of our daily job, I still find test suites that do not ensure the correct behavior of a feature. That is why I wanted a separate post to discuss how to test this example correctly.

In my experience, we will need unit and integration tests to test this kind of feature properly. As it's functionality without an entry point, endpoint, subscriber, cron, or console command, we do not have an acceptance test, but we'll need them if it is another situation.

Let's start with Unit Tests.

## Unit test

Against what most people believe, there is no single way to do unit tests because it depends on what we consider the unit. In my case, I consider the unit one behavior. This means I used test doubles just for the parts of the code that communicate with the outside, like connecting to an API, publishing an event, and connecting to a database. This approach allows me to create reliable test suites that shouldn't change if I refactor the code without changing the behavior.

With that in mind, let's see the code.

We're going to test this code.

```kotlin
class UploadFile(private val uploader: FileUploader) {
    suspend operator fun invoke(file: File): Result<Unit> {
        return file
            .takeIf { it.exists() }
            ?.run { uploader(File(path)) }
            ?: Result.failure(FilePathNotExists(file.path))
    }
}
```

The first thing that I notice is different is the `suspend`. I explained in the previous post that this function needs to be executed inside a coroutine. How can we do that on our test? Is this going to make my test execution slower? If a `delay` is inside one coroutine, will my test wait until it finishes?

Instead of answering those questions directly. I want to explain my learning process on testing coroutines.

I start the tests using `runBlocking` because I need to execute a coroutine, and I also need to wait until the coroutine finishes to ensure the result is correct.

```kotlin
    @Test
    fun `should upload the file successfully`() {
        runBlocking {
            val file = `given an existing file`()
            `given file uploader always is successful`()
            val result = `when an existing file is been uploaded`(file)
            `then the result should be successful`(result)
        }
    }
```

Ok, it works, but I want to be sure this is the best alternative, so I check with JetBrains AI Assistant. There is another option. I can use `runBlockingTest`. To use it, I need to add a new dependency to my `build.gradle.kts`

```plaintext
 testImplementation("org.jetbrains.kotlinx:kotlinx-coroutines-test:1.8.0")
```

And then change the `runBlocking` in my test for `runBlockingTest`

```kotlin
    @Test
    fun `should upload the file successfully`() {
        runBlockingTest {
            val file = `given an existing file`()
            `given file uploader always is successful`()
            val result = `when an existing file is been uploaded`(file)
            `then the result should be successful`(result)
        }
    }
```

But when I did that, the IDE advised me this function was deprecated and that I should use `runTest,` you can check the docs about this function [here](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/run-test.html). Here is the final result

```kotlin
    @Test
    fun `should upload the file successfully`() {
        runTest {
            val file = `given an existing file`()
            `given file uploader always is successful`()
            val result = `when an existing file is been uploaded`(file)
            `then the result should be successful`(result)
        }
    }
```

Once I solved that, we found the next challenge involving File objects. If you have used Mockk in the past, maybe you suffer from this problem, but there is no easy way to create a test double for File with this library, so I decided to create a couple of Stubs manually. One is to simulate an existing file, and another is to simulate a non-existing one.

```kotlin
data class ExistingFile(private val path: String) : File(path) {
    override fun exists() = true
}
```

```kotlin
data class NotExistingFile(private val path: String) : File(path) {
    override fun exists() = false
}
```

Finally, we arrive at the part where we need to create a test double for `FileUploader` because this is the interface we implement to use the AWS Kotlin SDK. For this particular situation, I decided to go with a Dummy, one for a successful execution and the other for a failed execution.

```kotlin
    private fun `given file uploader always is successful`() {
        coEvery { uploader(any()) } returns Result.success(Unit)
    }
```

```kotlin
    private fun `given file uploader always fails`() {
        coEvery { uploader(any()) } returns Result.failure(Throwable())
    }
```

We're missing only a way to ensure the results are correct. In this case, we're returning `Result<Unit>`. The following code shows a way to ensure the outcome is a successful Resul.

```kotlin
   private fun `then the result should be successful`(result: Result<Unit>) {
        assertEquals(Result.success(Unit), result)
    }
```

On the other hand, we can check that a `Result` is failure because of a particular exception with the following code.

```kotlin
private fun `then the result should be failed because file path not exists`(result: Result<Unit>) {
     assertEquals(Result.failure(FilePathNotExists("path/to/file.txt")), result)
}
```

You can find the complete example on [GitHub](https://github.com/isamadrid90/aws-kotlin-examples/tree/main/upload-s3-file/src/test/kotlin/org/isamadrid90/aws/demo)

I hope you find it useful. My name is Isabel Garrido, and I'm a Senior Kotlin server-side developer. You can follow me on [Twitter](https://twitter.com/isabeliita90), [Linkedin](https://www.linkedin.com/in/isabel-garrido-4000164a/) and [GitHub](https://github.com/isamadrid90)
