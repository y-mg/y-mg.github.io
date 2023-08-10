---
layout: post
title: "Clean Architecture"
date: 2021-12-12T00:00:00Z
authors: ["y-mg"]
categories: [Android, Architecture]
description: "Feature 단위로 레이어를 만들고 관심사를 분리하여 변화에 유연하도록 해주는 아키텍쳐"
thumbnail: "/assets/images/android/banner/architecture.png"
image: "/assets/images/android/banner/architecture.png"
comments: false
---

## Clean Architecture
***
로버트 마틴(`Robert` `C.` `Martin`)이 제안한 `Feature` 단위로 레이어를 만들고 관심사를 분리하여 변화에 유연하도록 해주는 아키텍쳐이다.
<br/>

클린 아키텍쳐가 제대로 동작하기 위해서는 의존정 규칙을 지키는 것이 가장 중요하며 반드시 외부에서 내부로, 저수준에서 고수준 정책으로 향해야 한다.
<br/>

예를 들어 안드로이드에서 비즈니스 로직을 담당하는 `ViewModel` 은 로컬 `DB` 혹은 서버와의 통신 같은 세부적인 사항에 의존적이지 않도록 해야한다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/clean_architecture-origin.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/clean_architecture-origin.png" style="visibility: hidden;" />
</div>
<br/>
<br/>



## 안드로이드 Clean Architecture
***
안드로이드 클린 아키텍처의 목적은 관심사의 분리이다.
<br/>

안드로이드에서 클릭 아키텍처는 `Data`, `Domain`, `Presentation(UI)` 레이어로 구성되며 각 레이어마다 역할과 책임을 명확히 가지고 있으며 각 레이어의 모듈들은 단방향의 참조를 한다.
<br/>

실제 개발 환경에서는 각 레이어를 모듈(`module`) 로 만드는게 일반적인데 하나의 모듈에서 모든 개발을 진행하게 되면 액티비티에서 `DB` 의 인스턴스에 접근하는 등과 같이 사실상 모든 코드에 접근 가능하기 때문에 의존성 규칙을 위반할 수 있는 실수가 발생할 수 있기 때문이다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/clean_architecture-android-flow.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/clean_architecture-android-flow.png" style="visibility: hidden;" />
</div>
<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/clean_architecture-android-detail.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/clean_architecture-android-detail.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Clean Architecture 에서 각 Layer 동작
***
"`A` 는 상품을 구매하기 위해 앱을 실행시키고 필요한 상품을 클릭했다" 라고 하는 단순한 동작 하나라도 앱 내에서는 각 레이어들의 단방향 데이터 흐름(`UpStream` + `DownStream`)으로 통신이 시작된다.
<br/>

단방향 데이터 흐름은 말 그대로 데이터가 한 방향으로 흐르는 것을 의미하는데 `UpStream` 방식과 `DownStream` 방식이 있다.
<br/>

`UpStream` 방식은 클릭 등의 이벤트를 발생시켜 `Presentation(UI)` -> `Domain` -> `Data` 로 전달함으로써 상위 레이어로 전달하는 방식을 말한다.
<br/>

`DownStream` 은 서버 혹은 로컬 `DB` 부터 받은 데이터를 `Data` -> `Domain` -> `Presentation(UI)` 로 전달함으로써 하위 레이어로 전달하는 방식을 말한다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/clean_architecture-layer-flow.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/clean_architecture-layer-flow.png" style="visibility: hidden;" />
</div>
<br/>
<br/>
<br/>



## Presentation(`UI`) Layer
***
`UI` 에 관련된 기능을 담당하고 `Domain` 레이어에 대한 의존성을 가지고 있는 레이어이다.
<br>

<code style="color: #eb5657;">Actvity</code>, <code style="color: #eb5657;">Fragment</code> 등의 `UI` 요소와 `UI` 에 바인딩되는 데이터를 보유하고 있는 `ViewModel` 로 구분할 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/clean_architecture-layer-presentation.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/clean_architecture-layer-presentation.png" style="visibility: hidden;" />
</div>
<br/>

`Presentation(UI)` 레이어에서 가장 중요한 역할을 하는 것은 `ViewModel` 로 3가지 책임을 가지고 있다.
- `UpStream` 관점에서 비즈니스 로직의 출발 지점으로 사용자에게 `UI` 이벤트를 받아 이를 `Domain` 혹은 `Data` 레이어에 이벤트를 전달하는 비즈니스 로직의 시작을 트리거하는 책임이 있다.
- `DownStream` 관점에서 `Domain` 혹은 `Data` 레이어로부터 `UI` 에 바인딩할 데이터를 받고 이를 통해 `UI` 에 데이터를 바인딩 해야하는 책임이 있다.
- <code style="color: #eb5657;">LiveData</code>, <code style="color: #eb5657;">StateFlow</code> 와 같은 데이터 홀더 클래스를 통해 `UI` 에 바인딩할 데이터를 보유해야 하는 책임이 있다.
<br/>

```kotlin
@AndroidEntryPoint
class MainActivity : BaseActivity<ActivityMainBinding>() {

    private val viewModel: MainViewModel by viewModels()

    /* ... */

    fun onClickSearch() {
        viewModel.getSearchData(binding.editText.toString())
    }
}

@HiltViewModel
class MainViewModel @Inject constructor(
    private val getSearchUseCase: GetSearchUseCase,
) {
    private val _searchItems = MutableStateFlow<List<SearchModel>>(emptyList())
    val searchItems = _searchItems.asStateFlow()   

    fun getSearchData(query: String) = viewModelScope.launch {
        getSearchUseCase(query)
            .onEach {
                _searchItems.emit(it)
            }
            .collect()
    }
}
```
<br/>
<br/>



## Domain Layer
***
비즈니스 로직에서 필요한 데이터 모델과 기능 단위의 `UseCase` 를 가지고 있으며 다른 레이어에 대한 의존성을 가지지 않고 독립적으로 분리되어 있는 레이어이다.
<br/>

`Domain` 레이어는 필수가 아닌 선택적인(`Optional`) 부분이지만, 앱의 규모가 커질수록 `Domain` 레이어의 필요성이 늘어난다.
<br/>

`Domain` 레이어는 `UseCase` 모듈을 가지고 있는데 보통 "동사(동작) + 명사/대상(`Optional`) + UseCase" 형태로 네이밍을 정의하고 클래스 이름을 그대로 사용하면서 <code style="color: #eb5657;">invoke()</code> 함수를 호출할 수 있게 해주는 <code style="color: #eb5657;">operator fun invoke()</code> 형태로 정의한다.
<br/>

`Domain` 레이어에서 `UseCase` 모듈을 사옹하는 이유는 `ViewModel` 의 3가지 책임 이외에 관련되지 않은 부분, 예를 들어 `ViewModel` 책임 이외의 비즈니스 로직 혹은 여러 `ViewModel` 에서 사용되는 중복 코드 등을 `UseCase` 패턴으로 분리하여 더욱 깔끔한 코드를 작성하는 것이 목적이다.
<br/>

```kotlin
class GetSearchUseCase @Inject constructor(
    private val repository: SearchRepository
) {

    operator fun invoke(query: String): Flow<List<SearchModel>> = flow {
        coroutineScope {
            val responsImage = async {
                repository.getImages(query)
            }

            val responseVideo = async {
                repository.getVideos(query)
            }

            val images = responsImage.await()
            val videos = responseVideo.await()

            val data = images + videos
            emit(data)
        }
    }
}

@HiltViewModel
class MainViewModel @Inject constructor(
    private val getSearchUseCase: GetSearchUseCase,
) {
    private val _searchItems = MutableStateFlow<List<SearchModel>>(emptyList())
    val searchItems = _searchItems.asStateFlow()   

    fun getSearchData(query: String) = viewModelScope.launch {
        getSearchUseCase()
            .onEach {
                _searchItems.emit(it)
            }
            .collect()
    }
}
```
<br/>
<br/>



## Data Layer
***
서버 혹은 로컬 `DB` 와 통신하여 데이터를 가져오는 역할을 하는 레이어로 `Domain` 레이어에 대한 의존성을 가지고 있다.
<br/>

`Domain` 레이어의 `Repository` 인터페이스에 대한 구현체를 포함하고 있는데 `Presentation` 레이어는 `Data` 레이어에 직접 접근하여 데이터를 가져올 수 없기 때문에 `Domain` 레이어의 `Repository` 를 통해 데이터를 가져와야 하기 때문이다.
<br/>

`Data` 레이어의 데이터를 `Domain` 레이어의 데이터로 변환해주는 `Mapper` 를 가지고 있는 있는 이유는 `Repository` 가 `DataSource` 의 데이터를 그대로 전달 받아 별다른 처리 없이 그대로 반환하여 이를 `Presentation` 레이어에서 사용하는 경우 서버 단의 이슈가 `Presentation` 에 영향을 끼칠 수 있다.
<br/>

필드 삭제, 필드 이름 변경 등 서버가 데이터 구조를 변경하면 이를 참조하고 있는 다른 레이어들에서도 변경이 발생할 수 밖에 없다.
<br/>

예를 들어 데이터의 정보를 `UI` 에 보여주기 위해 서버에 각각 다른 데이터의 요청을 `N` 번 해야하는 경우에 `N` 개의 데이터에 대한 변경 사항은 모두 클라이언트가 영향을 받게 되어 `Repository` 패턴의 목적인 `Data` 레이어와의 결합도를 느슨하게 하는 것과는 반대로 다시 강한 결합이 되어버린다.
<br/>

따라서 서버 혹은 로컬 `DB` 에서 가져온 데이터를 `Domain` 모델의 데이터로 `mapping` 시켜주는 역할을 하는 `Mapper` 을 사용하여 `Presentation` 레이어가 `Data` 레이어에 영향을 받지 않도록 할 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/clean_architecture-layer-data.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/clean_architecture-layer-data.png" style="visibility: hidden;" />
</div>
<br/>

`Repository` 는 데이터의 저장소 역할로 3가지 책임을 가지고 있다.
- `UpStream` 관점에서 앱에서 필요한 데이터를 `DataSource` 에 요청하는 책임이 있다.
- `DownStream` 관점에서 `DataSource` 로부터 받아온 데이터를 새로운 모델로 가공(`Mapper`)하여 하위 레이어(`Domain` `or` `Presetation(UI)`)에 전달해주는 책임이 있다.
<br/>
<br/>

`DataSource` 는 `API` 서버 혹은 로컬 `DB` 로부터 실제 데이터를 가져오는 역할로 2가지 책임을 가지고 있다.
- `UpStream` 관점에서 서버 혹은 로컬 `DB` 에 데이터를 요청하는 책임이 있다.
- `DownStream` 관점에서 `Repository` 에 데이터를 제공하는 책임이 있다.
<br/>
<br/>

즉, `DataSource` 는 서버 혹은 로컬 `DB` 로부터 받은 실제 데이터를 1차 생산자로 제공해주는 모듈이고, `Repository` 는 `DataSource` 로부터 받은 데이터를 하위 레이어(`Domain` `or` `Presetation(UI)`)에 제공해주는 2차 생산자의 역할이라고 볼 수 있다.
<br/>

```kotlin
data class ImageResponse(
    @SerializedName("title")
    val title: String,

    @SerializedName("url")
    val url: String,
)

fun<T> Response<T>.verify() : T {
    if (this.isSuccessful) {
        return this.body() ?: throw Exception(this.code().toString())
    } else {
        throw Exception(this.code().toString())
    }
}


interface MediaApi {

    @GET("v1/media/images")
    suspend fun getImages(): Response<ImageResponse>
}


class MediaDataSourceImpl @Inject constructor(
    private val api: MediaApi
): MediaDataSource {

    override suspend fun getImages(): ImageResponse = api.getAssets().verify()
}

interface MediaDataSource {

    suspend fun getImages(): ImageResponse
}


class MediaRepositoryImpl @Inject constructor(
    private val dataSource: MediaDataSource
): MediaRepository {

    override suspend fun getImages(): List<ImageModel> {
        val data = dataSource.getImages().map {
            ImageModel(
                title = it.title,
                url = it.url
            )
        }
        return data
    }
}

interface MediaRepository {

    suspend fun getImages(): List<ImageModel>
}
```