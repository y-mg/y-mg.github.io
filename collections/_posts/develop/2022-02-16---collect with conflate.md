---
layout: post
title: "collect with conflate"
date: 2022-02-16T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, Coroutine]
description: "conflate 를 이용한 최신 데이터 소비"
thumbnail: "/assets/images/kotlin/banner/coroutine.png"
image: "/assets/images/kotlin/banner/coroutine.png"
comments: false
---

## collectLatest 의 한계
***
<code style="color: #eb5657;">collectLatest</code> 는 새로운 데이터가 발행되었을 때 이전 데이터의 처리를 강제로 종료시키고 새로운 데이터를 처리하여 항상 최신의 데아터를 소비한다.
<br/>

이로 인해 <code style="color: #eb5657;">collectLatest</code> 는 데이터를 발행하는 시간보다 데이터를 처리하는 시간이 오래 걸릴 경우에 새로운 들어오는 중간의 데이터는 계속해서 소비되지 못하고 마지막 데이터만 소비하게 된다.
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
- 데이터를 발행하는데 0.1초, 데이터를 소비하는데 1초가 걸려서 중간의 데이터들은 소비되지 못하고 마지막 데이터만 소비된다.
<br/>
<br/>
<br/>



## conflate
***
<code style="color: #eb5657;">conflate</code> 는 한 번 시작된 데이터의 소비를 끝날 때 까지 진행하고 데이터의 소비가 완료되는 시점에 가장 최신의 데이터를 다시 소비하는 것이다.
<br/>

이로 인해 <code style="color: #eb5657;">collectLatest</code> 의 문제를 해결할 수 있다.
<br/>

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    countUp()
        .onEach {
            println("Number-Emit: $it")
        }
        .conflate()
        .collect {
            delay(3000)
            println("Number-Consume: $it")
        }
}

fun countUp(): Flow<Int> = flow {
    for (i in 0..5) {
        emit(i)
        delay(1000)
    }
}


// Number-Emit: 0
// Number-Emit: 1
// Number-Emit: 2
// Number-Consume: 0
// Number-Emit: 3
// Number-Emit: 4
// Number-Emit: 5
// Number-Consume: 2
// Number-Consume: 5
```
- 0이 발행된 후 0에 대한 소모가 3초 후에 완료되며, 추가적으로 1과 2가 발행되고 3초 시점에서 마지막으로 발행된 데이터는 2가 된다.
- 2에 대한 소모가 시작 시점으로부터 6초 후에 완료되며, 추가적으로 3부터 5까지 발행되고 6초 시점에서 마지막으로 발행된 데이터는 5가 된다.
- 5에 대한 소모가 시작 시점으로부터 9초 시점에 완료된다.