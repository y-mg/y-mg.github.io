---
layout: post
title: "collect with buffer"
date: 2022-02-15T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, Coroutine]
description: "buffer 를 이용한 collect 최적화"
thumbnail: "/assets/images/kotlin/banner/coroutine.png"
image: "/assets/images/kotlin/banner/coroutine.png"
comments: false
---

## collect 의 한계
***
<code style="color: #eb5657;">Flow</code> 의 <code style="color: #eb5657;">collect</code> 는 데이터의 발행과 데이터의 소비가 순차적으로 진행되기 때문에 데이터 발행 후 소비가 끝나고 나서 다음 데이터가 발행된다.
<br/>

이로 인해 <code style="color: #eb5657;">collect</code> 는 데이터의 발행 혹은 소비를 수행하는데 있어서 지연이 발생하면 양쪽 모두에서 지연이 생기게 된다.
<br/>

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    countUp()
        .onEach {
            println("Number-Emit: $it")
        }
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
// Number-Consume: 0
// Number-Emit: 1
// Number-Consume: 1
// Number-Emit: 2
// Number-Consume: 2
// Number-Emit: 3
// Number-Consume: 3
// Number-Emit: 4
// Number-Consume: 4
// Number-Emit: 5
// Number-Consume: 5
```
- 데이터를 발행하는데 1초, 데이터를 소비하는데 3초가 걸려서 데이터의 발행과 소비에 총 4초가 소요되며 이는 데이터의 발행에 지연이 발생하면 데이터를 늦게 받아 소비하는 쪽에도 지연이 발생하게 된다.
<br/>
<br/>
<br/>



## buffer
***
<code style="color: #eb5657;">Flow</code> 의 <code style="color: #eb5657;">collect</code> 사용 시 데이터의 발행 혹은 소비를 수행하는데 있어서 지연이 발생하면 양쪽 모두에서 지연되는 문제점을 해결하는 방법은 <code style="color: #eb5657;">buffer</code> 를 사용하여 데이터의 발행을 위한 코루틴과 데이터 소비를 위한 코루틴으로 분리하는 것이다.
<br/>

데이터의 발행과 소비의 코루틴이 분리된다면 데이터의 소비가 완료되었을 때 지연없이 데이터의 발행이 되어서 데이터의 발행은 발행대로, 데이터의 소비는 소비대로 실행되기 때문이다.
<br/>

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    countUp()
        .onEach {
            println("Number-Emit: $it")
        }
        .buffer()
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
// Number-Consume: 1
// Number-Consume: 2
// Number-Consume: 3
// Number-Consume: 4
// Number-Consume: 5
```