---
layout: post
title: "Compose Stability"
date: 2023-09-28T00:00:00Z
authors: ["y-mg"]
categories: [Android, Jetpack]
description: "Unstable / Stable / Immutable"
thumbnail: "/assets/images/android/banner/jetpack.png"
image: "/assets/images/android/banner/jetpack.png"
comments: false
---

## Stability
***
스마트 리컴포지션은 성능 향상을 위해서 변경된 부분만 다시 그리고 변경되지 않은 부분은 그대로 냅두는 것인데, 컴포저블 함수의 위치나 실행 순서가 같다면 리컴포지션이 필요한지 아닌지에 대한 여부를 스테빌리티 시스템으로 판단한다.
<br/>

컴포저블 함수는 파라미터를 통해서 여러 상태 값들을 보는데, 어떤 파라미터를 가지고 있는지에 따라서 컴포즈가 리컴포지션이 필요한지 아닌지 판단할 수 있게되며 컴포저블 함수에 지정을 할 수 있는 안정성 유형은 `Unstable`, `Stable`, `Immutable` 세 가지가 있다.
<br/>

성능 향상을 위한 스마트 리컴포지션을 위해서는 `Unstable` 타입을 최대한 없애고 모든 파라미터의 타입을 `Immutable` 혹은 `Stable` 타입으로 지정해줘야 한다.
<br/>
<br/>

### Unstable
안정적이지 않다는 뜻으로, 데이터 변경이 가능하고 변경 시에 컴포지션에서 추적이 불가능할 때 `Unstable` 하다고 말한다.
<br/>

데이터가 변경 가능한데 변경 시에 컴포지션에서 추적이 불가능하다는 것은 리컴포지션하는 시점에서 리컴포지션을 해도 되는지 안되는지 모르기 때문에 무조건 리컴포지션이 발생하게 된다.
<br/>
<br/>

### Stable
안정적이라는 뜻으로, 데이터 변경이 가능하지만 변경 시에 컴포지션에서 추적이 가능한 형태의 파라미터를 말한다.
<br/>

데이터가 변경 가능한데 변경 시에 컴포지션에서 추적이 가능하다는 것은 리컴포지션을 스킵하거나 리컴포지션으로 이끌어가지고 화면을 변경할 수 있다.
<br/>
<br/>

### Immutable
`Immutable` 타입은 데이터가 변경이 불가능한 타입으로 데이터의 변경 가능성이 없는 안정적인 데이터로 취급되기 때문에 컴포즈에서 주시하지 않는다.
<br/>
<br/>
<br/>



## Immutable / Mutable
***
프로퍼티가 모두 <code style="color: #eb5657;">val</code> 인 데이터 클래스의 값을 바꾸기 위해서는 새로운 객체를 생성해야 하며, 그렇게 되면 객체의 메모리상에서 위치가 달라지기 때문에 해당 객체의 해시값이 달라지게 되는데 컴포즈에서는 이것을 가지고 리컴포지션을 할지에 대해 판단할 수 있게 된다.
<br/>

프로퍼티가 모두 <code style="color: #eb5657;">var</code> 인 데이터 클래스라면 프로퍼티의 값을 재지정하더라도 해당 객체의 해시값이 없어지거나 바뀌지 않기 때문에 컴포즈에서는 값이 변경되었는지 알 수 없기 때문에 `Mutable` 하다고 인지하게 되고, `Mutable` 하다는 것은 `Unstable` 하다는 뜻으로 리컴포지션을 스킵할 수 없다.
<br/>

```kotlin
fun ContactRow(
    contact: Contact,
    modifier: Modifier = Modifier
) {
    var selected by remember { mutableStateOf(false) }
    Row(modifier) {
        ContactDetails(contact)
        ToggleButton(
            selected,
            onToggled = { selected = !selected }
        )
    }
}
```
- 데이터 클래스 `Contact` 의 프로퍼티가 모두 <code style="color: #eb5657;">val</code> 로 지정되어 있다면 `Imuutable` 한 데이터 클래스가 되며, 이 데이터 클래스를 파라미터로 받고 있는 `ContractDetails()` 는 스마트 리컴포지션의 대상이 된다.
- 토글 버튼이 눌렸을 때 `selected` 의 값이 바뀌기 때문에 이 전체가 리컴포지션의 대상이 되는데 그때 `ContactDetails()` 는 리컴포지션이 되지 않고 스킵될 수 있다.
<br/>
<br/>
<br/>



## Smart Recomposition 전략
***
스마트 리컴포지션의 전략으로 `Imuutable` 타입을 지정하는 방법이 있다. 
<br/>

코틀린 1.3 버전부턴 `Imuutable` `Collection` 타입이 추가되어 <code style="color: #eb5657;">List</code> 가 될 수도 <code style="color: #eb5657;">Set</code> 이 될 수도 있는 `Imuutable` `Collection` 타입을 지정 해주면 인터페이스 자체가 `Immutable` 이기 때문에 컴파일러 단에서 `Immutable` 라는 것을 알고 `Stable` 로 처리할 수 있게 된다.
<br/>

```kotlin
@Composable
private fun test(list: ImmutableList<String>)
```
<br/>

스마트 리컴포지션의 전략으로 또 다른 방법은 어노테이션을 사용하는 것으로 <code style="color: #eb5657;">@Immutable</code>, <code style="color: #eb5657;">@Stable</code> 어노테이션을 지정할 수 있다.
<br/>

객체에 <code style="color: #eb5657;">@Stable</code> 로 어노테이션을 지정해준다는 것은 해당 객체의 해시값이 바뀌어야 컴포즈에 알려줄 수 있기 때문에 프로퍼티 내부의 값을 변경하게 되면 리컴포지션이 일어나지 않기 때문에 새로운 객체를 생성해줘야 한다.
<br/>

```kotlin
@Composable
fun TestList(
    title: Title,
    content: Content 
)


@Immutable
data class Title(
    val list: List<String>
)

@Stable
data class Content(
    val list: List<String>
)
```