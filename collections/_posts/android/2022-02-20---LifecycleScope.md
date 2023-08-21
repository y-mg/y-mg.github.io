---
layout: post
title: "LifecycleScope"
date: 2022-02-20T00:00:00Z
authors: ["y-mg"]
categories: [Android, Coroutine]
description: "안드로이드의 생명주기를 인식하는 코루틴 스코프"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## LifecycleScope
***
안드로이드의 생명주기를 인식하는 코루틴 스코프이다.
<br/>

```kotlin
lifecycleScope.launch {

}
```
<br/>

액티비티 혹은 프래그먼트에서 일반적인 코루틴을 만들어 사용을 하는 경우 <code style="color: #eb5657;">LifecycleOwner</code> 가 `Destroyed` 될 때 실행 중인 코루틴을 취소하기 위해서는 <code style="color: #eb5657;">CoroutineContext.cancel()</code> 을 호출해야 한다.
<br/>

하지만 <code style="color: #eb5657;">LifecycleScope</code> 에서 실행되는 코루틴은 생명주기에 맞춰 안전하게 종료하며 하위 코루틴의 작업들을 모두 취소한다.
<br/>

```kotlin
internal class LifecycleCoroutineScopeImpl(
    override val lifecycle: Lifecycle,
    override val coroutineContext: CoroutineContext
) : LifecycleCoroutineScope(), LifecycleEventObserver {
    init {
        // in case we are initialized on a non-main thread, make a best effort check before
        // we return the scope. This is not sync but if developer is launching on a non-main
        // dispatcher, they cannot be 100% sure anyways.
        if (lifecycle.currentState == Lifecycle.State.DESTROYED) {
            coroutineContext.cancel()
        }
    }

    fun register() {
        launch(Dispatchers.Main.immediate) {
            if (lifecycle.currentState >= Lifecycle.State.INITIALIZED) {
                lifecycle.addObserver(this@LifecycleCoroutineScopeImpl)
            } else {
                coroutineContext.cancel()
            }
        }
    }

    override fun onStateChanged(source: LifecycleOwner, event: Lifecycle.Event) {
        if (lifecycle.currentState <= Lifecycle.State.DESTROYED) {
            lifecycle.removeObserver(this)
            coroutineContext.cancel()
        }
    }
}
```
<br>
<br>



## ViewModelScope
***
안드로이드 생명주기를 인식하는 코루틴 스코프로,, `ViewModel` 에서 <code style="color: #eb5657;">onCleared()</code> 호출 시 모든 코루틴이 취소된다.
<br>

```kotlin
class MainViewModel: ViewModel() {
    init {
        viewModelScope.launch {

        }
    }
}
```
<br>
<br>



## launchWhen···()
***
생명주기의 상태에 맞춰 코루틴을 실행, 중단, 재개하고 싶은 경우에는 <code style="color: #eb5657;">launchWhenCreated</code>, <code style="color: #eb5657;">launchWhenStarted</code>, <code style="color: #eb5657;">launchWhenResumed</code> 함수를 사용하면 된다.
<br/>

`When` 이후 접미어에 해당하는 생명주기에 맞춰 실행이 되고, 생명주기의 상태가 맞지 않는다면 정지가 되고, 다시 생명주기 상태가 충족되면 재개한다.
<br/>

```kotlin
lifecycleScope.launchWhenStarted {
            
}
```
<br/>
<br/>



## repeatOnLifeCycle
***
생명주기의 상태에 맞춰 코루틴을 시작, 취소, 재시작하고 싶은 경우에는 <code style="color: #eb5657;">LifecycleOwner</code> 의 확장 함수인 <code style="color: #eb5657;">repeatOnLifeCycle</code> 을 사용하면 된다.
<br/>

<code style="color: #eb5657;">Flow</code> 를 <code style="color: #eb5657;">launchWhen···()</code> 내에서 사용하게 되면 <code style="color: #eb5657;">onDestroy()</code> 에서 <code style="color: #eb5657;">collect</code> 가 중단되기 때문에 백그라운드 상태로 들어가게 되면 생명주기의 상태가 <code style="color: #eb5657;">onStop()</code> 이 되기 때문에 <code style="color: #eb5657;">collect</code> 가 중단되지 않는다.
<br/>

하지만 <code style="color: #eb5657;">repeatOnLifeCycle</code> 을 사용하면 포그라운드 상태일 때만 한정지어 특정 생명주기가 트리거 되었을 때 코루틴 <code style="color: #eb5657;">Job</code> 을 생성한다. 
<br/>

백그라운드 상태로 들어가게 되면 <code style="color: #eb5657;">onStop()</code> 에서 <code style="color: #eb5657;">Job</code> 이 취소되고, 다시 생명주기 상태가 충족되면 처음부터 다시 작업을 시작하기 때문에 백그라운드 상태에서 <code style="color: #eb5657;">collect</code> 를 중단할 수 있게 된다.
<br/>

```kotlin
lifeCycleScope.launch {
    repeatOnLifecycle(Lifecycle.State.STARTED) {

    }
}
```