---
layout: post
title: "Completable"
date: 2019-08-23T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, ReactiveX]
description: "데이터를 발행의 완료 혹은 에러에만 이벤트를 발생시키는 특수한 형태"
thumbnail: "/assets/images/kotlin/banner/reactivex.png"
image: "/assets/images/kotlin/banner/reactivex.png"
comments: false
---

## Completable
***
데이터를 발행하는 <code style="color: #eb5657;">Observable</code>, <code style="color: #eb5657;">Single</code>, <code style="color: #eb5657;">Maybe</code> 와 달리 데이터의 발행의 완료(<code style="color: #eb5657;">onComplete</code>) 혹은 에러(<code style="color: #eb5657;">onError</code>)에만 이벤트를 발생시키는 특수한 형태이다.
- <code style="color: #eb5657;">onComplete</code> 는 데이터의 발행이 완료되었음을 알린다.
- <code style="color: #eb5657;">onError</code> 는 에러가 발생하였음을 알린다.
<br/>
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/kotlin/content/completable.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/kotlin/content/completable.png" style="visibility: hidden;" />
</div>

```kotlin
fun main() {
    Completable.create{
        it.onComplete()
    }.subscribeBy(
        onComplete = {
            println("onComplete")
        },
        onError = {
            println("onError")
        }
    )
}


// onComplete
```