---
layout: post
title: "collect / collectLatest"
date: 2022-02-14T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, Coroutine]
description: "Flow 데이터 소비"
thumbnail: "/assets/images/kotlin/banner/coroutine.png"
image: "/assets/images/kotlin/banner/coroutine.png"
comments: false
---

## collect
***
<code style="color: #eb5657;">Flow</code> 에서 발행하는 데이터는 <code style="color: #eb5657;">collect</code> 를 이용해 소비할 수 있다.
<br/>

```kotlin
public suspend inline fun <T> Flow<T>.collect(crossinline action: suspend (value: T) -> Unit): Unit =
    collect(object: FlowCollector<T> {
        override suspend fun emit(value: T) = action(value)
    })
```
- 발행된 데이터는 <code style="color: #eb5657;">collect</code> 의 파라미터 <code style="color: #eb5657;">action</code> 의해 소비된다.
- <code style="color: #eb5657;">action</code> 블록은 발행된 데이터를 순차적으로 받아 <code style="color: #eb5657;">suspend fun</code> 을 수행한다.
<br/>
<br/>

<code style="color: #eb5657;">collect</code> 는 새로운 데이터가 발행되었을 때 이전 데이터의 처리가 끝난 후 새로운 데이터를 처리하는데 이로 인해 데이터의 발행 속도가 데이터의 소비 속도보다 빠를 경우 데이터의 소비가 지연될 수 있다.
<br/>

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    countUp().collect {
        println("Number: $it")
        delay(1000)
    }
}

fun countUp(): Flow<Int> = flow {
    for (i in 0..5) {
        emit(i)
        delay(100)
    }
}


// Number: 0
// Number: 1
// Number: 2
// Number: 3
// Number: 4
// Number: 5
```
- 0.1초마다 데이터가 발행되고 0부터 5까지 0.5초만에 모든 데이터의 발행이 완료되었지만 1개의 데이터를 소비하는데 1초씩 걸려서 모두 출력되는데 5초가 걸린다.
<br/>
<br/>
<br/>



## collectLatest
***
<code style="color: #eb5657;">collectLatest</code> 는 새로운 데이터가 발행되었을 때, 이전 데이터의 처리를 강제로 종료시키고 새로운 데이터를 처리하기 때문에 항상 최신의 데이터를 소비한다.
<br/>

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    countUp().collectLatest {
        println("Number: $it")
        delay(1000)
    }
}

fun countUp(): Flow<Int> = flow {
    for (i in 0..5) {
        emit(i)
        delay(100)
    }
}


// Number: 0
// Number: 1
// Number: 2
// Number: 3
// Number: 4
// Number: 5
```
- 데이터 소비에 있어 <code style="color: #eb5657;">delay</code> 를 1초 주었지만 <code style="color: #eb5657;">delay</code> 도중에 취소되고 새로운 데이터로 발행되어 결국 0.5초만에 모든 작업이 완료된다.
<br/>
<br/>

<code style="color: #eb5657;">collectLatest</code> 는 최신의 데이터를 유지하려는 특성이 있지만, 이로 인해 데이터를 발행하는 시간 사이의 간격보다 데이터를 처리하는 시간이 오래 걸릴 경우에 새로운 들어오는 데이터는 계속해서 취소되어 중간 데이터를 하나도 얻지 못하고 마지막 데이터만을 받게 된다.
<br/>

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    countUp().collectLatest {
        delay(1000)
        println("Number: $it")
    }
}

fun countUp(): Flow<Int> = flow {
    for (i in 0..5) {
        emit(i)
        delay(100)
    }
}


// Number: 5
```
- 0부터 4까지는 데이터가 소비되는 중간에 모두 취소되고 5만 소비되어 5만 출력된다.