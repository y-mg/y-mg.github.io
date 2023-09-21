---
layout: post
title: "Application"
date: 2018-01-17T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "안드로이드 컴포넌트에서 공유하는 전역 클래스"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## Application
***
안드로이드의 컴포넌트에서 공유할 수 있는 전역 클래스로, 애플리케이션의 프로세스가 생성될 때 가장 먼저 인스턴스화 된다.
<br/>
<br/>
<br/>



## Context
***
애플리케이션의 전역 정보에 접근하기 위해 필요한 추상 클래스로, 애플리케이션의 상태와 환경을 관리하는데 도움을 준다.
<br/>

<code style="color: #eb5657;">Context</code> 를 통해 애플리케이션 내의 리소스(예: 레이아웃, 문자열, 그림)에 접근할 수 있으며, 위치 서비스 등의 안드로이드 시스템 서비스를 호출할 때도 <code style="color: #eb5657;">Context</code> 를 사용한다.
<br/>

또한 앱의 패키지 이름, `Locale` 등 현재 애플리케이션의 환경 정보를 얻을 때도 <code style="color: #eb5657;">Context</code> 가 필요하며, 액티비티나 프래그먼트에서는 Context 를 사용해서 뷰나 다른 컴포넌트를 초기화하거나 조작할 수 있다.