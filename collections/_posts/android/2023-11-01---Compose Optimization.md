---
layout: post
title: "Compose Optimization"
date: 2023-11-01T00:00:00Z
authors: ["y-mg"]
categories: [Android, Jetpack]
description: "Compose 성능 최적화 방법"
thumbnail: "/assets/images/android/banner/jetpack.png"
image: "/assets/images/android/banner/jetpack.png"
comments: false
---

## Compose 성능 최적화
***
컴포즈에서는 불필요한 리컴포지션을 스킵하여 불필요한 리소스 낭비가 없도록 신경써야 하는데 `Android` `Studio` 에서 제공하는 `Layout` `Insepector` 를 통해 이를 체크할 수 있다.
<br/>

`Layout` `Insepector` 실행 후 옵션에서 `Show` `Recomposition` `Counts` 를 체크하면 리컴포지션 횟수 및 스킵된 횟수를 확인할 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/layout-inspector.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/layout-inspector.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## 상태 읽기 연기 (Defer State Reading)
***
상태 일기 연기는 실제값을 직접 전달하는게 아니라 람다를 사용해서 바뀌는 값을 리턴한다면 리컴포지션을 생략할 수 있는 기법으로, `Composition` 단계를 스킵하고 `Layout` 단계부터 실행된다.
<br/>

```kotlin
// 적용 전
@Composable
private fun Title(
    memoText: String,
    scroll: Int
) {
    val maxOffset = with(LocalDensity.current) { MaxTitleOffset.toPx() }
    val minOffset = with(LocalDensity.current) { MinTitleOffset.toPx() }

    Column(
        modifier = Modifier
            .heightIn(min = MaxTitleOffset)
            .offset {
                val offset = (maxOffset - scroll).coerceAtLeast(minOffset)
                IntOffset(x = 0, y = offset.toInt())
            }
            .fillMaxWidth()
            .background(Color.White)
    ) {
        /* ... */
    }
}
```

```kotlin
// 적용 후
@Composable
private fun Title(
    memoText: String,
    scrollProvider: () -> Int
) {
    val maxOffset = with(LocalDensity.current) { MaxTitleOffset.toPx() }
    val minOffset = with(LocalDensity.current) { MinTitleOffset.toPx() }

    Column(
        modifier = Modifier
            .heightIn(min = MaxTitleOffset)
            .offset {
                val offset = (maxOffset - scrollProvider()).coerceAtLeast(minOffset)
                IntOffset(x = 0, y = offset.toInt())
            }
            .fillMaxWidth()
            .background(Color.White)
    ) {
        /* ... *.
    }
}
```
<br/>
<br/>



## Lazy Layout Key
***
<code style="color: #eb5657;">LayzyColumn</code> 에서는 <code style="color: #eb5657;">items</code> 에 `key` 값을 설정하여 리컴포지션을 스킵할 수 있으며, <code style="color: #eb5657;"></code>Column 에서 <code style="color: #eb5657;">for</code> 문을 통해 아이템을 하니씩 가져오는 경우에는 <code style="color: #eb5657;">key()</code> 를 사용해서 리컴포지션을 스킵할 수 있다.
<br/>

```kotlin
// LazyColumn
@Composable
fun ColumnScope.MemoList(
    onClickAction: (Int) -> Unit,
    memoList: SnapshotStateList<Memo>
) {
    LazyColumn(
        modifier = Modifier.weight(1f)
    ) {
        items(
            items = memoList,
            key = { it.id }
        ) { 
            /* ... */
        }
    }
}
```

```kotlin
// Column
@Composable
fun ColumnScope.MemoList(
    onClickAction: (Int) -> Unit,
    memoList: SnapshotStateList<Memo>
) {
    Column(
        modifier = Modifier.weight(1f)
    ) {
        for (memo in memoList) {
            key(memo.id) {
                /* ... */
            }
        }
    }
}
```
<br/>
<br/>



## 계산 최소화 (Minimize Calculation)
***
계산 최소화는 계산하는 위치를 안쪽이 아닌 밖으로 빼서 계산을 최소화 하는 것으로, <code style="color: #eb5657;">items</code> 안에서는 모든 시점에 변화가 있지 않아도 항상 매번 보여줄 때마다 정렬되기 때문에 <code style="color: #eb5657;">remember</code> 를 사용해서 정렬된 값을 저장을 하고 <code style="color: #eb5657;">items</code> 에 등록하면 필요할 때만 정렬된다.
<br/>

```kotlin
// 적용 전
@Composable
fun HomeScreen(homeState: HomeState) {
    MemoAppTheme {
        Surface(
            modifier = Modifier.fillMaxSize(),
            color = MaterialTheme.colors.background
        ) {
            val memoList = remember { memos }
            
            /* ... */
        }
    }
}

// 적용 전
@Composable
fun ColumnScope.MemoList(
    onClickAction: (Int) -> Unit,
    memoList: SnapshotStateList<Memo>
) {
    LazyColumn(
        modifier = Modifier.weight(1f)
    ) {
        items(
            items = memoList.sortedBy { it.id },
            key = { it.id }
        ) { 
            /* ... */
        }
    }
}
```

```kotlin
// 적용 후
@Composable
fun HomeScreen(homeState: HomeState) {
    MemoAppTheme {
        Surface(
            modifier = Modifier.fillMaxSize(),
            color = MaterialTheme.colors.background
        ) {
            val memoList = remember { memos.sortedBy { it.id }.toMutableStateList() }
            
            /* ... */
        }
    }
}

// 적용 후
@Composable
fun ColumnScope.MemoList(
    onClickAction: (Int) -> Unit,
    memoList: SnapshotStateList<Memo>
) {
    LazyColumn(
        modifier = Modifier.weight(1f)
    ) {
        items(
            items = memoList,
            key = { it.id }
        ) { 
            /* ... */
        }
    }
}
```
<br/>
<br/>



## 역방향 쓰기 금지 (Avoid Backward Writes)
***
역방향 쓰기는 읽힘 이후에 쓰는 것을 의미하며 무한루프를 발생시키는 문제를 야기하는데, 컴포지션 스코프 안에서 사용하는 값을 직접 변경하는 경우 무한루프가 발생하는 문제가 생길 수 있다.
<br/>

```kotlin
@Composable
fun AddMemo(memoList: SnapshotStateList<Memo>) {
    val inputValue = remember { mutableStateOf("") }
    var count by remember { mutableStateOf(0) }

    Row(
        modifier = Modifier
            .padding(all = 16.dp)
            .height(100.dp),
        horizontalArrangement = Arrangement.End
    ) {
        TextField(
            modifier = Modifier
                .fillMaxHeight()
                .weight(1f),
            value = inputValue.value,
            onValueChange = { textFieldValue ->
                inputValue.value = textFieldValue
            }
        )
        Button(
            onClick = {
                memoList.add(
                    index = 0,
                    Memo(memoList.size, inputValue.value)
                )
                inputValue.value = ""
                count++ // 정상
            },
            modifier = Modifier
                .wrapContentWidth()
                .fillMaxHeight()
        ) {
            // 무한루프 발생
            Text("ADD\n$count") // 읽기
            count++ // 쓰기 
        }
    }
}
```