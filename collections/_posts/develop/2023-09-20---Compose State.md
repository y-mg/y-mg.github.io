---
layout: post
title: "Compose State"
date: 2023-09-20T00:00:00Z
authors: ["y-mg"]
categories: [Android, Jetpack]
description: "객체의 어떠한 값 또는 데이터"
thumbnail: "/assets/images/android/banner/jetpack.png"
image: "/assets/images/android/banner/jetpack.png"
comments: false
---

## State
***
안드로이드에서 앱은 사용자에게 상태를 표시하는데 "네트워크를 연결할 수 없을 때의 메시지 표시", "사용자가 클릭하면 발생하는 애니메이션" 등을 말하며 상태는 컴포즈에서 가장 중요한 개념이다.
<br/>
<br/>

### remember
컴포저블은 중단되거나, 새로 그려지거나, 여러 스레드에서 여러 컴포저블을 계산하고 합쳐서 하나의 `UI` 를 만든다던지 등 언제든지 상태가 저장되지 않고 지워질 수 있는데 <code style="color: #eb5657;">remember</code>  를 사용하면 상태를 기억했다가 나중에 다시 사용할 수 있다.
<br/>

<code style="color: #eb5657;">remember</code> 를 사용해서 메모리에 단일 객체를 저장할 수 있으며 <code style="color: #eb5657;">remember</code> 에 의해 컴포지션에 저장되고 저장된 값은 리컴포지션 중에 반환되어 상태가 업데이트 된다.
<br/>

```kotlin
// 해당 코드를 실행하면 텍스트를 입력해도 아무런 반응이 없다.
@Composable
fun Input1() {
    Column(modifier = Modifier.padding(10.dp)) {
        Text(text = "Hello")
            OutlinedTextField(
            value = "",
            onValueChange = { },
            label = { Text("Name") }
        )
    }
}

// 해당 코드를 실행하면 텍스트를 입력하면 동작한다.
@Composable
fun Input2() {
    Column(modifier = Modifier.padding(10.dp)) {
        val name by remember { mutableStateOf("") }
    
        Text(text = "Hello")
            OutlinedTextField(
            value = name,
            onValueChange = { name = it },
            label = { Text("Name") }
        )
    }
}
```
<br/>

### mutableStateOf
컴포즈에서는 다시 그려지는 과정이 반복되는데, `UI` 가 갱신되기 위해서는 리컴포지션이 발생해야하며 리컴포지션이 발생하기 위해서는 상태가 바뀌어야 하는데, 여기서 상태는 코드로는 <code style="color: #eb5657;">mutableStateOf</code> 를 말한다.
<br/>

```kotlin
interface MutableState<T> : State<T> {
   override var value: T
}
```
- <code style="color: #eb5657;">mutableStateOf</code> 는 컴포즈에서 `Observable` 타입인 <code style="color: #eb5657;">MutableState</code> 를 생성하는데 `value` 가 변경되면 그 값을 읽는 컴포저블 함수의 리컴포지션이 발생하게 된다.
<br/>
<br/>

```kotlin
val checked1 = remember { mutableStateOf(false) }
val checked2 by remember { mutableStateOf(false) }
val (checked3, setChecked3) = remember { mutableStateOf(false) }
```
- <code style="color: #eb5657;">by</code> 키워드를 통해 위임된 속성(delegated properties)을 사용하면 `value` 프로퍼티 자체인 것처럼 사용할 수 있는데 예를 들면, `checked` 가 `checked.value` 프로퍼티 인것 처럼 사용이 가능하다.
- 비구조화(`destruction`)를 통해 상태를 받아와 처리하는 것도 가능한데, <code style="color: #eb5657;">mutableStateOf</code> 의 반환형인 <code style="color: #eb5657;">MutableState</code> 의 `getter` 인 `component1()` 과 `setter` 인 `component2()` 를 받아오는 것을 의미한다.
<br/>
<br/>

### rememberSaveable
<code style="color: #eb5657;">remember</code> 는 리컴포지션이 발생할 경우에 상태를 유지할 수 있도록 도움을 주지만 화면 회전과 같은 `Configuration` 이 변경되는 경우에는 유지가 되지 않아 <code style="color: #eb5657;">rememberSaveable</code> 을 사용해야 한다.
<br/>

<code style="color: #eb5657;">rememberSaveable</code> 는 <code style="color: #eb5657;">Bundle</code> 에 저장할 수 있는 모든 값을 자동으로 저장하는데, 저장 공간에 한계가 있기 때문에 모든 값에 <code style="color: #eb5657;">rememberSaveable</code> 를 적용하는 것은 권장되지 않는다.
<br/>

```kotlin
val checked by rememberSaveable { mutableStateOf(false) }
```
<br/>
<br/>



## State Hosting
***
`State` `Hosting` 은 컴포저블의 상태를 다른 컴포저블이나 호출하는 함수로 옮기는 패턴을 말하며, 이를 통해 컴포저블을 상태를 가지지 않는(`Stateless`) 형태로 만들 수 있게 된다.
<br/>

`UI` 부분은 상태가 없게 만들고, 상태가 변경되는 코드를 따로 관리하는 것이 권장되는 방법이다.
<br/>

```kotlin
@Composable
fun PyeongToSquareMeter() {
    var pyeong by rememberSaveable {
        mutableStateOf("23")
    }
    var squaremeter by rememberSaveable {
        mutableStateOf((23 * 3.306).toString())
    }

    PyeongToSquareMeterStateless(
        pyeong = pyeong,
        squaremeter = squaremeter,
        onPyengChange = {
            if (it.isBlank()) {
                pyeong = ""
                squaremeter = ""
                return@PyeongToSquareMeterStateless
            }
            val numericValue = it.toFloatOrNull() ?: return@PyeongToSquareMeterStateless
            pyeong = it
            squaremeter = (numericValue * 3.306).toString()
        }
    )
}

@Composable
fun PyeongToSquareMeterStateless(
    pyeong: String,
    squaremeter: String,
    onPyengChange: (String) -> Unit
) {
    Column(modifier = Modifier.padding(16.dp)) {
        OutlinedTextField(
            value = pyeong,
            onValueChange = onPyengChange,
            label = {
                Text("평")
            }
        )
        OutlinedTextField(
            value = squaremeter,
            onValueChange = {},
            label = {
                Text("제곱미터")
            }
        )
    }
}
```
<br/>
<br/>



## Compose Observable Type
***
컴포즈에서 상태 관리를 위해 <code style="color: #eb5657;">MutableState&lt;T&gt;</code> 를 사용할 필요 없이 기존의 <code style="color: #eb5657;">Livedata</code>, <code style="color: #eb5657;">Flow</code> 등을 사용할 수 있다.
<br/>

<code style="color: #eb5657;">Livedata</code> 와 같은 다른 <code style="color: #eb5657;">Observable</code> 타입을 사용할 경우에 <code style="color: #eb5657;">Livedata&lt;T&gt;.observeAsState()</code> 와 같은 컴포저블 함수를 사용해서 <code style="color: #eb5657;">State&lt;T&gt;</code> 로 변환하여 상태를 읽어올 수 있으며, 그래야 상태가 변할 때 자동으로 리컴포지션이 발생하게 된다.
<br/>

<code style="color: #eb5657;">observeAsState()</code> 와 같은 <code style="color: #eb5657;">State&lt;T&gt;</code> 로 변환해주는 컴포저블 함수는 컴포지션에 있는 동안에만 해당 데이터 홀더를 관찰한다.