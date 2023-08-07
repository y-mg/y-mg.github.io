---
layout: post
title: "Application"
date: 2018-01-19T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "액티비티, 서비스와 같은 기본 구성 요소를 포함하는 안드로이드 앱의 기본 클래스"
thumbnail: "/assets/images/android/banner/basic.png"
image: "/assets/images/android/banner/basic.png"
comments: false
---

## Application / Context
***
### Application
안드로이드의 컴포넌트에서 공유할 수 있는 전역 클래스로, 애플리케이션의 프로세스가 생성될 때 가장 먼저 인스턴스화 된다.

### Context
애플리케이션의 전역 정보에 접근하기 위해 필요한 추상 클래스이다.
<br/>
<br/>
<br/>



## Android Component
***
안드로이드는 <code style="color: #eb5657;">Activity</code>, <code style="color: #eb5657;">Service</code>, <code style="color: #eb5657;">BroadcastReceiver</code>, <code style="color: #eb5657;">ContentProvider</code> 4개의 컴포넌트로 구성되며 각 컴포넌트는 하나의 독립된 형태로 존재하며 정해진 역할을 수행한다.
<br/>

각 컴포넌트 간에는 <code style="color: #eb5657;">Intent</code> 라는 일종의 메시지 객체를 사용하여 상호 통신을 한다.
<br/>
<br/>

### Activity
사용자에게 보여지는 `UI` 를 구성하는 컴포넌트로 사용자와 직접적으로 상호작용하는 컴포넌트이고 모든 안드로이드 앱은 최소 1개 이상의 액티비티를 가지고 있어야 한다.
<br/>

그리고 각 컴포넌트끼리는 인텐트(<code style="color: #eb5657;">Intent</code>)를 통해 상호작용하기 때문에, 인텐트를 통해 다른 액티비티를 호출할 수 있다.
<br/>
<br/>

### Service
백그라운드에서 실행되는 컴포넌트이다. 
<br/>

백그라운드 스레드에서 동작한다고 생각하지만 기본적으로 메인 스레드에서 동작하기 때문에 네트워크 통신과 같은 작업을 위해서는 서비스 내에서 별도의 스레드를 생성하여 작업을 처리하여야 한다.
<br/>
<br/>

### BroadcastReceiver
안드로이드 `OS` 로부터 발생하는 각종 이벤트와 정보를 받아와 핸들링하는 컴포넌트이다.
<br/>

상호작용할 애플리케이션에 <code style="color: #eb5657;">BroadcastReceiver</code> 를 등록하고 <code style="color: #eb5657;">sendBroadcast()</code> 를 이용하여 <code style="color: #eb5657;">Intent</code> 를 주고 받는 방식으로 동작한다.
<br/>
<br/>

### ContentProvider
애플리케이션 간에 데이터 공유를 위한 인터페이스를 제공하는 컴포넌트이다. 
<br/>

`Content` `Uri` 를 설정한 이후 <code style="color: #eb5657;">query()</code>, <code style="color: #eb5657;">insert()</code>, <code style="color: #eb5657;">update()</code>, <code style="color: #eb5657;">delete()</code> 를 구현한 뒤 <code style="color: #eb5657;">Intent</code> 를 교환하여 애플리케이션간 데이터를 교환할 수 있다.