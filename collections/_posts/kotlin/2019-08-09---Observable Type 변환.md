---
layout: post
title: "Observable Type 변환"
date: 2019-08-09T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, ReactiveX]
description: "ConnectableObservable / Subject"
thumbnail: "/assets/images/kotlin/banner/reactivex.png"
image: "/assets/images/kotlin/banner/reactivex.png"
comments: false
---

## ConnectableObservable
***
`Hot` `Observable` 중 하나로 <code style="color: #eb5657;">connect()</code> 함수를 호출하면 데이터 발행을 시작하는 <code style="color: #eb5657;">Observable</code> 로, <code style="color: #eb5657;">publish()</code> 함수는 <code style="color: #eb5657;">connect()</code> 함수가 호출되기 전까지 데이터의 발행을 유예하며 `Cold` `Observable` 을 `Hot` `Observable` 로 변환을 한다.
<br/>

```kotlin
fun main() {
    val connectableObservable = (1..4).toObservable().publish()

    connectableObservable.subscribe { println("First Observer $it")}
    println("Add First Observer")

    connectableObservable.subscribe { println("Second Observer $it")}
    println("Add Second Observer")

    connectableObservable.connect()
    
    // 이미 위의 connect() 호출되어 데이터가 발행된 후라 데이터를 수신하지 못함
    connectableObservable.subscribe { println("Third Observer $it")}
    println("Add Third Observer")
}


// Add First Observer
// Add Second Observer
// First Observer 1
// Second Observer 1
// First Observer 2
// Second Observer 2
// First Observer 3
// Second Observer 3
// First Observer 4
// Second Observer 4
// Add Third Observer
```

```kotlin
fun main() {
    val connectableObservable = 
        Observable.interval(100, TimeUnit.MILLISECONDS).publish()

    connectableObservable.subscribe { 
        println("First Observer $it") 
    }

    // publish() 로 Hot Observable 로 변환되어서 subscirbe() 가 아닌 
    // connect() 시점에 데이터 발행
    connectableObservable.connect()

    runBlocking { delay(300) }

    connectableObservable.subscribe { 
        println("Second Observer $it") 
    }

    runBlocking { delay(300) }
}


// First Observer 0
// First Observer 1
// First Observer 2
// First Observer 3
// Second Observer 3
// First Observer 4
// Second Observer 4
// First Observer 5
// Second Observer 5
```
<br/>
<br/>



## Subject
***
`Cold` `Observable` 를 `Hot` `Observable` 로 변환하는 클래스로, 데이터를 발행하는 <code style="color: #eb5657;">Observable</code> 이면서 <code style="color: #eb5657;">Observable</code> 를 구독하여 데이터를 소비하는 소비자 역할을 수행한다.
<br/>
<br/>

### PublishSubject
등록 시점 이후부터 데이터를 수신하는 `Subject` 이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/subject-publish.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/subject-publish.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val observable = Observable.interval(100, TimeUnit.MILLISECONDS)	

    val subject = PublishSubject.create<Long>()

    // subject 가 구독
    observable.subscribe(subject)			
    runBlocking { delay(300) }

    // subject 에 1번째 Observer 등록
    subject.subscribe { println("1st $it") }
    runBlocking { delay(300) }

    // subject 에 2번째 Observer 등록
    subject.subscribe { println("2st $it") }
    runBlocking { delay(300) }
}


// 1st 3
// 1st 4
// 1st 5
// 1st 6
// 2st 6
// 1st 7
// 2st 7
// 1st 8
// 2st 8
```
<br/>

### BehaviorSubject
등록 시점 이전에 배출된 직전의 값 하나를 전달받고 시작하는 `Subject` 로, 구독 시점에 데이터가 없으면 기본값을 전달한다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/subject-behavior.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/subject-behavior.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val subject = BehaviorSubject.createDefault("5")
		
    subject.subscribe { data -> println("#1 -> $data") }
    subject.onNext("1")
    subject.onNext("2")

    subject.subscribe { data -> println("#2 -> $data") }
    subject.onNext("3")
    subject.onComplete()
}


// #1 -> 5
// #1 -> 1
// #1 -> 2
// #2 -> 2
// #1 -> 3
// #2 -> 3
```
<br/>

### AsyncSubject
<code style="color: #eb5657;">onComplete()</code> 가 호출된 이후 가장 최신 데이터인 <code style="color: #eb5657;">Observable</code> 의 마지막 값을 한 번만 배출하는 `Subject` 로, <code style="color: #eb5657;">Observable</code> 의 마지막 발행된 값을 저장하고 이후 구독 시 저장한 마지막 값을 발행한다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/subject-async.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/subject-async.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val observable = Observable.range(1, 10)

    val subject = AsyncSubject.create<Int>()

    // subject 가 구독
    observable.subscribe(subject)			

    // subject 에 1번째 Observer 등록
    subject.subscribe { println("1st $it") }
    runBlocking { delay(300) }

    // subject 에 2번째 Observer 등록
    subject.subscribe { println("2st $it") }
}


// 1st 10
// 2st 10
```
<br/>

### ReplaySubject
`Cold` `Observable` 와 비슷하게 등록 시점 이전값을 모두 수신 받은 후 새로 발행되는 값을 전달 받는 `Subject` 이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/subject-replay.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/subject-replay.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val observable = Observable.interval(100, TimeUnit.MILLISECONDS)	

    val subject = ReplaySubject.create<Long>()

    // subject 가 구독
    observable.subscribe(subject)			
    runBlocking { delay(300) }

    // subject 에 1번째 Observer 등록
    subject.subscribe { println("1st $it") }
    runBlocking { delay(300) }

    // subject 에 2번째 Observer 등록
    subject.subscribe { println("2st $it") }
    runBlocking { delay(300) }
}


// 1st 0
// 1st 1
// 1st 2
// 1st 3
// 2st 0
// 2st 1
// 2st 2
// 2st 3
// 1st 4
// 2st 4
// 1st 5
// 2st 5
// 1st 6
// 2st 6
```