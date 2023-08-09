---
layout: post
title: "Retrofit / OkHttp"
date: 2018-07-13T00:00:00Z
authors: ["y-mg"]
categories: [Android, Network]
description: "HTTP 통신 라이브러리"
thumbnail: "/assets/images/android/banner/network.png"
image: "/assets/images/android/banner/network.png"
comments: false
---

## Retrofit / OkHttp
***
`Square` 사에서 만든 `HTTP` 통신 라이브러리이다. 
<br/>

`Retrofit` 을 사용하면 어노테이션(`Annotation`) 사용으로 코드의 가독성이 좋고 직관적인 설계가 가능하다.
<br/>

하지만 `Retrofit` 역시 `HTTP` 통신을 할 때 `OkHttp` 에 의존하고 있고 네트워크 `Interceptor` 를 통해 API 가 통신되는 모든 활동을 모니터링 할 수 있고 서버와의 통신 시간을 조절할 수 있다는 장점이 있다.
<br/>

이처럼 두 라이브러리 모두 각각 이점을 가지고 있기 때문에 최고의 성능을 내기 위해서 함께 사용하는 것이 보편적이다.
<br/>
<br/>
<br/>



## Request
***
`Retrofit` 은 <code style="color: #eb5657;">baseUrl()</code> 에 인자를 전달하여 객체를 생성하고 네트워크 호출을 할 때마다 미리 정의해놓은 인터페이스 메소드에 필요한 데이터들을 전달해서 호출하는 형식으로 구성된다.
<br/>


```kotlin
Retrofit.Builder()
    .addConverterFactory(GsonConverterFactory.create())
    .baseUrl(URL)
    .build()
```
- `JSON` 은 안드로이드 개발 환경에서 바로 사용 가능한 데이터 형식이 아니기 때문에 데이터 클래스로 변환해줄 컨버터를 사용해야하는데, <code style="color: #eb5657;">GsonConverterFactory</code> 를 사용하면 별도의 변환 과정 없이 바로 사용할 수 있다.
<br/>
<br/>

인터페이스를 생성하고 원하는 요청 방식과 파라미터에 대해 정의하여 인터페이스 메소드에 필요한 데이터를 전달해서 호출할 수 있다.
<br/>

```kotlin
interface ApiServe{
    /*  
        HTTP Methods
        @GET - 조회
        @POST - 등록
        @PUT - 갱신
        @PATCH - 수정
        @DELETE - 삭제
    */
    @GET(END_POINT)
    fun ApiCall(
        @Query(PARAM_ACTION) action: String,
    ): Call<Model>
}
```
<br/>

`OkHttp` 는 수동으로 요청을 구성해야하며 그렇기에 코드 가독성이 좋지 않다.
<br/>

```kotlin
val request = Request.Builder()
        .url("$URL$PATH/?$PARAM_ACTION=$VALUE_QUERY")
        .build()
```
<br/>
<br/>



## Response
***
`JSON` 은 안드로이드 개발 환경에서 바로 사용 가능한 데이터 형식이 아니기 때문에 데이터 클래스로 변환해줄 컨버터를 사용하거나 직접 변환해서 사용해야 한다.
<br/>

`Retrofit` 은 객체 생성 시 <code style="color: #eb5657;">GsonConverterFactory</code> 를 연결했다면 응답 객체의 `body` 를 별도의 변환 과정(역직렬화) 없이 바로 사용할 수 있다.
<br/>

```kotlin
call?.enqueue(
    object: Callback<Model> {
        override fun onFailure(call: Call<Model>, t: Throwable) {
            /* ... */
        }
    
        override fun onResponse(
            call: Call<Model>, 
    	    response: Response<Model>
        ) {
            response.body()?.let { /* ... */ }
        }
    }
)
```
<br/>

`OkHttp` 에서는 반환 받은 `JSON` 객체를 데이터 클래스로 바로 변환해주지 못하기 때문에 별도의 과정을 통해 직접 변환해서 사용해야 한다.
<br/>

대표적으로 자바 `Object` 를 `JSON` 으로 변환해주거나(직렬화) 반대로 `JSON` 을 자바 `Object` 로 변환해주는(역직렬화) `GSON` 라이브러리를 사용하는 방법이 있다.
<br/>

```kotlin
client.newCall(request).enqueue(
    okhttp3.Callback {
        override fun onFailure(call: okhttp3.Call, e: IOException) {
            /* ... */
        }

        override fun onResponse(
            call: okhttp3.Call, 
            response: okhttp3.Response
        ) {
            response.body()?.let { 
                val result = Gson().fromJson(it.string(), Model::class.java)
                /* ... */
            }
        }
    }
)
```