---
layout: post
title: "Compose Lifecycle"
date: 2023-09-20T00:00:00Z
authors: ["y-mg"]
categories: [Android, Jetpack]
description: "Composition / Recomposition"
thumbnail: "/assets/images/android/banner/jetpack.png"
image: "/assets/images/android/banner/jetpack.png"
comments: false
---

## Composition
***
컴포저블은 컴포즈를 구성하는 함수의 이름으로, 선언형 `UI` 를 구성하기 위한 기본 단위이자 최소 단위이며, 컴포저블 함수에서 사용되는 어노테이션 이름이기도 하다.
<br/>

컴포지션은 컴포저블 함수가 화면에 그려지기 위한 전체의 과정을 전체적으로 말하기도 하고, 제일 처음 1회 그려지는 과정을 말하기도 한다.
<br/>

컴포즈는 컴포저블 함수를 실행해서 컴포지션을 만드는데, 컴포지션에 입력되는 상태 값이 바귈 때 컴포지션을 수정하여 화면에 표시되는 내용을 바꿀 필요가 있는데 이때 화면을 고치는 동작을 리컴포지션이라고 한다.
<br/>

리컴포지션 단계에서 컴포즈는 이전에 계산한 값을 재사용하여 `UI` 전체를 다시 그리지 않고도 `UI` 일부를 업데이트 할 수 있는데 이룰 스마트 리컴포지션이라고 하며 상태에 영향을 받는 부분만 수정되기 때문에 이 부분을 최적화한다면 더욱 빠른 성능을 얻을 수 있다.
<bt/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/composition.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/composition.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Recomposition
***
상태를 <code style="color: #eb5657;">State&lt;T&gt;</code> 라고 한다면 <code style="color: #eb5657;">State&lt;T&gt;</code> 가 변경되면 리컴포지션이 트리거된다.
<br/>

리컴포지션이 트리거 되기 위해서는 <code style="color: #eb5657;">State&lt;T&gt;</code> 를 읽는 컴포저블은 무조건 변경되어야 하고 해당 컴포저블에서 호출되는 다른 컴포저블도 리컴포지션의 대상이 되는데, 대상이 되는 컴포저블이 입력이 안정적이고 변경되지 않았으면 리컴포지션이 스킵된다.
<br/>

리컴포지션이 스킵되는 조건은 두 인스턴스의 <code style="color: #eb5657;">equals</code> 결과가 영원히 같거나, 상태 타입 내부 프로퍼티가 변경되었을 때 컴포지션에 이를 알리는 경우, 상태 타입 내부 프로퍼티가 안정적인 경우 리컴포지션은 스킵될 수 있다.
<br/>

예를 들어 <code style="color: #eb5657;">MutableState</code> 는 안정적인데, <code style="color: #eb5657;">State</code> 의 내부 프로퍼티 <code style="color: #eb5657;">value</code> 를 통해 프로퍼티의 값 변경이 알려지기 때문이다.
<br/>

상태 타입 내부 프로퍼티가 안정적인 경우는 기본 자료형들, 문자열, 람다를 포함한 모든 함수 타입들인 경우이고, 안정적이라고 추론할 수 없는 경우에는 <code style="color: #eb5657;">@Stable</code> 을 표기하여 `Compose` 컴파일러가 안정적이라고 인지할 수 있도록 해야한다.
<br/>

```kotlin
// Marking the type as statble to favor skipping and smart recompositions.
@Stable
interface UiState<T: Result<T>> {
    val value: T?
    val exception: Throwable?

    val hasError: Boolean
        get() = exception != null
}
```
<br/>
<br/>
<br>



## 같은 Compostion 여러번 호출
***
컴포즈는 기본적으로 컴포지션이 여러번 호출되면 여러 인스턴스가 배치되며, 다른 인스턴스는 각자 다른 생명주기를 가지고 있다. 
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/composition-instance.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/composition-instance.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## 상태에 따라 컴포저블 호출이 달라지는 경우
***
컴포즈는 코드 상에서 어떤 위치에 있냐에 따라 인스턴스를 구분하며, 어떤 컴포저블이 추가 혹은 제거되는지 알고 있기 때문에 리컴포지션이 일어났을 때 모든 것을 전부 갱신하지 않고 입력값이 변경된 컴포저블 일부만 갱신한다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/composition-state.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/composition-state.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Smart Recompostion
***
예를 들어 2개의 컴포저블이 배치되어 있는 상태에서 가장 아래에 하나의 컴포저블이 추가되는 경우에는 기존에 있던 2개의 컴포저블은 재사용되고 아래에 하나의 컴포저블이 추가된다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/composition-good.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/composition-good.png" style="visibility: hidden;" />
</div>
<br/>

하지만 2개의 컴포저블이 배치되어 있는 상태에서 가장 위에 하나의 컴포저블이 추가되는 경우에는 재정렬이 필요하여 기존에 있던 2개의 컴포저블이 추적되지 않기 때문에 3개의 모든 컴포저블이 모두 새로 그려지게 된다.
<br/>

또한, 목록이 불려오고 네트워크 이미지가 로드되는 동안 동안 가장 위에 하나의 컴포저블이 추가된다면 리컴포지션이 발생하여 인스턴스가 날라가기 때문에 네트워크 이미지 로드는 취소되게 되는데, 이러한 동작을 최적화하지 못한다면 큰 리소스 낭비로 이어질 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/composition-bad.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/composition-bad.png" style="visibility: hidden;" />
</div>
<br/>

위와 같은 문제를 해결하기 위해서는 `key` 를 지정하여 재사용에 도움을 주어야하며, 이를 `Smart` `Recompostion` 이라고 한다.
<br/>

키를 이용해서 각 아이템을 구분해 줄 수 있는 유니크한 값을 전달해준다면 컴포즈에서 인식을 해서 위 또는 중간에 아이템을 추가를 해도 필요 없는 리컴포지션을 피할 수 있게 된다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/composition-key.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/composition-key.png" style="visibility: hidden;" />
</div>
<br/>

일부 컴포저블에서는 `key` 가 지원되는데 대표적으로 <code style="color: #eb5657;">LazyColumn</code> 이 있다.
<br/>

```kotlin
@Composable
fun MoviesScreen(movies: List<Movie>) {
    LazyColumn {
        items(movies, key = { movie -> movie.id }) { movie ->
            MovieOverview(movie)
        }
    }
}
```