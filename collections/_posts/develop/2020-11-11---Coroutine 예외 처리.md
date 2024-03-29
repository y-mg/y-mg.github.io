---
layout: post
title: "Coroutine 예외 처리"
date: 2020-11-11T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, Coroutine]
description: "Cancellation / Exception / SupervisorJob / supervisorScope / CoroutineExceptionHandler"
thumbnail: "/assets/images/kotlin/banner/coroutine.png"
image: "/assets/images/kotlin/banner/coroutine.png"
comments: false
---

## Cancellation
***
코루틴은 내부적으로 취소 동작을 위해 <code style="color: #eb5657;">CancellationException</code> 을 사용한다.
- 모든 <code style="color: #eb5657;">Handler</code> 가 무시하여 <code style="color: #eb5657;">Handler</code> 등록하여도 동작하지 않는다.
- <code style="color: #eb5657;">try-catch</code> 를 통해 처리할 경우 취소 및 종료시에 자원 반납이 대한 이중 코드가 되기에 <code style="color: #eb5657;">try-finally</code> 에서 한번에 처리해야 한다.
<br/>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val parent = launch {
        val child = launch {
            try {
                delay(Long.MAX_VALUE)
            } catch (e :CancellationException) {
                println("Child Cancelled")
            }
        }

        yield() // Thread 순서 양보
        println("Start Child Cancel")
        
        child.cancelAndJoin()
        yield()

        println("Parent Not Cancelled")
    }
    parent.join()
}


// Start Child Cancel
// Child Canceld
// Parent Not Cancel
```
<br/>
<br/>



## Exception
***
코루틴은 <code style="color: #eb5657;">CancellationException</code> 이외의 예외를 만나면 양방향 처리(부모 → 자식, 자식 → 부모)한다.
- 부모에서 예외 발생 시 모든 자식을 종료시키고 부모 코루틴에 의해 처리된다.
- 자식에서 예외 발생 시 부모 및 모든 자식을 종료시키고 부모 코루틴에 의해 처리된다.
<br/>

```kotlin
import kotlinx.coroutines.*

val handler = CoroutineExceptionHandler { _, exception ->
    println("Caught $exception")
}

fun main() = runBlocking {
    val parent = GlobalScope.launch(handler) {
        launch {
            try {
                delay(Long.MAX_VALUE)
            } finally {
                withContext(NonCancellable) {
                    println("First Child Canceled")
                    delay(2000)
                    println("First Child Finished")
                }
            }
        }
        launch {
            delay(1000)
            println("Second Child Throw Exception")
            throw Exception()
        }
    }
    parent.join()
}


// Second Child Throw Exception
// First Child Canceled
// First Child Finished
// Caught java.lang.Exception
```
<br/>
<br/>



## SupervisorJob
***
예외를 양방향 처리(부모 → 자식, 자식 → 부모)하는 <code style="color: #eb5657;">Job</code> 객체와는 다르게 예외를 단방향 처리(부모 → 자식)하는 <code style="color: #eb5657;">Job</code> 객체이다.
<br/>
<br/>

### 부모 코루틴 예외 발생
부모 코루틴에서 예외 발생 시 모든 자식이 종료된다.
<br/>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    with(CoroutineScope(coroutineContext)) {
        val firstChild = launch(CoroutineExceptionHandler { _, _ ->  }) {
            println("First Child Failed")
            throw Exception("Caught Exception") // 에러로 인해 취소되고 부모 코루틴으로 전파
        }

        val secondChild = launch { // 부모 코루틴이 취소되었기에 자식 코루틴에게도 취소가 전파됨
            delay(1000)
            println("First Child State: ${firstChild.isCancelled}")
            println("Second Child")
        }

        firstChild.join()
        secondChild.join()
    }
}


// First Child Failed
// Exception in thread "main" java.lang.Exception: Caught Exception
```
<br/>

### 자식 코루틴 예외 발생
자식 코루틴에서 예외 발생 시 해당 <code style="color: #eb5657;">Job</code> 만 종료된다.
<br/>

```kotlin
import kotlinx.coroutines.*

val supervisor = SupervisorJob()

fun main() = runBlocking {
    with(CoroutineScope(coroutineContext + supervisor)) {
        val firstChild = launch(CoroutineExceptionHandler { _, _ ->  }) {
            println("First Child Failed")
            throw Exception("Caught Exception") // 에러로 인해 취소되지만 부모 코루틴으로 전파 안함
        }

        val secondChild = launch {
            delay(1000)
            println("First Child State: ${firstChild.isCancelled}")
            println("Second Child") // 작업 완료
        }

        firstChild.join()
        secondChild.join()
    }
}


// First Child Failed
// First Child State: true
// Second Child
```
<br/>
<br/>



## supervisorScope
***
<code style="color: #eb5657;">coroutineScope</code> 와는 다르게 예외를 단방향 처리(부모 → 자식)하는 <code style="color: #eb5657;">CoroutineScope</code> 이다.
<br/>

블록 내부의 모든 코루틴에 <code style="color: #eb5657;">SupervisorJob</code> 을 설정하고 싶을 경우에 주로 사용한다.
<br/>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    supervisorScope {
        val firstChild = launch(CoroutineExceptionHandler { _, _ ->  }) {
            println("First Child Failed") // 에러로 인해 취소되지만 부모 코루틴으로 전파 안함
            throw Exception("Caught Exception")
        }

        val secondChild = launch {
            delay(1000)
            println("First Child State: ${firstChild.isCancelled}")
            println("Second Child") // 작업 완료
        }

        firstChild.join()
        secondChild.join()
    }
}


// First Child Failed
// First Child State: true
// Second Child
```
<br/>
<br/>



## CoroutineExceptionHandler
***
코루틴에서 예외 발생에 대한 처리를 할 수 있는 <code style="color: #eb5657;">CoroutineContext</code> 이다.
<br/>

스레드에서 캐치되지 않은 런타임 예외를 한 곳에서 처리할 수 있도록 도와주는 자바의 <code style="color: #eb5657;">Thread.defaultUncaughtExceptionHandler</code> 와 비슷하다.
<br/>
<br/>

### launch
<code style="color: #eb5657;">launch</code> 는 예외 발생 시 등록한 <code style="color: #eb5657;">CoroutineExceptionHandler</code> 에 위임하여 콜백으로 예외 처리가 가능하다.
<br/>

```kotlin
import kotlinx.coroutines.*

val exceptionHandler = CoroutineExceptionHandler { coroutineScope, exception ->
    Log.e("ERROR", "${exception.message}")
}

val ioDispatchers = Dispatchers.IO + exceptionHandler

GlobalScope.launch(ioDispatchers) {
    throw Exception()
}
```
<br/>

### async / withContext
<code style="color: #eb5657;">async</code>, <code style="color: #eb5657;">withContext</code> 는 외부에 <code style="color: #eb5657;">try-catch</code> 로 예외 처리가 가능하다.
<br/>

```kotlin
import kotlinx.coroutines.*

fun error() {
    try {
        GlobalScope.async {
            throw Exception()
        }
    } catch (e: Exception) {
        Log.e("ERROR", "${exception.message}")
    }
}

GloablScope.launch(Dispatchers.IO) {
    try {
        withContext(Dispatchers.Main) {
            throw Exception()
        }
    } catch (e: Exception) {
        Log.e("ERROR", "${exception.message}")
    }
}
```