---
layout: post
title: "Compose Side Effect"
date: 2024-02-22T00:00:00Z
authors: ["y-mg"]
categories: [Android, Jetpack]
description: "컴포즈에서 UI 의 상태를 변경하는 작업"
thumbnail: "/assets/images/android/banner/jetpack.png"
image: "/assets/images/android/banner/jetpack.png"
comments: false
---

## Side Effect
***
안드로이드 컴포즈에서 `effect` 란 용어는 주로 `Side` `Effect` 를 말하는데, 안드로이드 컴포즈에서 `UI` 의 상태를 변경하는 작업을 의미한다.
<br/>
<br/>
<br/>



## LaunchedEffect
***
컴포저블의 범위에서 중단 함수(<code style="color: #eb5657;">suspend function</code>) 실행을 할 때 사용한다.
<br/>

<code style="color: #eb5657;">LaunchedEffect</code> 는 하나의 키를 받아야 하며, 키가 바뀌면 새로운 코루틴 스코프를 만들어서 실행하며, 만약 <code style="color: #eb5657;">LaunchedEffect</code> 가 컴포지션 범위에서 빠지게 되면 코루틴이 캔슬된다.
<br/>

```kotlin
@Composable
fun MyScreen(
    state: UiState<List<Movie>>,
    scaffoldState: ScaffoldState = rememberScaffoldState()
) {
    if (state.hasError) {
        LaunchedEffect(scaffoldState.snackbarHostState) {
            scaffoldState.snackbarHostState.showSnackbar(
                message = "Error Message",
                actionLabel = "Retry Message"
            )
        }
    }

    Scaffold(scaffoldState = scaffoldState)
}
```
<br/>
<br/>



## rememberCoroutineScope
***
코루틴 스코프를 만들고 유지하기 위하여 사용하는 것으로, 컴포지션 인식 범위를 확보해서 컴포저블 외부에서 코루틴을 실행할 수 있도록 해준다.
<br/>

```kotlin
@Composable
fun MoviesScreen(
    scaffoldState: ScaffoldState = rememberScaffoldState()
) {
    val scope = rememberCoroutineScope()
    Scaffold(scaffoldState = scaffoldState) {
        Column {
            Button(
                onClick = {
                    scope.launch {
                        scaffoldState.snackbarHostState.showSnackbar("Something Happened!")
                    }
                }
            ) {
                Text("Press Me")
            }
        }
    }
}
```
- `MoviesScreen` 안에서 코루틴 스코프가 만들어진다.
<br/>
<br/>
<br/>



## rememberUpdatedState
***
값이 변경되는 경우에 그것을 이용하는 효과가 다시 시작되지 않게 되어야 하는 경우에 사용한다.
<br/>

```kotlin
@Composable
fun <T> rememberUpdatedState(newValue: T): State<T> = remember {
    mutableStateOf(newValue)
}.apply {
    value = newValue
}
```
- 값을 상태로 만들고 `remember` 한 후에 다시 <code style="color: #eb5657;">apply</code> 로 값을 대입한다.
- `remember` 상태에서는 상태가 만들어지면 리컴포지션 과정에 예전 값을 가지게 되기 때문에 값을 다시 <code style="color: #eb5657;">apply</code> 로 설정하는 것이다.
<br/>
<br/>

```kotlin
@Composable
fun LandingScreen(onTimeout: () -> Unit) {
    val currentOnTimeout by rememberUpdatedState(onTimeout)

    LaunchedEffect(true) {
        delay(SplashWaitTimeMillis)
        currentOnTimeout()
    }
}
```
- <code style="color: #eb5657;">LaunchedEffect</code> 에 키에 바로 `onTimeout` 을 설정하지 않은 이유는 리컴포지션이 되면 <code style="color: #eb5657;">delay</code> 가 계속 호출되기 때문에, <code style="color: #eb5657;">true</code> 를 설정하여 리컴포지션에서 제외하여 한번만 호출되게 한 것이다.
<br/>
<br/>
<br/>



## DisposableEffect
***
"파일을 열었다가 닫는다" 와 같은 정리가 필요한 효과를 onDispose() 에서 관리할 수 있도록 해준다.
<br/>

<code style="color: #eb5657;">DisposableEffect</code> 의 키가 바뀌면 <code style="color: #eb5657;">dispose</code> 하고 이펙트를 재시작하며, 컴포지션에서 이펙트가 나가도 <code style="color: #eb5657;">onDispose</code> 를 호출한다.
<br/>

```kotlin
@Composable
fun HomeScreen(
    lifecycleOwner: LifecycleOwner = LocalLifecycleOwner.current,
    onStart: () -> Unit,
    onStop: () -> Unit
) {
    val currentOnStart by rememberUpdatedState(onStart)
    val currentOnStop by rememberUpdatedState(onStop)

    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            if (event == Lifecycle.Event.ON_START) {
                currentOnStart()
            } else if (event == Lifecycle.Event.ON_STOP) {
                currentOnStop()
            }
        }
    }

    lifecycleOwner.lifecycle.addObserver(observer)

    onDispose {
        lifecycleOwner.lifecycle.removeObserver(observer)
    }
}
```
<br/>
<br/>



## SideEffect
***
컴포즈 상태를 비컴포즈 상태로 바꿔주며, 리컴포지션이 발생할 때 마다 호출된다.
<br/>

```kotlin
@Composable
fun rememberAnalytics(user: User): FirebaseAnalytics {
    val analytics: FirebaseAnalytics = remember {
        /* ... */
    }

    SideEffect {
        analytics.setUserProperty("userType", user.userType)
    }
    return analytics
}
```
<br/>
<br/>



## produceState
***
비컴포즈 상태를 컴포즈 상태로 바꿔주며, 코루틴을 제공한다.
<br/>

```kotlin
@Composable
fun loadNetworkImage(
    url: String,
    imageRepository: ImageRepository
): State<Result<Image>> {
    return produceState<Result<Image>>(
        initialValue = Result.Loading,
        url,
        imageRepository
    ) {
        val image = imageRepository.load(url) // suspend call
        value = if (image == null) {
            Result.Error
        } else {
            Result.Success(image)
        }
    }
}
```
- <code style="color: #eb5657;">State</code> 를 반환하는데(<code style="color: #eb5657;">State&lt;Result&lt;Image&gt;&gt;</code>), `value` 값을 통해 상태를 반환한다.
<br/>
<br/>
<br/>



## derivedStateOf
***
하나 이상의 상태 객체를 다른 상태 객체로 변환한다.
<br/>

<code style="color: #eb5657;">derivedStateOf</code> 는 상태의 값이 바뀌지 않으면 값이 유지되는 특징이 있으며 값이 바뀔 때만 <code style="color: #eb5657;">State</code> 의 `value` 가 바뀌기 때문에 불필요한 리컴포지션을 방지하는 장점이 있다.
<br/>

```kotlin
@Composable
fun TodoList(
    highPriorityKeywords: List<String> = listOf("Review", "Unblock", "Compose")
) {
    val todoTasks = remember { mutableStateListOf<String>() }

    val highPriortyTasks by remember(highPriorityKeywords) {
        derivedStateof {
            todoTasks.filter {
                it.containWord(highPriorityKeywords)
            }
        }
    }

    Box(Modifier.fillMaxSize()) {
        LazyColumn {
            items(highPriorityTasks) { /* ... */ }
            items(todoTasks) { /* ... */ }
        }
    }
}
```

<br/>
<br/>



## snapshotFlow
***
컴포즈의 상태를 <code style="color: #eb5657;">Flow</code> 로 변환한다.
<br/>

<code style="color: #eb5657;">State&lt;T&gt;</code> 를 콜드 <code style="color: #eb5657;">Flow</code> 로 변경하며, 이전에 방출한 값(`emitted` `value`)과 다를 경우에 방출(`emit`)한다. 
<br/>

```kotlin
val listState = rememberLazyListState()

LazyColumn(state = listState) {
    /* ... */
}

LaunchedEffect(listState) {
    snapshotFlow {
        listState.firstVisibleItemIndex
    }.map { index ->
        index > 0
    }
    .distinctUntilChanged()
    .filter {
        it == true
    }
    .collect {
        MyAnalyticsService.sendScrolledPastFirstItemEvent()
    }
}
```