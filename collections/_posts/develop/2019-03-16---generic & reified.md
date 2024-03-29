---
layout: post
title: "generic & reified"
date: 2019-03-16T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin]
description: "제네릭 함수의 형태는 일반적으로 런타임에는 손실되지만, 코틀린에서 inline 과 reified 을 이용하는 것으로 런타임시에도 제네릭 함수의 형태를 참조하는 것이 가능"
thumbnail: "/assets/images/kotlin/banner/basic.png"
image: "/assets/images/kotlin/banner/basic.png"
comments: false
---

## 제네릭 타입
***
타입을 파라미터로 받는 클래스, 인터페이스, 메소드를 말한다.
<br/>

```java
public interface List<E> extends Collection<E> { /* ... */ }
```

```kotlin
interface List<E> : Collection<E> { /* ... */ }
```
<br/>
<br/>



## 제네릭 타입 소거
***
제네릭은 타입 소거(`type` `erasure`)를 사용해 구현되는데 런타임 시점에 제네릭의 인스턴스에는 타입 파라미터의 정보가 사라진다는 뜻이다.
<br/>

예를 들어 <code style="color: #eb5657;">List&lt;String&gt;</code> 객체를 만들고 그 안에 문자열을 넣어도 런타임시 이 객체를 오직 <code style="color: #eb5657;">List</code> 로 인식한다.
<br/>
<br/>
<br/>



## 제네릭 동작 및 제약
***
제네릭은 컴파일 타입에만 제약을 가하며 런타임 시점에 바운드 타입(`bound` `type`, 특정 타입으로 제한한다는 의미)이 있는 경우에는 해당 바운드 타입으로 대체하고 없는 경우에는 <code style="color: #eb5657;">Object</code> 로 대체한다.
<br/>

결국 제네릭은 런타임 시점에 타입 소거로 인해 타입 정보가 사라져서 런타임 시점에는 타입 파라미터를 검사할 수 없게 된다.
<br/>

 ```kotlin
 val list1: List<String> = listOf("a", "b")
 val list2: List<Int> = listOf(1, 2)
 ```
 - 컴파일러는 위 리스트들을 다른 타입으로 인식하지만, 런타임 시점에는 같은 타입의 객체로 인식한다.
 - <code style="color: #eb5657;">List&lt;String&gt;</code> 는 문자열, <code style="color: #eb5657;">List&lt;Int&gt;</code> 는 정수만 들어있다고 가정할 수 있는 이유는 컴파일러가 올바른 타입만 각 리스트에 넣도록 보장해주기 때문이다. 
<br/>
<br/>
<br/>



## reified 키워드
***
코틀린에서는 <span onClick="window.open('../2019-03-06--10. 인라인 함수');" style="cursor:pointer; color: #5495ff;">인라인 함수</span>의 특성을 이용하여 컴파일러는 타입 파라미터로 쓰인 구체적인 클래스를 참조하는 바이트 코드를 생성해 삽입하고 <code style="color: #eb5657;">reified</code> 를 사용한 타입 파라미터를 실체화할 수 있게 되어 기존 제네릭의 제약을 극복할 수 있게 된다.
<br/>

```kotlin
inline fun <reified T> isValue(value: Any) = value is T
```
- 함수에 <code style="color: #eb5657;">inline</code> 키워드를 붙여 인라인 함수로 만들고 타입 파라미터에 <code style="color: #eb5657;">reified</code> 키워드를 붙이게 되면 런타임 시점에 <code style="color: #eb5657;">value</code> 의 타입이 <code style="color: #eb5657;">T</code> 의 인스턴스인지 확인할 수 있게 된다.
- <code style="color: #eb5657;">reified</code> 타입 파라미터를 사용하는 모든 함수는 반드시 인라인 되어야 한다.
<br/>
<br/>