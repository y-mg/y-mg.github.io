---
layout: post
title: "Coroutine"
date: 2020-11-02T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, Coroutine]
description: "비동기적으로 실행되는 코드를 간소화하기 위해 사용할 수 있는 동시 실행에 대한 패턴"
thumbnail: "/assets/images/kotlin/banner/coroutine.png"
image: "/assets/images/kotlin/banner/coroutine.png"
comments: false
---

## Thread / Coroutine
***
<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/coroutine_and_thread.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/coroutine_and_thread.png" style="visibility: hidden;" />
</div>
<br/>

프로세스(`Process`)는 `OS` 에서 실행 중인 프로그램의 인스턴스이다.
<br/>

스레드는 프로세스 내에서 실행되는 실행의 단위로, 작업(`Routine`, `Work`)이 실행되는 영역이다.
- `OS` 의 `Native Thread` 에 직접 링크되어 동작하여 많은 비용이 발생한다.
- 스레드 전환 시 `CPU` 상태 체크가 필요하기 때문에 그에 따른 비용이 발생한다.
<br/>
<br/>

코루틴은 비동기적으로 실행되는 코드를 간소화하기 위해 사용할 수 있는 동시 실행에 대한 패턴을 말한다.
- 생성된 루틴은 작업 전환 시에 `OS` 의 영향을 받지 않기 때문에 그에 따른 비용이 발생하지 않는다.
- 하나의 스레드의 영역안에서 여러 루틴을 실행할 수 있기에 비용이 감소한다.
<br/>
<br/>

결론적으로 코루틴은 스레드 내에서 실행되는 일시 중단 가능한 작업의 단위라도 볼 수 있으며, 하나의 스레드 안에 여러 개의 코루틴이 존재할 수 있다.
<br/>
<br/>
<br/>



## block / suspend
***
스레드의 중단(`block`)은 해당 스레드를 계속 점유하고 있는 것이기 때문에 중단되어 있는 동안 해당 스레드에서 다른 작업을 진행할 수 없다.
<br/>

```kotlin
fun main(){
    println("Coroutine")
    Thread.sleep(3000L) // 3초 동안 처리가 중지되며 이 시간은 동안 처리는 진행되지 않음
    println("Hello")
}


// Coroutine
// Hello
```
- 중단된 3초간 `Thread` 가 점유되기 때문에, 3초 이후에 Hello 가 출력된다.
<br/>
<br/>

코루틴이 일시 중단(suspend)이 되면 해당 스레드를 해제하고 해제하는 동안 다른 처리에 리소스를 활용할 수 있다.
<br/>

```kotlin
import kotlinx.coroutines.*

fun main(): Unit = runBlocking {
    launch {
        delay(3000L) // 3초간 처리를 중단되며 스레드가 해제되기 때문에 먼저 Hello 가 출력
        println("Coroutine")
    }
    println("Hello")
}


// Hello
// Coroutine
```
- 중단된 3초간 다른 작업에 리소스를 활용할 수 있기 때문에 Hello 가 먼저 출력된다.
<br/>
<br/>
<br/>



## Coroutine Keyword
***
### CoroutineScope
코루틴의 범위로 코루틴 블록을 묶음으로 제어할 수 있는 단위를 말한다.
<br/>
<br/>

### GlobalScope
`Top-Level` 의 `CoroutineScope` 로 `Application` 의 `Lifecycle` 에 종속적으로 존재한다.
<br/>
<br/>

### CoroutineContext
코루틴을 어떻게 처리할 것인지에 대한 데이터 집합이다.
<br/>
<br/>

### Dispatcher
`CoroutineContext` 의 주요 요소로 `CoroutineContext` 를 상속 받아 어떤 스레드를 어떻게 동작할 것인가에 대한 정의이다.
- 코루틴을 만들고 `Dispatcher` 에 전송하면 `Dispatcher` 는 자신이 관리하는 스레드 풀 내의 스레드의 상황에 맞게 코루틴을 배분한다.
- <code style="color: #eb5657;">Dispatchers.Main</code> 은 메인(`UI`) 스레드에서 동작하는 방식이다.
- <code style="color: #eb5657;">Dispatchers.IO</code> 는 네트워크 / `DB` 등 작업에 사용하는 방식이다. (`Background` `Task`)
- <code style="color: #eb5657;">Dispatchers.Default</code> 는 `CPU` 사용량이 많은 작업에서 사용하는 방식이다. (`Background` `Task`)