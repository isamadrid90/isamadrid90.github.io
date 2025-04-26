---
title: "Uploading a file to S3 - Integration test"
description: "Uploading a file to S3 - Integration test"
seoTitle: "S3 File Upload Integration Test"
seoDescription: "Learn how to perform integration tests for uploading files to AWS S3 using Test Containers and Localstack"
publishDate: "6 June 2024"
cuid: clx2v70e000000amf8qgw8bk4
slug: uploading-a-file-to-s3-integration-test
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1720019433612/7d300404-e931-45af-9209-5dbfc50f9a78.png
tags: ["aws", "testing", "kotlin", "localstack", "testcontainers"]

---

In the previous article of the series, I covered the unit tests for this functionality. However, that isn't enough to ensure our feature works on this occasion because we are integrating it with another service.

To create our controlled version of AWS S3, we will use [Test Containers](https://testcontainers.com/) and [Localstack](https://www.localstack.cloud/).

Test Containers will help us to create docker containers by code, it's very convenient and easy to use to have everything defined in our tests. Localstack is a curated mock server of AWS, it's very complete and easy to use.

The following code is how we run Localstack in our test and how we set it up to be able to use S3. We also save the local endpoint to access S3 because we have to use it in several places during the test and this way makes the code cleaner.

```kotlin
companion object {
    private lateinit var s3Endpoint: URI
    @Container
    val localstack: LocalStackContainer =
        LocalStackContainer(DockerImageName.parse("localstack/localstack:3.0"))
            .withServices(LocalStackContainer.Service.S3)

    @JvmStatic
    @BeforeAll
    fun beforeAll() {
        localstack.execInContainer("awslocal", "s3", "mb", "s3://my-bucket")
        s3Endpoint = localstack.getEndpointOverride(LocalStackContainer.Service.S3)
    }
}
```

First, we're going to briefly review the code we're going to test

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

We need a file that exists, once that is checked, we upload it to S3 and ensure the result is a success

The first version of a successful integration test will be the one you can find below

```kotlin
@Test
fun `should upload file successfully`() {
    val file = `given an existing file`()
    runTest {
        val result = `when the file is uploaded to S3`(file)
        `then the upload should be successful`(result)
    }
}
```

We can find again the `runTest` keyword which we talked about in [the previous article](https://hashnode.com/post/cltpzd5dr000008jqd8803bdb). We need it because the code inside includes a suspended function.

```kotlin
private suspend fun `when the file is uploaded to S3`(file: File) =
    UploadFile(
        S3FileUploader(
            S3ClientConfig(
                bucketName = "my-bucket",
                region = localstack.region,
                url = s3Endpoint.toURL(),
                credentials =
                StaticCredentialsProvider {
                    accessKeyId = localstack.accessKey
                    secretAccessKey = localstack.secretKey
                },
            ),
        ),
    ).run {
        this(file = file)
    }
```

To ensure the result is correct we're using a function similar to the one for the unit test.

```kotlin
private fun `then the upload should be successful`(result: Result<Unit>) {
    assertEquals(Result.success(Unit), result)
}
```

With this test, we could say that there was no exception during the execution of the happy path, but are we sure the file is correctly uploaded? I'll add another check to be sure.

```kotlin
@Test
fun `should upload file successfully`() {
    val file = `given an existing file`()
    runTest {
        val result = `when the file is uploaded to S3`(file)
        `then the upload should be successful`(result)
    }
    `then the content on S3 should be the same and the content uploaded`(file)
}
```

This new step forces us to create a new instance of an S3Client directly on the test, why? because we now don't have another way to access the files uploaded to our local version of S3 (localstack).

```kotlin
private fun `then the content on S3 should be the same and the content uploaded`(file: File) {
    val s3Client =
        S3Client {
            region = localstack.region
            endpointUrl =
                Url {
                    scheme = Scheme.parse(s3Endpoint.toURL().protocol)
                    host = Host.parse(s3Endpoint.toURL().host)
                    port = s3Endpoint.toURL().port
                }
            credentialsProvider =
                StaticCredentialsProvider {
                    accessKeyId = localstack.accessKey
                    secretAccessKey = localstack.secretKey
                }
        }

    runBlocking {
        s3Client.use {
            it.getObject(
                GetObjectRequest {
                    bucket = "my-bucket"
                    key = "file.txt"
                },
            ) { response ->
                assertNotNull(response.body)
                response.body?.let { body ->
                    assertEquals(
                        InputStreamReader(file.inputStream(), StandardCharsets.UTF_8).readText(),
                        InputStreamReader(body.toInputStream(), StandardCharsets.UTF_8).readText(),
                    )
                }
            }
        }
    }
}
```

This code is very similar to the one we use in production to upload an S3 file, with. the difference is that here we create a `GetObjectRequest` with the name of the bucket and the file that we just uploaded.

Finally, to ensure the file in S3 is the same one that we uploaded we compare their content.

You can find the complete example on [GitHub](https://github.com/isamadrid90/aws-kotlin-examples/tree/main/upload-s3-file/src/test/kotlin/org/isamadrid90/aws/demo)
