---
layout: post
title: "Serializable / Parcelable"
date: 2018-07-15T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "객체를 직렬화하는 인터페이스"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## Serializable
***
<code style="color: #eb5657;">Serializable</code> 은 `Java` 표준 인터페이스로, 객체를 바이트(`Byte`) 스트림으로 변환하거나 바이트 스트림을 다시 객체로 변환하는 작업을 수행한다.
<br/>

특별한 메소드 구현 없이 직렬화 및 역직렬화가 되지만, 자바 Reflection 을 사용하기 때문에 오버헤드가 발생할 수 있다.
<br/>

```java
import java.io.Serializable;

public class MySerializableObject implements Serializable {
    /* ... */
}
```
<br/>
<br/>


## Parcelable
***
<code style="color: #eb5657;">Parcelable</code> 은 안드로이드에서 제공하는 인터페이스로, 객체를 바이트(`Byte`) 스트림으로 변환하거나 바이트 스트림을 다시 객체로 변환하는 작업을 수행한다.
<br/>

안드로이드 시스템 간 데이터 전달에 최적화되어 있으나 직렬화 및 역직렬화 과정을 명시적으로 작성해야 하기 때문에 클래스의 구조 변경이 복잡할 수 있다.
<br/>

```java
import android.os.Parcel;
import android.os.Parcelable;

public class MyParcelableObject implements Parcelable {
    
    public static final Parcelable.Creator<MyParcelableObject> CREATOR
            = new Parcelable.Creator<MyParcelableObject>() {
        public MyParcelableObject createFromParcel(Parcel in) {
            return new MyParcelableObject(in);
        }

        public MyParcelableObject[] newArray(int size) {
            return new MyParcelableObject[size];
        }
    };

    private MyParcelableObject(Parcel in) {
        // Parcel 에서 데이터를 읽는 생성자
    }

    public int describeContents() {
        return 0;
    }

    public void writeToParcel(Parcel out, int flags) {
        // 객체의 데이터를 Parcel 에 쓰는 코드
    }

    /* ... */
}
```