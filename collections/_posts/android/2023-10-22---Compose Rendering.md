---
layout: post
title: "Compose Rendering"
date: 2023-10-22T00:00:00Z
authors: ["y-mg"]
categories: [Android, Jetpack]
description: "Composition / Layout / Drawing"
thumbnail: "/assets/images/android/banner/jetpack.png"
image: "/assets/images/android/banner/jetpack.png"
comments: false
---

## Rendering
***
컴포즈의 랜더링은 데이터를 `UI` 로 변환하는 것으로, `Composition`, `Layout`, `Drawing` 이라는 세 단계를 거쳐서 실제 `UI` 로 변환된다.
<br/>

상태가 변경될 때 마다 동작하는데, 중간에 값이 변경되는 경우에 컴포지션이 `Reader` 를 통해 다시 데이터를 읽고 다시 `Composition`, `Layout`, `Drawing` 을 하게 된다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/rendering.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/rendering.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Composition
***
`Composition` 단계에서는 화면에 무엇을 보여주게 할지 `What` 을 결정하는 단계로, `UI` 코드를 트리 형태로 변환한다.
<br/>

이러한 트리를 `UI` 트리라고 부르며, 코드 상의 컴포저블 함수 하나가 트리에서 노드 하나에 1:1 로 매핑이 되는데, `UI` 트리에서 각 노드를 레이아웃 노드라고 부른다.
<br/>

컴포저블 함수에는 자식 컴포저블 함수나 그 외에 여러 로직이나 상태 등이 들어가 있는데, 이 로직이나 상태가 매칭되는 레이아웃 노드에 같이 포함되어서 만들어지고 레이아웃 노드가 `UI` 트리로 만들어져서 다음 렌더링 단계인 `Layout` 단계로 넘어가게 된다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/rendering-composition.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/rendering-composition.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Layout
***
`Layout` 단계에서는 각 요소가 화면상 어디에 보여지게 할지 `Where` 를 결정하는 단계이다.
<br/>

트리를 탐색하면서 화면의 어떤 위치에 각 레이아웃 노드 혹은 컴포저블 함수를 배치할지 결정하게 되며, 자식 측정, 본인 크기 측정, 자식 배치 순으로 세 가지 알고리즘을 통해서 위치를 측정한다. 
<br/>

노드가 자식이 있는지 자식 측정을 하여 자식이 없다면 본인 크기를 측정하고 자식은 없기 떄문에 자식 배치는 생략된다.
<br/>

만약 노드가 자식을 가지고 있다면 자식 노드를 방문하여 자식 노드가 자식을 가지고 있는지 측정하고 자식이 없다면 본인 크기를 측정하고 자식은 없기 때문에 자식 배치는 생략되며 다시 상위 노드로 올라오고 이 노드는 자식 측정이 완료 되었기 때문에 자식을 기반으로 본인 크기를 측정하고 측정된 자식의 크기와 위치를 기반으로 현재 노드 안에 상대적 위치로 자식을 배치를 한다.
<br>

자식 측정부터 자식 배치까지 이와 같은 모든 과정이 끝나면 레이아웃 노드들의 `x`, `y` 좌표와 너비, 높이 정보가 저장된다. 
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/rendering-layout.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/rendering-layout.png" style="visibility: hidden;" />
</div>
<br/>

`Layout` 단계에서는 트리를 탐색을 하면서 세 가지 알고리즘을 따라서 가는데 모든 노드를 한 번씩만 다 방문을 하면서 그려지는 특징이 있는데, 이 점이 중요한 이유는 모든 뷰를 한 번씩만 간다는 사실이 기존 안드로이드 레거시(`XML`)에 비해서 성능상으로 큰 이점을 취할 수 있기 때문이다. 
<br/>

기존 `XML` 기반 안드로이드 뷰 시스템에서는 뷰의 구조가 복잡해지고 중첩될 수록 느려지는 현상이 일어났으나, 컴포즈에서는 뷰가 아무리 중첩되어도 단 한 번만 방문되기 때문에  뷰의 숫자가 많아지면 많아질수록 큰 장점으로 적용된다.
<br/>

`XML` 기반 안드로이드 뷰 시스템에서는 중첩된 뷰가 있으면 여러 번 뷰를 방문하게 되어 있기 때문에 기하급수적으로 시간이 늘어나는 현상이 일어나는 반면, 컴포즈에서는 노드의 수가 아무리 많아도 전체 노드를 단 한 번씩만 방문하기 때문에 산술급수적인 시간이 소요된다.
<br/>

이는 노드의 수가 적을 때에는 전체 노드의 방문 횟수가 큰 차이 나지 않지만 시간이 지나면 지날수록 차이가 많이 나는 것을 확인할 수 있으며 성능적으로 매우 큰 이점을 가져다 주는 장점이라고 할 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/rendering-layout-time.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/rendering-layout-time.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Drawing
***
`Drawing` 단계에서는 각 요소를 화면에 어떻게 그리는지 `How` 를 결정하는 단계로 `Layout` 단계와 마찬가지로 모든 노드를 한 번씩 방문하며 그리게 된다.
<br/>

`Layout` 단계에서 모든 위치치가 확정되었기 때문에 위에서부터 아래로 순서대로 방문하며 화면을 그리게 된다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/rendering-drawing.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/rendering-drawing.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Composition with Modifier
***
컴포저블 함수에 <code style="color: #eb5657;">Modifier</code> 가 설정이 되어 있다면 `UI` 트리에서 모디파이어의 값으로 노드가 생성되는데 레이아웃 노드를 감싸는 노드이기 때문에 래퍼(`Wrapper`) 노드 혹은 모디파이어 노드라고 부른다.
<br/>

여러 개의 모디파이어가 반복해서 감싸는 형태를 체인되어있다고 하는데, 여러 모디파이어들이 연결된 경우에 `UI` 트리에서는 래퍼 노드들이 체인되어 있는 것을 확인할 수 있다.
<br/>

`Layout` 단계에서는 각 레이아웃 노드가 방문이 될 때 원본 레이아웃뿐만 아니라 래핑되어 있는 전체 노드가 방문되기 때문에, 모디파이어가 위치 혹은 크기에 대한 정보일 때 레이아웃 단계에서 원본 레이아웃 노드의 값을 가져오는 것이 아니라 래핑된 래퍼 노드의 값을 가져와서 오버라이드한 후 그 값을 통해서 레이아웃 단계에서 위치나 크기를 설정하게 된다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/rendering-composition-modifier.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/rendering-composition-modifier.png" style="visibility: hidden;" />
</div>