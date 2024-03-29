---
layout: post
title: "lateinit & lazy"
date: 2019-03-07T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin]
description: "지연 초기화 / 초기화 지연"
thumbnail: "/assets/images/kotlin/banner/basic.png"
image: "/assets/images/kotlin/banner/basic.png"
comments: false
---

## lateinit - 지연 초기화
***
프로퍼티를 선언할 때 초기화하는 작업을 뒤로 미루어서 값을 할당하지 않고 선언할 수 있게하는 키워드이다.
- <code style="color: #eb5657;">var</code> 프로퍼티만 사용 가능하며, 기본 자료형(`Primitive` `Type`)에는 사용할 수 없다.
- <code style="color: #eb5657;">isInitialized()</code> 를 사용하여 초기화 여부를 확인할 수 있으며, 초기화하지 않고 사용하면 에러가 발생한다.
<br/>

```kotlin
fun main() {
    val sample = Sample()

    println(sample.getText())
    
    sample.text = "new value"
    println(sample.getText())
}


class Sample{
    lateinit var text: String
    
    fun getText(): String = if (::text.isInitialized) {
        text
    } else {
        "default value"
    }
}


// default value
// new value
```
<br/>
<br/>



## lazy - 초기화 지연
***
프로퍼티를 선언할 때 초기화 코드를 정의하고 최초 호출될 때 초기화 코드가 동작하도록 하는 키워드이다.
- <code style="color: #eb5657;">val</code> 프로퍼티만 사용 가능하기에 값을 변경할 수 없다.
- <code style="color: #eb5657;">by lazy</code> 를 붙이고 람다 함수를 정의하면 되고 함수 안에 여러 개의 구문이 들어갈 수 있으며 마지막 구문의 값이 프로퍼티에 할당된다.
<br/>

```kotlin
fun main() {
    val number: Int by lazy {
        println("초기화")
        100
    }

    println(number) // 초기화 진행
    println(number) // 이미 초기화가 되었기 때문에 다시 초기화 코드를 실행하지 않음
}


// 초기화
// 100
// 100
```