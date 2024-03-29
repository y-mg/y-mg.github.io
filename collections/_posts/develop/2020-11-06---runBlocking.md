---
layout: post
title: "runBlocking"
date: 2020-11-06T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, Coroutine]
description: "코루틴을 만드는 코루틴 빌더 (CoroutineBuilder)"
thumbnail: "/assets/images/kotlin/banner/coroutine.png"
image: "/assets/images/kotlin/banner/coroutine.png"
comments: false
---

## runBlocking
***
코루틴을 만드는 코루틴 빌더로 코드 블록의 작업이 끌날때 까지 스레드를 점유하고 대기한다.
- <code style="color: #eb5657;">runBlocking</code> 다음 라인의 코드를 실행하지 못하게 막는다.
- 자식 스레드가 완료될 때 까지 현재 스레드를 `block` 하며 점유하게 되는 스레드는 <code style="color: #eb5657;">runBlocking</code> 이 호출된 위치의 스레드이다.
- <code style="color: #eb5657;">runBlocking<type: T>()</code> 은 반환 받을 타입을 지정할 수 있으며 기본값은 <code style="color: #eb5657;">Unit</code> 으로 생략이 가능하다.
<br>

```kotlin
import kotlinx.coroutines.*

fun main() {
    blocking()
}

fun blocking() = runBlocking {
    var job = launch {
        delay(5000)
        println("1")
    }
    println("2")
}


// 2
// 1
```