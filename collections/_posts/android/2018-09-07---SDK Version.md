---
layout: post
title: "SDK Version"
date: 2018-09-07T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "minSdkVersion / compileSdkVersion / targetSdkVersion"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## Minimum SDK Version
***
앱이 실행되기 위해 필요한 최소의 안드로이드 `API` 레벨을 나타낸다. 
<br/>

<code style="color: #eb5657;">minSdkVersion</code> 은 설정된 값 이상의 안드로이드 버전에서 앱이 실행될 수 있음을 의미하기 때문에 이 값보다 낮은 버전의 안드로이드에서는 앱이 작동하지 않는다. 
<br/>

예를 들어, <code style="color: #eb5657;">minSdkVersion</code> 을 21로 설정하면 안드로이드 5.0(롤리팝) 이상의 기기에서만 실행됨을 의미한다.
<br/>
<br/>
<br/>


## Compile SDK Version
***
앱이 빌드되는 동안 사용되는 안드로이드 `API` 레벨을 나타낸다. 
<br/>

<code style="color: #eb5657;">compileSdkVersion</code> 은 빌드 시 앱이 사용하는 안드로이드 프레임워크의 버전을 결정하며, 런타임에 영향을 주지는 않는다.
<br/>

따라서 <code style="color: #eb5657;">compileSdkVersion</code> 을 최신으로 유지한다면 새로운 `API` 를 사용할 수 있고, 사용되지 않는 `Deprecated` 된 `API` 를 피할 수 있게 도와준다.
<br/>
<br/>
<br/>


## Target SDK Version
***
앱이 목표로 하는 안드로이드 `API` 의 버전을 나타낸다. 
<br/>

<code style="color: #eb5657;">targetSdkVersion</code> 은 앱이 목표로 하는 `API` 레벨에 따라 시스템에서의 동작이 조장된다.
<br/>

새로운 안드로이드 버전이 출시될 때 <code style="color: #eb5657;">targetSdkVersion</code> 값을 업데이트하여 앱을 최신 기능과 동작에 맞게 유지할 수 있다.