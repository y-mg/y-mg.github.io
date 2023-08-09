---
layout: post
title: "MVC / MVP / MVVM"
date: 2019-10-01T00:00:00Z
authors: ["y-mg"]
categories: [Android, Architecture]
description: "디자인 패턴"
thumbnail: "/assets/images/android/banner/architecture.png"
image: "/assets/images/android/banner/architecture.png"
comments: false
---

## MVC
***
`MVC` 디자인 패턴은 `Model`, `View`, `Controller` 의 줄임말이다.
<br/>

`Controller` 는 `View` 로부터 이벤트가 발생하면 로직에 맞게 `Model` 을 변경하거나 `Model` 에서 데이터를 가져와 `View` 의 상태를 업데이트 하는 역할을 하며, 안드로이드에서 액티비티 혹은 프래그먼트는 `View` 의 역할을 하는 동시에 컨트롤러 역할도 한다.
<br/>

구조가 직관적이고 단순하지만 그만큼 코드가 편중되는 경향이 있어 앱의 규모가 커질수록 코드를 유지보수 하기 힘들다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/pattern-mvc.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/pattern-mvc.png" style="visibility: hidden;" />
</div>
<br/>
<br/>



## MVP
***
`MVP` 디자인 패턴은 `Model`, `View`, `Presenter` 의 줄임말이다.
<br/>

`Presenter` 는 `View` 와 `Model` 을 연결해주는 역할로 `View` 와 `Presenter` 는 1:1 관계를 가지며 `Presenter` 를 도입함으로써 `UI` 로직과 비즈니스 로직을 분리할 수 있게 된다.
<br/>

`UI` 로직과 비즈니스 로직을 분리하여 결합도를 낮추게 한 장점이 있지만 `View` 에서 `Presenter` 를 직접 생성하기에 의존성이 높고 1:1 관계를 가지다보니 `View` 가 많아질수록 `Presenter` 도 많아지고 기능이 추가될 때 마다 `Presenter` 에 코드가 많아짐에 따라 유지보수하기 어려워진다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/pattern-mvp.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/pattern-mvp.png" style="visibility: hidden;" />
</div>
<br/>
<br/>



## MVVM
***
`MVVM` 디자인 패턴은 `Model`, `View`, `ViewModel` 의 줄임말이다.
<br/>

`MVP` 디자인 패턴의 `Presenter` 는 `View` 를 참조할 수 있지만 `MVVM` 디자인 패턴의 `ViewModel` 은 `View` 에 대한 참조가 불가능하다.
<br/>

따라서 `ViewModel` 은 `View` 가 필요로 하는 데이터를 `Model` 에서 가져와 <code style="color: #eb5657;">Observable</code> 타입으로 관리하여 `View` 들이 `ViewModel` 의 데이터를 구독하여 화면을 갱신할 수 있도록 한다.
<br/>

화면 회전과 같이 액티비티가 재성생이 되는 경우 액티비티는 종료되고 다시 생성하는 과정을 거치면서 데이터가 유지되지 않게 된다.
<br/>

액티비티 재성성시 데이터를 유지하기 위해서는 <code style="color: #eb5657;">onSaveInstanceState()</code> 에서 번들(<code style="color: #eb5657;">Bundle</code>)에 데이터를 저장하고 <code style="color: #eb5657;">onCreate()</code> 또는 <code style="color: #eb5657;">onRestoreInstanceState()</code> 에서 저장한 데이터를 전달 받을 수 있으나 번들은 저장하는 데이터의 자료형과 크기에 제한이 있다는 단점이 있다.
<br/>

`ViewModel` 을 사용하면 액티비티가 재성성이 되는 경우에 소멸되지 않고 액티비티 내에서 <code style="color: #eb5657;">finish()</code> 가 호출되는 경우에 <code style="color: #eb5657;">onCleared()</code> 가 호출되며 소멸된다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/pattern-mvvm.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/pattern-mvvm.png" style="visibility: hidden;" />
</div>