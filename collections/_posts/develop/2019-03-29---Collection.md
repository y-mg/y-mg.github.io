---
layout: post
title: "Collection"
date: 2019-03-29T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin]
description: "Kotlin 의 컬렉션 프레임워크는 읽기(read-only,immutable)만 가능한 컬렉션과 읽기/쓰기(mutable, read/write)가 가능한 컬렉션으로 구분됨"
thumbnail: "/assets/images/kotlin/banner/basic.png"
image: "/assets/images/kotlin/banner/basic.png"
comments: false
---

## Collection / MutableCollection
***
<code style="color: #eb5657;">kotlin.collection.Collection</code> 인터페이스이다.
<br/>

```kotlin
// 읽기만 지원
interface Collection<out E> : Iterable<E>

// 읽기/쓰기 지원
interface MutableCollection<E> : Collection<E>, MutableIterable<E>
```
<br>
<br/>



## List / MutableList
***
<code style="color: #eb5657;">kotlin.collection.Collection</code> 인터페이스를 상속 받고 있다.
<br/>

```kotlin
// 읽기만 지원
interface List<out E> : Collection<E>

// 읽기/쓰기 지원
interface MutableList<E> : List<E>, MutableCollection<E>
```
<br/>
<br/>



## Map & MutableMap
***
`Key` 와 `Value` 를 쌍으로 데이터를 저장한다.
<br/>

```kotlin
// 읽기만 지원
interface Map<K, out V>

// 요소의 추가 및 삭제 처리를 제공
interface MutableMap<K, V> : Map<K, V>
```

```kotlin
fun main() {
    var map = mapOf("RED" to "#FF0000", "GREEN" to "#008000", "BLUE" to "#0000FF")

    println(map["Red"])

    for ((key, value) in map) {
        println("$key = $value")
    }
}


// #FF0000
// RED = #FF0000
// GREEN = #008000
// BLUE = #0000FF
```
<br/>
<br/>



## Set & MutableSet
***
<code style="color: #eb5657;">List</code> 와 비슷하지만 중복값을 허용하지 않으며 순서가 없다.

```kotlin
// 읽기만 지원
interface Set<out E> : Collection<E>

// 요소의 추가 및 삭제 처리를 제공
interface MutableSet<E> : Set<E>, MutableCollection<E>
```

```kotlin
fun main() {
    var color = mutableSetOf("RED", "GREEN", "BLUE")

    color.add("RED")

    println(color)
}


// [RED, GREEN, BLUE]
```
<br/>
<br/>



## Array / List
***
### Array
배열(`Array`)은 정적인 데이터 타입으로 선언하는 순간 크기가 정해지며 이후 크기를 수정할 수 없으며 배열의 값(value)에 인덱스(Index)를 이용해 접근하고 수정할 수 있다.
<br/>

```kotlin
val arr1 = arrayOf<Int>(1, 2, 3, 4, 5)   // [1, 2, 3, 4, 5]
val arr2 = arrayOfNulls<Int>(3)          // [null, null, null]
val arr3 = Array<Int>(5) { 0 }           // [0, 0, 0, 0, 0]
```
<br/>

### List
리스트(`List`)는 동적인 데이터 타입으로 순서를 가지지만 크기(size)가 정해지지 않아 크기에 관계없이 데이터 추가가 가능하다.
<br/>

기본적으로 `Immutable`(수정 불가) 타입이므로 데이터 수정을 위해서는 <code style="color: #eb5657;">MutableList</code> 선언이 필요하다.
<br/>

```kotlin
val list1: List<Int> = List(3) { i -> i }     // [0, 1, 2]
val list2 = mutableListOf<Int>(1, 2, 3, 4, 5) // [1, 2, 3, 4, 5]
```
<br/>
<br/>



## arrayListOf / linkedListOf
***
### arrayListOf
<code style="color: #eb5657;">java.util.ArrayList</code> 인스턴스를 생성한다.
- 추가, 삭제 등의 수정이 가능하다.
- 기본적으로 <code style="color: #eb5657;">Array</code> 를 사용하지만 크기를 지정하지 않고 동적으로 값을 할당하고 삭제할 수 있다.
- 각 데이터의 index 를 가지고 있고 무작위 접근이 가능하기 때문에 해당 index 의 데이터를 가져오기 용이하다.
- 예를 들어 3개의 데이터가 있을 때 맨 앞의 데이터를 삭제하였다면 나머지 뒤의 2개의 데이터를 앞으로 한칸씩 이동해야하기 때문에 데이터 추가, 삭제가 많다면 비효율적이다.
<br/>

```kotlin
val arrayList = arrayListOf(1, 2, 3)
```
<br/>

### linkedListOf
<code style="color: #eb5657;">java.util.LinkedList</code> 인스턴스를 생성한다.
- 추가, 삭제 등의 수정이 가능하다.
- 내부적으로 양방향의 연결 리스트로 구성되어 있기 때문에 참조하려는 원소에 따라 정방향 또는 역순으로 조회한다.
- 요소에 대해 순차적으로 접근하기 때문에 조회의 속도가 느라다.
- 예를 들어 3개의 데이터가 있을 때 2번째 데이터를 삭제하였다면 1번째 노드가 마지막 노드를 가리키게 하면 되기 때문에 데이터 추가, 삭제 시 가리키고 있는 주소값만 변경해주면 되어서 효율적이다.
<br/>

```kotlin
val linkedList = linkedListOf(1, 2, 3)
```
<br/>
<br/>



## Collection Methods
***
### forEach()
목록에 반복 처리를 할 수 있는 함수이다.
<br/>

```kotlin
fun main() {
    val array = arrayOf(1, 2, 3, 4, 5)

    // 람다
    array.forEach({ element ->
        print("$element, ")
    })
    println()

    // 괄호'()' 생략 가능
    array.forEach { element ->
        print("$element, ")
    }
    println()

    // it 으로 수신 객체에 접근 가능
    array.forEach {
        print("$it, ")
    }
}


// 1, 2, 3
// 1, 2, 3
// 1, 2, 3
```
<br/>

### filter()
목록에 조건을 걸어 그 조건에 맞는 새로운 목록을 반환하는 함수이다.
<br/>

```kotlin
fun main() {
    val array = arrayOf(1, 2, 3, 4, 5) // => 1,2,3,4,5 배열을 생성한다.

    // 조건에 충족되는 목록을 반환
    array.filter({ e -> e % 2 == 0 })
        .forEach { println(it) }

    // 괄호'()' 생략 가능
    array.filter { e -> e % 2 == 0 }
        .forEach { println(it) }

    // it 으로 수신 객체에 접근 가능
    array.filter { it % 2 == 0 }
        .forEach { println(it) }
}


// 2
// 2
// 2
```
<br/>

### map()
목록의 요소를 변경하여 새로운 목록을 반환하는 함수이다.
<br/>

```kotlin
fun main() {
    val array = arrayOf(
        Person("A", 10),
        Person("B", 20),
        Person("C", 30),
    )

    // 람다로 user 인스턴스 접근 가능
    array.map({ user -> user.name })
        .forEach { print("${it}, ") }
    println()

    // 괄호'()' 생략 가능
    array.map { user -> user.name }
        .forEach { print("${it}, ") }
    println()

    // it 으로 수신 객체에 접근 가능하며 it 은 user 인스턴스를 가리킴
    array.map { it.name }
        .forEach { print("${it}, ") }
}


data Person(
    val name: String,
    val age: Int
)


// A, B, C
// A, B, C
// A, B, C
```
<br/>

### sum()
입력한 숫자를 합산해주는 함수이다.
<br/>

```kotlin
fun main() {
    val array = arrayOf(1, 2, 3)
    println(array.sum())
}


// 6
```
<br/>

### take()
목룍의 요소를 입력한 숫자 만큼 가져오는 함수이다.
<br/>

```kotlin
fun main() {
    val array = arrayOf(1, 2, 3)

    array.take(1)
        .forEach { print(it) }
    println()

    array.take(2)
        .forEach { print(it) }
    println()

    array.take(3)
        .forEach { print(it) }
}


// 1
// 12
// 123
```
<br/>

### drop()
목록의 요소를 앞에서부터 입력한 숫자만큼 제거하고 가져오는 함수이다.
<br/>

```kotlin
fun main() {
    val array = arrayOf(1, 2, 3)

    array.drop(1)
        .forEach{print(it)}
    println()

    array.drop(2)
        .forEach{print(it)}
}


// 23
// 3
```