---
title: "Refactor Tips: Kotlin Scope Functions"
description: "Refactor Tips: Kotlin Scope Functions"
seoTitle: "kotlin scope functions"
publishDate: "28 Mar 2025"
tags: ["kotlin"]

---

Kotlin provides many valuable functionalities, and one of the most powerful is scope functions.

Scope functions—`let`, `apply`, `also`, `run`, and `with`—allow us to execute blocks of code within the context of an object. They are handy, but it can be difficult to know when to use each one. You can find more details in the official documentation: [Scope Functions](https://kotlinlang.org/docs/scope-functions.html).

Although plenty of documentation is available, many Kotlin developers come from a Java background. Since I followed the same path, I’d like to share my tips and tricks for using them effectively.

---

## Common Categorization of Scope Functions

The most common way to divide scope functions is by:

### 1\. Receiver vs. Parameter (`this` vs. `it`)

* `run`, `apply` and `with` provide access to the context object as `this`.

* `let` and `also`provide it as `it` (an implicit lambda parameter).


| Function | Receiver (`this`) | Parameter (`it`) |
| --- | --- | --- |
| `let` | ❌ | ✅ |
| `run` | ✅ | ❌ |
| `apply` | ✅ | ❌ |
| `also` | ❌ | ✅ |
| `with` | ✅ | ❌ |

### 2\. Return Type

* `let`, `run` and `with` return the result of the block.

* `apply`and `also` return the object itself.


| Function | Returns |
| --- | --- |
| `let` | Lambda result |
| `run` | Lambda result |
| `apply` | Original object |
| `also` | Original object |
| `with` | Lambda result |

---

## Deep Dive

To begin with, let's imagine we have this code

```kotlin
data class MyFile(
    val name: String,
    val createdAt: Instant,
    val lastAccessedAt: Instant,
    val lastModifiedAt: Instant,
    val actionsOver: List<String> = emptyList()
){

    fun rename(newName: String): MyFile {
        val currentTime: Instant = Instant.now()
        return this.copy(
            name = newName,
            lastAccessedAt = currentTime,
            lastModifiedAt = currentTime,
            actionsOver = actionsOver + "RENAMED TO $newName at $currentTime"
        )
    }
}
```

But other differences are not so highlighted and, for me are key to choosing between them.

### `with`: A Non-Extension Function

`with` is unique because it is not an extension function. Instead, it takes the context object as an argument and makes it available as `this` inside the block. It is useful when you need to access the same object multiple times within a code block.

We can perform a rename over MyFile using the following code

```kotlin
fun execute(newName: String) {
    file.rename(newName)
    println("Rename completed successfully to ${file.name}")
    println("Previous actions")
    file.actionsOver.forEach { println(it) }
}
```

Let's see how we can refactor it by using `with`

### Example:

```kotlin
fun execute(newName: String) = with(file) {
    rename(newName)
    println("Rename completed successfully to $name")
    println("Previous actions:")
    actionsOver.forEach(::println)
}
```

---

### `let` and `run`: Returning a Computed Result

Both `let` and `run` return the result of the lambda.

Now, suppose we have this code:

```kotlin
fun execute(newName: String) {
    val renamedFile = file.rename(newName)
    println("Rename completed successfully to ${renamedFile.name}")
    println("Actions over file")
    renamedFile.actionsOver.forEach { println(it) }
}
```

How would it end up:

### Using `let`:

```kotlin
fun execute(newName: String) = file.rename(newName).let { renamedFile ->
    println("Rename completed successfully to ${renamedFile.name}")
    println("Actions over file:")
    renamedFile.actionsOver.forEach(::println)
}
```

### Using run:

```kotlin
fun execute(newName: String) = file.rename(newName).run {
    println("Rename completed successfully to $name")
    println("Actions over file:")
    actionsOver.forEach(::println)
}
```

---

### `apply` and `also`: Returning the Object Itself

Both `apply` and `also` return the original object, which is useful for modifying objects fluently.

### Example:

```kotlin
fun execute(newName: String) = file.apply {
    rename(newName)
    println("Rename completed successfully to $newName")
}.also {
    println("Actions over file:")
    it.actionsOver.forEach(::println)
}
```

---

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text"><code>also</code> works great for logging when there is no need to change the original object</div>
</div>

## Final Advice

### Avoid Nesting Scope Functions

When we learn something new and we're very excited to use it it's very easy to overuse it, this could lead to code hard to read and understand. With scope functions I always try to keep them flat and short.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742325263299/26f347f4-4780-48f5-a920-cd89b28d3dcd.gif align="center")

---

## Summary

This is the combination of all the categorization explained at the beginning:

| Function | `this` vs `it` | Extension function | Returns |
| --- | --- | --- | --- |
| `let` | `it` | ✅ | Lambda result |
| `run` | `this` | ✅ | Lambda result |
| `apply` | `this` | ✅ | Original object |
| `also` | `it` | ✅ | Original object |
| `with` | `this` | ❌ | Lambda result |

Kotlin’s scope functions are incredibly powerful when used correctly. Understanding their differences helps write more concise, readable code.

Do you use them?

My name is Isabel Garrido, and I'm a Senior Backend Developer. You can follow me on [Bluesky](https://bsky.app/profile/isabeliita90.bsky.social), [LinkedIn](https://www.linkedin.com/in/isabel-garrido-4000164a/), and [GitHub](https://github.com/isamadrid90).