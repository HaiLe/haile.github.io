---
layout: post
title:  Kotlin Questions
date:   2022-05-19 10:21:22 -0700
categories: android, kotlin
---

# Why should we use Kotlin for Android Development?

Google announced at Google I/O 2019 that Android development will be Kotlin first. 

Why? 

Kotlin is an expressive and concise programming language that reduces common code errors

* Expressive and concise: Kotlin increased developers productivity, reduces boiler plate code. 
* Safer code: Have language features that help you avoid common programming mistakes such as Null Pointer exceptions.  Android apps that contain Kotlin code are 20% less likely to crash
* Interoperable: Kotlin is 100% interoperable with the Java programming language. 
* Structured Concurrency: Kotlin coroutines make async call easy.  Coroutines dramatically simplify background task management for everything from network calls to accessing local data. 

# Kotlin operators - apply, run, let, ?

Kotlin has made our life easy by providing features like extension functions, nullability check and much more. One really helpful feature is Scope functions. 

Scope functions are functions which define to the scope of the calling object. We can apply operations on that object within that scope and return the scope itself from the scope function. 

### `let` 

`let` scope function is used to apply operations on an object and finally return the lambda expression from that scope function. Return type can also be `void`

```kt


/**
 * Calls the specified function [block] 
 * with `this` value as its argument 
 * and returns its result.
 *
 */
@kotlin.internal.InlineOnly
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}



```

### `run` 

How Kotlin is null-safe?

What is the difference between var and val?

How const is different from val?

What is the difference between ?. and !!

How to define static functions in Kotlin?

What are Data Classes in Kotlin?

What do you mean by Sealed classes in Kotlin?

Collections in Kotlin.

Difference between lateinit and lazy.

What is Kotlin synthetic binding?

Difference between Kotlin Synthetics, View binding and Butterknife?

What are extension functions in Kotlin?

What are lambda expressions?

What are Higher-Order functions in Kotlin?

Difference between let, run, with, also, apply in Kotlin.

What is the best way for performing tasks on a background thread in kotlin?

What are Coroutines in Kotlin?

What are the different Coroutines Scope?

Why use suspend function in Kotlin Coroutines?

Explain Coroutine LifecycleScope.

What is Kotlin Flow?

Kotlin Flow vs RxJava.

Room vs Realm? Why not Realm?