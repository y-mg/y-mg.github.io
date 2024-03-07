---
layout: post
title: "View / Layout"
date: 2018-01-22T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "안드로이드 UI 구성 요소"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## View 구조
***
안드로이드의 뷰는 트리 구조로 최상위부터 최하위까지 `Top-Down` 방식으로 그려지며, 뷰가 그려지는 순서는 <code style="color: #eb5657;">onMeasure()</code>, <code style="color: #eb5657;">onLayout()</code>, <code style="color: #eb5657;">onDraw()</code> 순으로 그려진다.
<br/>

<code style="color: #eb5657;">onMeasure()</code> 는 뷰의 크기를 정하고, <code style="color: #eb5657;">onLayout()</code> 은 뷰의 위치를 정하고,  <code style="color: #eb5657;">onDraw()</code> 는 최종적으로 뷰를 <code style="color: #eb5657;">Canvas</code> 를 사용해 그린다.
<br/>
<br/>
<br/>



## Layout
***
안드로이드에서 사용되는 대표적인 레아아웃은 <code style="color: #eb5657;">ConstraintLayut</code>, <code style="color: #eb5657;">LinearLayout</code> 등이 있으며, 레이아웃이 중첩되면 성능이 저하되기 때문에 <code style="color: #eb5657;">ConstraintLayut</code> 을 사용해서 작업을 하는 것이 권장된다.
<br/>

중첩된 레이아웃은 뷰의 계층 구조 깊이를 높이게 되어 뷰를 검색하고 측정하는데 더 많은 시간이 소요된다. 
<br/>

따라서 화면을 다시 그리거나 레이아웃을 다시 계산하는데 더 많은 시간을 소비하게 되어 화면 갱신이 느려질 수 있고, 이는 사용자 경험에 부정적인 영향을 미칠 수 있다.
<br/>

또한 각 레이아웃은 메모리에 뷰를 저장하고 관리하기 때문에 중첩된 레아아웃을 사용하게 되면 더 많은 리소스를 필요로 해서 메모리 부족과 성능 저하를 유발할 수 있다.
<br/>
<br/>

### LinearLayout
수평(`Horizontal`) 혹은 수직(`Vertical`)의 방향성을 가지고 있는 레이아웃이며, 뷰의 비율(<code style="color: #eb5657;">layout_weight</code>)을 조정할 수 있다.
<br/>
<br/>

### RelativeLayout
다른 뷰를 기준으로 상대적인 위치와 방향을 조정할 수 있는 레이아웃이다.
<br/>
<br/>

### FrameLayout
다른 뷰들과 겹쳐질 수 있도록 하는 레이아웃이다.
<br/>
<br/>

### ConstraintLayout
각 뷰들에 제약 조건을 적용해서 크기와 위치를 정할 수 있는 레이아웃으로, <code style="color: #eb5657;">LinearLayout</code> 에서만 가능했던 뷰의 비율을 조정할 수 있고, <code style="color: #eb5657;">RelativeLayout</code> 에서는 불가능했던 자식 뷰들간에 상호 관계를 정의할 수 있다.