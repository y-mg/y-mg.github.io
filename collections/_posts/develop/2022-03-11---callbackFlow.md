---
layout: post
title: "callbackFlow"
date: 2022-03-11T00:00:00Z
authors: ["y-mg"]
categories: [Android, Coroutine]
description: "Callback 을 Flow 로 변경하는 Flow Builder"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## callbackFlow
***
`Callback` 을 <code style="color: #eb5657;">Flow</code> 로 변경하는 `Flow` `Builder` 이다.
<br/>

<code style="color: #eb5657;">Flow</code> 와 `Listener` 내부의 스코프가 다르기 때문에 `Listener` 내부에서 <code style="color: #eb5657;">emit()</code> 을 사용하여 데이터를 보낼 수 없는데, <code style="color: #eb5657;">callbackFlow</code> 를 사용하면 `Listener` 내부의 값을 <code style="color: #eb5657;">Flow</code> 로 보낼 수 있다.
<br/>

```kotlin
fun EditText.textChanges(): Flow<CharSequence?> =
    callbackFlow {
        val listener = object: TextWatcher {
            override fun afterTextChanged(s: Editable?) = Unit
            override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) = Unit
            override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) { trySend(s) }
        }
        addTextChangedListener(listener)
        awaitClose { removeTextChangedListener(listener) }
    }.onStart { emit(text) }
```