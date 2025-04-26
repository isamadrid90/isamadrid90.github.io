---
title: "Upload a file to S3"
seoTitle: "upload s3 aws kotlin sdk"
publishDate: "29 February 2024"
cuid: clt6x7tj6000b09jw6pgh0mf0
slug: aws-sdk-kotlin-uploading-to-s3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1709192597524/aba41a4c-5da4-4b48-98bd-f995b993fdb2.png
tags: ["aws", "kotlin", "sdk", "s3"]

---

Last November, AWS released the first version of [aws-sdk-kotlin](https://github.com/awslabs/aws-sdk-kotlin), and since then, the team has released several versions. You may be wondering why it is interesting if the Kotlin projects could use the AWS Java SDK. This new SDK uses coroutines, one exciting feature of Kotlin.

But something so new will take time to integrate into our projects fully and require some time to investigate. That was exactly my case. I wanted to play with this new SDK and have the functionality fully covered by tests. You can find my progress in [aws-kotlin-examples](https://github.com/isamadrid90/aws-kotlin-examples).

But I also wanted to explain some things I found during my investigation.

For now, let's start with uploading a file to S3.

## First steps

When I start playing around with something new, I always go for the documentation first, so let's visit [AWS S3 code examples](https://docs.aws.amazon.com/sdk-for-kotlin/latest/developer-guide/kotlin_s3_code_examples.html), here we can find some snippets of code. Still, not within the context of a complete program, it points towards a GitHub repository [aws-doc-sdk-examples](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/s3#code-examples) where we could find some examples a bit thorough.

AWS has similar examples for their java SDK at [aws-doc-sdk-examples](https://github.com/awsdocs/aws-doc-sdk-examples)

Let's dive into the code.

```kotlin
suspend fun putS3Object(bucketName: String, objectKey: String, objectPath: String) {
    val metadataVal = mutableMapOf<String, String>()
    metadataVal["myVal"] = "test"

    val request = PutObjectRequest {
        bucket = bucketName
        key = objectKey
        metadata = metadataVal
        body = File(objectPath).asByteStream()
    }

    S3Client { region = "us-east-1" }.use { s3 ->
        val response = s3.putObject(request)
        println("Tag information is ${response.eTag}")
    }
}
```

The first thing that caught my eye is the suspend keyword, which indicates that putS3Object needs to exist inside a coroutine; we have several options to do this:

* Call it from another suspended function or inside coroutineScope block

* Call it inside a runBlocking block, which will block the current thread until all the functions inside the block are complete.


Also, we see that the function receives three parameters: the bucket name, the name of the file in S3, and the file's path to upload; everything is clear until we get here.

Once inside the function, we find a mutable map to define some metadata, which we could use to determine some custom values(this field is optional; I don't particularly like to use mutable maps, so I try with an immutable map, which works fine).

Then, we arrive at the SDK part. To upload a file, we need to create a PutObjectRequest. The example uses the builder to create the instance; bucket, key, and body are required parameters.

After that, we need an S3Client upon which to execute the putObject. The example uses the builder again to create the client; we can define a region for the client. It's possible to set more parameters like credentials and URL (which is very useful to test everything); we'll see it later.

Last, we use the S3Client to execute putObject, which returns a PutObjectResponse, but it can also throw exceptions, something I find interesting to deal with.

# Let's do some code

Now that we have read the docs and know where to start let's do it.

## Create the S3 Client

We start with the S3Client; maybe you think that we could go right away and create the client BUT, as we want to be able to test everything, we need this client to be configurable, so our first class will be a data class that could hold the S3 configuration and which can change depending on the environment.

```kotlin
data class S3ClientConfig(
    val bucketName: String,
    val region: String,
    val url: URL,
    val credentials: CredentialsProvider,
)
```

Here, we could store the bucket's name to which we'll upload the file. In this region, we created the bucket and the URL(from java.net.URL) and credentials(from aws.smithy.kotlin.runtime.auth.awscredentials.CredentialsProvider) to connect.

With that data, we could create the S3Client as follows.

```kotlin
S3Client {
   region = s3ClientConfig.region
   endpointUrl =
   Url {
       scheme = Scheme.parse(s3ClientConfig.url.protocol)
       host = Host.parse(s3ClientConfig.url.host)
       port = s3ClientConfig.url.port
    }
    credentialsProvider = s3ClientConfig.credentials
}
```

## The interface

Before diving into how we can upload our S3 file, I want to mention a decision to make the code easier to test. Thinking about that, I created an interface, but why did this decision make it easier to test our code? We'll see it in the next blog post dedicated to the testing part.

```kotlin
interface FileUploader {
    suspend operator fun invoke(file: File): Result<Unit>
}
```

Here, we have a couple of exciting keywords: `suspend` and `operator`.

The keyword `suspend` indicates that this is a suspending function that needs to be inside a coroutine and could pause its execution and resume it later.

We also find the keyword `operator`, which we use when we want to overload the behavior of an existing operator, in this case, invoke. It'll allow us to use something like `ImplementationFileUploader(file)`.

## Using the new SDK

Keeping the interface in mind, let's see how we can implement it using our shine and new AWS Kotlin SDK.

```kotlin
override suspend fun invoke(file: File): Result<Unit> {
    return runCatching {
        client.use {
            it.putObject(
                PutObjectRequest {
                    bucket = s3ClientConfig.bucketName
                    key = file.name
                    metadata = mapOf()
                    body = file.asByteStream()
                },
            )
        }
    }
}
```

The solution is similar to the example provided by the documentation.

Still, in this case, I prefer to return a `Result` instead of allowing the SDK's exceptions to go uncaught. To do that, I used `runCatching`. This function wraps the result of the code executed inside it into a `Resul` object.

If everything goes fine and the code doesn't throw any exception, the function will return a `Resul.success(Unit)`, but if something fails and the code throws an exception, the function will return `Result.failure(exception)`.

Similarly, we could return the Either type from [Arrow](https://arrow-kt.io/)

## How to use all of this

Now that we know how to upload the file let's see how to use this class.

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

In this code, we find the `suspend` and `operator` keywords; as we discussed them before, we will focus on some other parts of the solution. First of all, we find a `takeIf`, which is a Kotlin scope function (more info [here](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/take-if.html)) that will continue to pass the object to continue the execution if it meets the conditions, if not it'll pass a null value.

Following that, we can see a `?.run` the code inside this `run` function (which is also a Kotlin scope function, more info [here](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/run.html)) will execute, only if the calling object is not null, in this case we'll invoke the FileUploader.

To finish, we find `?:` or the Elvis operator, which executes the code at its right if the left object is null.

Everything seems like any other piece of code, right? How is that possible when the operator `invoke` from FileUploader is a suspending function? Well, because inside a suspending function, we can execute another suspending function, `suspend operator fun invoke(file: File).`

I think this is enough for the series' first post about AWS SDK Kotlin. We'll continue with the testing part in the following post.

You can find the complete example on [GitHub](https://github.com/isamadrid90/aws-kotlin-examples/tree/main/upload-s3-file)

I hope you find it useful. My name is Isabel Garrido, and I'm a Senior Kotlin server-side developer. You can follow me on [Twitter](https://twitter.com/isabeliita90), [Linkedin](https://www.linkedin.com/in/isabel-garrido-cardenas/?locale=en_US) and [GitHub](https://github.com/isamadrid90)
