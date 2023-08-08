---
layout: post
title: "Observable"
date: 2019-08-07T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, ReactiveX]
description: "소비할 데이터를 발행하는 역할을 하는 구독 대상자"
thumbnail: "/assets/images/kotlin/banner/reactivex.png"
image: "/assets/images/kotlin/banner/reactivex.png"
comments: false
---

## Observable
***
소비할 데이터를 발행하는 역할을 하는 구독 대상자로, <code style="color: #eb5657;">Observable</code> 이 데이터를 발행하고 이벤트를 보내면 소비자는 소비자는 <code style="color: #eb5657;">Observable</code> 에 구독(`Subscribe`)해 데이터를 소비(`Consume`)한다.
<br/>
<br/>
<br/>



## Observable Callback
***
<code style="color: #eb5657;">Observable</code> 이 데이터를 발행한 후 보내는 이벤트는 4가지 종류가 있으며, `Callback` 함수를 구현하여 <code style="color: #eb5657;">Observable</code> 에 등록하면 <code style="color: #eb5657;">Observable</code> 이 전달하는 이벤트를 받을 수 있다.
<br/>

```kotlin
val observer: Observer<T> = object: Observer<T> {
    override fun onSubscribe(d :Disposable) = Unit
    override fun onNext(item :T) = Unit
    override fun onError(e :Throwable) = Unit
    override fun onComplete() = Unit
}
```
- <code style="color: #eb5657;">onSubscribe</code> 는 구독을 하면 호출이 되며, 파라미터의 <code style="color: #eb5657;">Disposable</code> 객체는 구독을 해제할 때 사용되며 <code style="color: #eb5657;">dispose()</code> 를 호출하면 된다.
- <code style="color: #eb5657;">onNext</code> 는 데이터를 발행할 때 호출되며 데이터를 전달한다.
- <code style="color: #eb5657;">onError</code> 는 에러가 발생하였을 경우에 호출되며, <code style="color: #eb5657;">onError</code>, <code style="color: #eb5657;">onComplete</code> 가 발생하지 않는다.
- <code style="color: #eb5657;">onComplete</code> 는 데이터의 발행이 완료되었을 때 호출되며, 딱 한 번만 발생한다.
<br/>
<br/>
<br/>



## Observable 구독
***
구독은 발행한 데이터를 수신하여 무엇을 할 것인지 행동을 정의하는 것으로, <code style="color: #eb5657;">subscribe()</code> 로 구독을 신청할 수 있다.
<br/>

```kotlin
public final Disposable subscribe() {
    /* ... */
}

public final Disposable subscribe(Consumer<? super T> onNext) {
    /* ... */
}

public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError) {
    /* ... */
}

public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError, Action onComplete) {
    /* ... */
}

public final void subscribe(Observer<? super T> observer) {
    /* ... */
}
```
- 여러 메소드가 오버로딩 되어있는데 파라미터가 없는 <code style="color: #eb5657;">subscribe()</code> 는 주로 테스트 혹은 디버깅 용도로 사용되며, <code style="color: #eb5657;">onError</code> 가 호출되면 <code style="color: #eb5657;">onErrorNotImplementedException</code> 를 <code style="color: #eb5657;">throw</code> 하고 끝난다.
- <code style="color: #eb5657;">Disposable</code> 객체는 구독을 해제할 때 사용되는데, <code style="color: #eb5657;">onComplete</code> 가 호출되면 <code style="color: #eb5657;">dispose()</code> 를 호출해 <code style="color: #eb5657;">Observable</code> 이 더 이상 데이터를 발행하지 않도록 구독을 해지해야 한다.
<br/>
<br/>

<code style="color: #eb5657;">Observable</code> 의 구독 방법은 사용자 필요 이벤트만 정의해서 등록하는 방법과, <code style="color: #eb5657;">Observable</code> 객체를 등록하는 방법 2가지가 있다.
<br/>

```kotlin
// 사용자 필요한 이벤트 정의
fun main() {
    // 4개 정의 (onNext / onError / onComplete / onSubscribe) 
    Observable.just(1)
        .subscribe(
            { println("onNext() - $it") },     // onNext()
            { println("onError() - $it") },	   // onError()
            { println("onComplete()") },	   // onComplete()
            { println("onSubscribe() - $it") } // onSubscribe()
        )

    // 2개 정의 (onNext / onError)
    Observable.just(1)
        .subscribe(
            { println("onNext() - $it") }, // onNext()
            { println("onError() - $it") } // onError()
        )
}
```

```kotlin
// Observer 객체 등록
val observer: Observer<Any> = object: Observer<Any> {
    override fun onSubscribe(d: Disposable) {
        println("onSubscribe() - $d")
    }
        
    override fun onNext(item: Any) {
        println("onNext() - $item")
    }
        
    override fun onError(e: Throwable) {
        println("onError() - ${e.message}")
    }
        
    override fun onComplete() {
        println("onComplete()\n")
    }
}
```
<br/>
<br/>



## Observable 구독 해지
***
<code style="color: #eb5657;">subscribe()</code> 로 구독을 하였다면 <code style="color: #eb5657;">onSubscribe(d: Disposable)</code> 호출로 <code style="color: #eb5657;">Disposable</code> 객체를 전달하여 구독을 해지할 수 있다.
<br/>

```kotlin
public interface Disposable {
    // 구독 해지 요청
    void dispose();
    
    // 구독 해지 상태 확인
    boolean isDisposed();   
}
```
<br/>

### Observer 를 통한 구독 해지
```kotlin
fun main() {
    val observer: Observer<Long> = object: Observer<Long> {
        lateinit var disposable: Disposable

        override fun onSubscribe(d: Disposable) {
            println("onSubscribe() $d")
            disposable = d
        }

        override fun onNext(item: Long) {
            println("onNext() $item")
            if (item >= 10 && disposable.isDisposed == false) {
                disposable.dispose()
                println("Subscribe Dispose")
            }
        }

        override fun onError(e: Throwable) { 
            println("onError() ${e.message}") 
        }

        override fun onComplete() { 
            println("onComplete()") 
        }
    }

    Observable
        .interval(100, TimeUnit.MILLISECONDS)
        .subscribe(observer)

    Thread() {
        sleep(1000)
    }.apply {
        start()
        join()
    }
}


// onSubscribe() = null
// onNext() 0
// onNext() 1
// onNext() 2
// onNext() 3
// onNext() 4
// onNext() 5
// onNext() 6
// onNext() 7
// onNext() 8
// onNext() 9
// onNext() 10
// Subscribe Dispose
```
<br/>

### Disposable 를 통한 구독 해지
```kotlin
fun main() {
    val observable = Observable.interval(100, TimeUnit.MILLISECONDS)
    
    val disposable: Disposable = observable.subscribe(
        { println("onNext() $it")},			
        { println("onError() ${it.message}")},
        { println("onComplete()")}			
    )

    Thread() {
        Thread.sleep(1000)
        disposable.dispose()
    }.apply {
        start()
        join()
    }
}


// onNext() 0
// onNext() 1
// onNext() 2
// onNext() 3
// onNext() 4
// onNext() 5
// onNext() 6
// onNext() 7
// onNext() 8
// onNext() 9
// onNext() 10
// onNext() 11
```