---
layout: post
title: "StateFlow / SharedFlow"
date: 2022-02-23T00:00:00Z
authors: ["y-mg"]
categories: [Android, Coroutine]
description: "Flow 로부터 발행된 데이터를 저장할 데이터 홀더"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## StateFlow
***
<code style="color: #eb5657;">Flow</code> 는 데이터를 발행할 뿐 데이터가 저장되지는 않기 때문에 안드로이드에서 <code style="color: #eb5657;">Flow</code> 를 사용해서 `UI` 에 데이터를 바인딩하기 위해서는 화면이 재구성될 때마다 데이터를 가져오는 방법이 있고, <code style="color: #eb5657;">collect</code> 한 데이터를 `ViewModel` 에 저장하는 방법이 있다.
<br/>

화면이 재구성될 때마다 데이터를 가져오는 방법은 비효율적이기 때문에 `ViewModel` 에 저장해놓고 사용하는 방법이 옳은데, 이를 위해서는 <code style="color: #eb5657;">LiveData</code> 와 같은 데이터 홀더가 필요하다.
<br/>

<code style="color: #eb5657;">Flow</code> 에서는 <code style="color: #eb5657;">StateFlow</code> 라는 데이터 홀더를 제공해주는데 데이터의 홀더 역할을 하면서 데이터 스트림의 역할까지 한다.
<br/>

따라서 <code style="color: #eb5657;">StaeFlow</code> 를 사용하게 되면 단순히 <code style="color: #eb5657;">StaeFlow</code> 를 구독해 데이터가 들어오면 소비하면 된다.
<br/>

<code style="color: #eb5657;">StaeFlow</code> 는 <code style="color: #eb5657;">SharedFlow</code> 의 상속을 받는 `Hot` `Stream` 으로 <code style="color: #eb5657;">collect</code> 시점 이후부터 발행된 데이터를 소비할 수 있으며, 내부적으로 <code style="color: #eb5657;">distinctUntilChanged()</code> 기능이 있어서 중복된 데이터가 발행되는 경우에는 데이터를 받지 않게 되며, 초기값 설정이 필수이고 데이터 바인딩을 지원한다.
<br/>

<code style="color: #eb5657;">StaeFlow</code> 는 단일 데이터의 상태 변화를 관리하고 전달하데 사용되며, 주로 UI 상태에 앱의 상태를 관리할 떄 사용된다.
<br/>
<br/>

### stateIn
<code style="color: #eb5657;">Flow</code> 를 <code style="color: #eb5657;">StateFlow</code> 로 변환하기 위해서는 <code style="color: #eb5657;">stateIn()</code> 을 사용하면 된다.
<br/>

```kotlin
public fun <T> Flow<T>.stateIn(
    scope: CoroutineScope,
    started: SharingStarted,
    initialValue: T
): StateFlow<T> {
    val config = configureSharing(1)
    val state = MutableStateFlow(initialValue)
    val job = scope.launchSharing(config.context, config.upstream, state, started, initialValue)
    return ReadonlyStateFlow(state, job)
}
```
- `scope` 는 <code style="color: #eb5657;">StateFlow</code> 가 <code style="color: #eb5657;">Flow</code> 로부터 데이터를 구독받을 코루틴 스코프를 설정하면 된다.
- `started` 는 <code style="color: #eb5657;">Flow</code> 로부터 언제부터 구독을 시작할지를 설정하면 된다.
- `initialValue` 는 <code style="color: #eb5657;">StateFlow</code> 에 설정할 초기값이다.
<br/>
<br/>
<br/>



## SharedFlow
***
<code style="color: #eb5657;">SharedFlow</code> 는 <code style="color: #eb5657;">StateFlow</code> 보다 상세한 설정이 가능한 데이터 스트림으로, <code style="color: #eb5657;">collect</code> 시점 이후부터 발행된 데이터를 소비할 수 있고, 초기값 설정을 하지 않아도 되며 데이터 바인딩을 지원하지 않는다.

<code style="color: #eb5657;">SharedFlow</code> 는 데이터를 다수의 구독자 간에 공유하면서 각자의 속도로 데이터를 처리할 수 있도록 유연한 데이터 처리를 지원한다.
<br/>
<br/>

### replay
<code style="color: #eb5657;">collect</code> 시 전달받을 이전 데이터의 갯수를 지정하는 옵션으로, 만약 1이라면 <code style="color: #eb5657;"></code> 직전의 데이터를 전달받고 시작하게 된다.
<br>
<br>

### extraBufferCapacity
버퍼의 크기를 설정하는 옵션이다.
<br>
<br>

### onBufferOverflow
버퍼가 전부 찼을때의 동작을 정의하는 옵션이다.
- <code style="color: #eb5657;">BufferOverflow.DROP_OLDEST</code> 로 설정하면 버퍼가 전부 찼을 때 데이터가 들어오면 오래된 데이터부터 삭제하고 새로운 데이터가 전달된다.
- <code style="color: #eb5657;">BufferOverflow.DROP_LATEST</code> 로 설정하면 버퍼가 전부 찼을 때 데이터가 들어오면 가장 최근의 데이터를 삭제하고 새로운 데이터가 전달된다.
- <code style="color: #eb5657;">BufferOverflow.SUSPEND</code> 로 설정하면 버퍼가 전부 찼을 때 더 이상 데이터가 들어오지 않고 `Blocking` 된다.