---
layout: post
title: "Schedulers"
date: 2019-08-15T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, ReactiveX]
description: "코드가 어느 스레드에서 실행될 것인지를 지정하는 역할"
thumbnail: "/assets/images/kotlin/banner/reactivex.png"
image: "/assets/images/kotlin/banner/reactivex.png"
comments: false
---

## Scheduler
***
스케줄러의 역할은 코드가 어느 스레드에서 실행될 것인지를 지정하는 역할로 스케줄러를 통해 스레드를 분리해주어야 비동기 처리가 가능하다.
<br/>

<code style="color: #eb5657;">Observable</code> 스레드를 지정할 수 있는 역할로 <code style="color: #eb5657;">subscrbieOn()</code>, <code style="color: #eb5657;">observeOn()</code> 로 지정이 가능하며 기본적으로 스케줄러를 별도로 지정하지 않으면 호출한 스레드에서 동작한다.
<br/>
<br/>

### subscribeOn()
<code style="color: #eb5657;">Observable</code> 이 데이터를 발행하고 연산하는 스레드를 지정할 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/scheduler-subscribeon.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/scheduler-subscribeon.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    Observable.just(1, 2, 3)
        .map {
            println("map: $it - ${Thread.currentThread().name}")
            it
        }
        .subscribeOn(Schedulers.io())
        .subscribe {
            println("subscribe: $it - ${Thread.currentThread().name}")
        }

    delay(1000)
}


// map: 1 - RxCashedThreadScheduler-1
// subscribe: 1 - RxCashedThreadScheduler-1
// map: 2 - RxCashedThreadScheduler-1
// subscribe: 2 - RxCashedThreadScheduler-1
// map: 3 - RxCashedThreadScheduler-1
// subscribe: 3 - RxCashedThreadScheduler-1
```
- 데이터를 생산하는 스레드와 데이터를 소비하는 스레드가 동일하다.
<br/>
<br/>

### observeOn()
<code style="color: #eb5657;">Observable</code> 이 소비자에게 알림을 보내는 스레드를 지정할 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/scheduler-observeron.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/scheduler-observeron.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    Observable.just(1)
        .observeOn(Schedulers.io())
        .map {
            println("map[1] - ${Thread.currentThread().name}")
            it
        }
        .observeOn(Schedulers.computation())
        .map {
            println("map[2] - ${Thread.currentThread().name}")
            it
        }
        .observeOn(Schedulers.single())
        .subscribe {
            println("subscribe - ${Thread.currentThread().name}")
        }

    delay(1000)
}


// map[1] - RxCashedThreadScheduler-1
// map[2] - RxComputationThreadPool-1
// subscribe - RxSingleScheduler-1
```
<br/>
<br/>



## Schedulers.newThread()
***
요청을 받을 때마다 새로운 스레드를 생성하여 작업하는 스케줄러이다.
<br/>

```kotlin
fun main() {
    val stream = Observable.just(1, 2, 3)
		
    stream.subscribeOn(Schedulers.newThread())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[1] - ${Thread.currentThread().name}")
        }
		
	stream.subscribeOn(Schedulers.newThread())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[2] - ${Thread.currentThread().name}")
        }

    stream.subscribeOn(Schedulers.newThread())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[3] - ${Thread.currentThread().name}")
        }

    sleep(1000)
}


// 1: Observable[1] - RxNewThreadSchedulers-1
// 1: Observable[3] - RxNewThreadSchedulers-3
// 1: Observable[2] - RxNewThreadSchedulers-2
// 1: Observable[2] - RxNewThreadSchedulers-2
// 1: Observable[3] - RxNewThreadSchedulers-3
// 1: Observable[1] - RxNewThreadSchedulers-1
// 1: Observable[3] - RxNewThreadSchedulers-3
// 1: Observable[2] - RxNewThreadSchedulers-2
// 1: Observable[1] - RxNewThreadSchedulers-1
```
- <code style="color: #eb5657;">newThread()</code> 로 생성된 <code style="color: #eb5657;">Observable</code> 은 서로 다른 스레드이기에 비동기로 출력된다.
<br/>
<br/>
<br/>



## Scheduler.io()
***
파일 입출력 등의 `IO` 작업을 하거나 네트워크 요청 처리 시에 사용하는 스케줄러로 내부적으로 `CashedPool` 사용하여 스레드가 필요할 때 마다 계속 생성되며 유후 스레드가 존재하면 재활용한다.
<br/>

```kotlin
fun main() {
    val stream = Observable.just(1, 2, 3)
		
    println("start1")

    stream.subscribeOn(Schedulers.io())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[1] - ${Thread.currentThread().name}")
        }

    println("start2")

    stream.subscribeOn(Schedulers.io())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[2] - ${Thread.currentThread().name}")
        }

    println("end")

    delay(1000)
}


// start1
// start2
// end
// 1: Observable[2] - RxCachedThreadSchedulers-1
// 1: Observable[1] - RxCachedThreadSchedulers-1
// 2: Observable[1] - RxCachedThreadSchedulers-1
// 2: Observable[2] - RxCachedThreadSchedulers-1
// 3: Observable[1] - RxCachedThreadSchedulers-1
// 3: Observable[2] - RxCachedThreadSchedulers-1
```
<br/>
<br/>
<br/>



## Scheduler.computation()
***
별도의 `IO` 없고 일반적인 연산 작업을 할 때 사용하는 스케줄러로 내부적으로 스레드 풀을 생성하고 생성된 스레드를 사용하며 기본적으로 스레드의 개수는 프로세서의 개수와 같다.
- <code style="color: #eb5657;">interval()</code>, <code style="color: #eb5657;">timer()</code> 는 기본적으로 계산 스케줄러를 사용한다.
<br/>

```kotlin
fun main() {
    val stream = Observable.just(1, 2, 3)
		
    stream.subscribeOn(Schedulers.computation())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: ${Thread.currentThread().name}")
        }

    delay(1000)
}


// 1: RxComputationThreadPool-1
// 2: RxComputationThreadPool-1
// 3: RxComputationThreadPool-1
```
<br/>
<br/>
<br/>



## Scheduler.single()
***
단일 스레드를 계속 재사용하는 스케줄러로 스레드를 만들어 해당 스레드 큐에 작업을 넘기는 방식으로 한 번 생성된 스레드로 여러 작업을 처리한다.
<br/>

```kotlin
fun main() {
    val stream = Observable.just(1, 2, 3)
		
    println("start1")

    stream.subscribeOn(Schedulers.single())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[1] - ${Thread.currentThread().name}")
        }

    println("start2")

    stream.subscribeOn(Schedulers.single())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[2] - ${Thread.currentThread().name}")
        }

    println("end")

    delay(1000)
}


// start1
// start2
// end
// 1: Observable[1] - RxSingleScheduler-1
// 2: Observable[1] - RxSingleScheduler-1
// 3: Observable[1] - RxSingleScheduler-1
// 1: Observable[2] - RxSingleScheduler-1
// 2: Observable[2] - RxSingleScheduler-1
// 3: Observable[2] - RxSingleScheduler-1
```
<br/>
<br/>
<br/>



## Scheduler.trampoline()
***
새로운 스레드를 생성하지 않고 호출한 스레드 큐에 작업을 넘겨주는 방식의 스케줄러로 `Queuing` 된 모든 작업이 종료되어야 다음 라인의 코드가 실행된다.
<br/>

```kotlin
fun main() {
    val stream = Observable.just(1, 2, 3)
		
    println("start1")

    stream.subscribeOn(Schedulers.trampoline())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[1] - ${Thread.currentThread().name}")
        }

    println("start2")

    stream.subscribeOn(Schedulers.trampoline())
        .subscribe {
            runBlocking { delay(100) }
            println("$it: Observable[2] - ${Thread.currentThread().name}")
        }

    println("end")

    delay(1000)
}


// start1
// 1: Observable[1] - main
// 2: Observable[1] - main
// 3: Observable[1] - main
// start2
// 1: Observable[2] - main
// 2: Observable[2] - main
// 3: Observable[2] - main
// end
```