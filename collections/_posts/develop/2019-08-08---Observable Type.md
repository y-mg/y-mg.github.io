---
layout: post
title: "Observable Type"
date: 2019-08-08T00:00:00Z
authors: ["y-mg"]
categories: [Kotlin, ReactiveX]
description: "Cold Observable / Hot Observable"
thumbnail: "/assets/images/kotlin/banner/reactivex.png"
image: "/assets/images/kotlin/banner/reactivex.png"
comments: false
---

## Cold Observable
***
구독하는 시점에 데이터를 발행하는 <code style="color: #eb5657;">Observable</code> 로 원하는 시점에 데이터를 요청하고 처음부터 끝까지 모든 데이터가 순서대로 발행된어 결과를 받을 수 있다.
<br/>

대표적인 예시로는 API 요청, DB 쿼리 등이 있다.
<br/>
<br/>



## Hot Observable
***
구독 시점과 상관 없이 데이터를 발행하는 <code style="color: #eb5657;">Observable</code> 로 구독 시점 이후부터 데이터를 받을 수 있기 때문에 구독 이전의 데이터는 받지 못한다.
<br/>

대표적인 예시로는 마우스 이벤트, 키보드 이벤트 등이 있다.