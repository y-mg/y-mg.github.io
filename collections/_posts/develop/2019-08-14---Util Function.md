---
layout: post
title: "Util Function"
date: 2019-08-14T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, ReactiveX]
description: "delay / timeInterval / debounce / throttleFirst / throttleLast"
thumbnail: "/assets/images/kotlin/banner/reactivex.png"
image: "/assets/images/kotlin/banner/reactivex.png"
comments: false
---

## delay()
***
일정 시간을 받아서 데이터 발행을 지연시켜주는 함수이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/util-delay.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/util-delay.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val data = listOf("1", "2", "3", "4", "5")
		
    Observable.fromIterable(data)
        .delay(500L, TimeUnit.MILLSECONDS) // 0.5초 이후에 데이터를 발행
        .subscribe(System.out::println)
		
    sleep(1000)
}


// 1
// 2
// 3
// 4
// 5
```
<br/>
<br/>



## timeInterval()
***
이전 값을 발행한 이후 시간이 얼마나 지났는지 알려주는 함수이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/util-timeinterval.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/util-timeinterval.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val data = listOf("1", "2", "3")
		
    Observable.fromIterable(data)
        .delay(
            sleep(Random().nextInt(100).toLong())
            return@delay Observable.just(it)
        )
        .timeInterval()
        .subscribe(System.out::println) // Timed 객체가 전달
		
    sleep(1000)
}


// Timed[time=57, unit=MILLISECONDS, value=1]
// Timed[time=31, unit=MILLISECONDS, value=2]
// Timed[time=45, unit=MILLISECONDS, value=3]
```
<br/>
<br/>



## debounce()
***
일정시간이 지난 후 제일 마지막으로 들어온 값만 발행하는 함수이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/util-debounce.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/util-debounce.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val input = BehaviorSubject.create<String>()
    for (i in 0..10) {
        val text = "${i}초"
        input.onNext(text)
        sleep(1000)
    }

    input.debounce(5000L, TimeUnit.MILLSECONDES)
        .subscribeOn(Scheduler.computation())
        .subscribe(System.out::println)
}


// 10초
```
<br/>
<br/>



## throttleFirst()
***
입력값이 들어온 후, 일정시간이 지난 후에 가장 처음 들어온 값을 발행하는 함수이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/util-throttlefirst.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/util-throttlefirst.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val input = BehaviorSubject.create<String>()
    for (i in 0..10) {
        val text = "${i}초"
        input.onNext(text)
        sleep(1000)
    }

    input.throttleFirst(5000L, TimeUnit.MILLSECONDES)
        .subscribeOn(Scheduler.computation())
        .subscribe(System.out::println)
}


// 0초
// 6초
// 12초
// 18초
```
<br/>
<br/>



## throttleLast()
***
입력값이 들어온 후, 일정시간이 지난 후에 가장 마지막으로 들어온 값을 발행하는 함수이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/util-throttlelast.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/util-throttlelast.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    val input = BehaviorSubject.create<String>()
    for (i in 0..10) {
        val text = "${i}초"
        input.onNext(text)
        sleep(1000)
    }

    input.throttleLast(5000L, TimeUnit.MILLSECONDES)
        .subscribeOn(Scheduler.computation())
        .subscribe(System.out::println)
}


// 4초
// 8초
// 14초
// 18초
// 20초
```