---
layout: post
title: "Component"
date: 2018-01-18T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "안드로이드를 구성하는 기본 요소"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## Android Component
***
안드로이드는 <code style="color: #eb5657;">Activity</code>, <code style="color: #eb5657;">Service</code>, <code style="color: #eb5657;">BroadcastReceiver</code>, <code style="color: #eb5657;">ContentProvider</code> 4개의 컴포넌트로 구성되며m 각 컴포넌트는 하나의 독립된 형태로 존재하며 정해진 역할을 수행한다.
<br/>

각 컴포넌트 간에는 인텐트(<code style="color: #eb5657;">Intent</code>)라는 일종의 메시지 객체를 사용하여 상호 통신을 한다.
<br/>
<br/>
<br/>



## Activity
***
사용자에게 보여지는 `UI` 를 구성하는 컴포넌트로, 사용자와 직접적으로 상호작용하는 컴포넌트이고, 모든 안드로이드 앱은 최소 1개 이상의 액티비티를 가지고 있어야 한다.
<br/>

각 컴포넌트는 인텐트를 통해 상호작용하기 때문에 인텐트를 통해 다른 액티비티를 호출할 수 있다.
<br/>
<br/>
<br/>



## Service
***
백그라운드에서 실행되는 컴포넌트로, 다른 컴포넌트가 서비스를 실행할 수 있고 다른 애플리케이션으로 전환되더라도 계속 실행된다.
<br/>

기본적으로 메인 스레드에서 동작하기 때문에 시간이 소요되는 작업을 하기 위해서는 서비스 내에서 별도의 스레드를 생성해서 작업을 처리해야 한다.
<br/>

서비스는 포그라운드, 백그라운드, 바인드 서비스가 있다.
<br/>

포그라운드 서비스는 사용자에게 알림을 표시하며 작업을 수행하는 서비스이고, 백그라운드 서비스는 사용자에게 보이지 않는 작업을 수행하는 서비스이고, 바인드 서비스는 서비스와 서비스를 호출하는 다른 컴포넌트가 상호작용하는 서비스이다.
<br/>
<br/>
<br/>



## BroadcastReceiver
***
안드로이드 `OS` 로부터 발생하는 각종 이벤트와 정보를 받아와 핸들링하는 컴포넌트이다.
<br/>

상호작용할 애플리케이션에 <code style="color: #eb5657;">BroadcastReceiver</code> 를 등록하고 <code style="color: #eb5657;">sendBroadcast()</code> 를 이용해서 인텐트를 주고 받는 방식으로 동작한다.
<br/>
<br/>
<br/>



## ContentProvider
***
애플리케이션 간에 데이터 공유를 위한 기능을 제공하는 컴포넌트로, 연락처들의 정보를 가져올 수 있는 <code style="color: #eb5657;">ContactsProvider</code> 등이 있다.
<br/>

`Content` `Uri` 를 설정한 이후 <code style="color: #eb5657;">query()</code>, <code style="color: #eb5657;">insert()</code>, <code style="color: #eb5657;">update()</code>, <code style="color: #eb5657;">delete()</code> 를 구현한 뒤 인텐트를 통해 애플리케이션 간에 데이터를 교환할 수 있다.