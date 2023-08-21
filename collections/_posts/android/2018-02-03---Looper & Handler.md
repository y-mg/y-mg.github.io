---
layout: post
title: "Looper / Handler"
date: 2018-02-03T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "스레드간의 통신을 위한 장치"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## Looper
***
하나의 스레드는 오직 하나의 <code style="color: #eb5657;">Looper</code> 를 가지며, 안드로이드에선 기본적으로 `MainActivity` 가 실행됨과 동시에 자동으로 메인 스레드의 <code style="color: #eb5657;">Looper</code> 가 돌기 시작한다.
<br/>

<code style="color: #eb5657;">Looper</code> 내부에는 `MessageQueue` 가 존재하는데, 여기에는 해당 스레드가 처리해야 할 동작들이 메세지(`Message`)라는 형태로 하나씩 쌓이게 되고, <code style="color: #eb5657;">Looper</code> 는 이 메세지들을 하나씩 꺼내어 이를 적절한 <code style="color: #eb5657;">Handler</code> 로 전달하는 역할을 한다. 
<br/>

메세지는 하나의 작은 작업 단위로, 메세지 객체 혹은 <code style="color: #eb5657;">Runnable</code> 객체로 이루어져 있다.
<br/>

<code style="color: #eb5657;">Looper</code> 는 어떤 <code style="color: #eb5657;">Handler</code> 에 메세지를 전달해야 하는지에 대한 참조를 가지고 있으며, 만약 `MessageQueue` 가 비어있다면 아무런 동작을 수행하지 않는다.
<br/>
<br/>
<br/>



## Handler
***
<code style="color: #eb5657;">Handler</code> 는 특정 메세지를 <code style="color: #eb5657;">Looper</code> 의 `MessageQueue` 에 넣거나, <code style="color: #eb5657;">Looper</code> 의 `MessageQueue` 에서 특정 메세지를 꺼내어 전달하면 이를 처리하는 기능을 수행하는 중간 다리 역할을 한다.
<br/>

<code style="color: #eb5657;">Looper</code> 로 메세지를 전달하는 경우에는 <code style="color: #eb5657;">sendMessage()</code> 메소드를 통해 `MessageQueue` 에 메세지 객체를 담을 수 있고, <code style="color: #eb5657;">post</code> 로 시작하는 메소드들을 통해 <code style="color: #eb5657;">Runnable</code> 객체를 담을 수 있다.
<br/>

<code style="color: #eb5657;">Looper</code> 로 메세지를 전달받는 경우에는 메세지에 <code style="color: #eb5657;">Runnable</code> 객체가 담겨있다면 <code style="color: #eb5657;">Runnable</code> 의 <code style="color: #eb5657;">run()</code> 메소드를 호출해서 작업을 실행할 수 있고, 메세지 객체가 담겨있다면 메세지 내부의 <code style="color: #eb5657;">Handler</code> 가 갖고 있는 <code style="color: #eb5657;">handleMessage()</code> 메소드를 호출해서 해당 <code style="color: #eb5657;">Handler</code> 가 메세지를 전달받을 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/looper-and-handler.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/looper-and-handler.png" style="visibility: hidden;" />
</div>








