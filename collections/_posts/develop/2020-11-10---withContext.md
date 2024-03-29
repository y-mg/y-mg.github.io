---
layout: post
title: "withContext"
date: 2020-11-10T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, Coroutine]
description: "코루틴을 만드는 코루틴 빌더 (CoroutineBuilder)"
thumbnail: "/assets/images/kotlin/banner/coroutine.png"
image: "/assets/images/kotlin/banner/coroutine.png"
comments: false
---

## withContext
***
코루틴을 만드는 코루틴 빌더로, <code style="color: #eb5657;">async</code> 는 <code style="color: #eb5657;">await()</code> 을 호출해야 결과값을 받지만 <code style="color: #eb5657;">withContext</code> 는 처음부터 결과값을 반환할 때까지 대기한다.
- 내부 코루틴 블록이 끝나고 결과값이 반환될 때까지 종료하지 않기에 <code style="color: #eb5657;">try-finally</code> 블록에서 자원 반납 등 코루틴을 마무리하는데 주로 사용한다.
- 코루틴 취소 시 <code style="color: #eb5657;">CancellationException</code> 발생시키고 <code style="color: #eb5657;">try-finally</code> 블록에서 예외 처리를 한 경우 코루틴이 취소된 상황이기에 다른 중단 함수 등을 호출할 수 없으므로 <code style="color: #eb5657;">withContext</code> + <code style="color: #eb5657;">NonCancellable</code> 을 사용해야 한다.
<br/>

```kotlin
fun main() = runBlocking {
    val job = launch {
        try {
            repeat(1000) {
                println("Sleeping... $it")
                delay(1000)
            }
        } finally {
            withContext(NonCancellable) {
                delay(1000)
                println("Bye!!")
            }
        }
    }

    delay(2000)
    println("Start")
    job.cancelAndJoin() // CancellationException 발생
    println("End")
}


// Sleeping... 0
// Sleeping... 1
// Start
// Bye!!
// End
```
<br/>
<br/>



## withContext(NonCancellable)
***
<code style="color: #eb5657;">withContext(NonCancellable)</code> 사용 시 코루틴이 죽지 않고 끝나는 이유는 <code style="color: #eb5657;">isActive()</code> 가 항상 <code style="color: #eb5657;">true</code> 를 반환하도록 구현이 되어있기 때문에 코루틴이 `Completed`(종료)될 때 까지 무조건 <code style="color: #eb5657;">true</code> 를 반환해서 취소 예외에 영향 없이 작업을 수행하기 때문이다.
<br/>

```kotlin
@Suppress("DeprecatedCallableAddReplaceWith")
public object NonCancellable : AbstractCoroutineContextElement(Job), Job {

    private const val message = "NonCancellable can be used only as an argument for 'withContext', direct usages of its API are prohibited"

    /**
     * Always returns `true`.
     * @suppress **This an internal API and should not be used from general code.**
     */
    @Deprecated(level = DeprecationLevel.WARNING, message = message)
    override val isActive: Boolean
        get() = true

    /**
     * Always returns `false`.
     * @suppress **This an internal API and should not be used from general code.**
     */
    @Deprecated(level = DeprecationLevel.WARNING, message = message)
    override val isCompleted: Boolean get() = false

    /**
     * Always returns `false`.
     * @suppress **This an internal API and should not be used from general code.**
     */
    @Deprecated(level = DeprecationLevel.WARNING, message = message)
    override val isCancelled: Boolean get() = false

    /* ... */
}
```