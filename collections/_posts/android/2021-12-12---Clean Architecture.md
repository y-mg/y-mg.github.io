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
로버트 마틴(`Robert` `C.` `Martin`)이 제안한 소프트웨어를 계층으로 나누고, 각 계층이 특정 역할을 수행하도록 설계된 소프트웨어이다.
<br/>

즉, 각 계층을 분리하여 변경 사항이 다른 계층에 영향을 미치지 않도록 해서, 코드의 관리성, 확장성, 테스트 용이성을 향상시키는데 목적이 있다.
<br/>

MVP, MVVM 은 `Presentation` 계층에 초점을 두는 반면, 클린 아키텍처는 전체 앱 아키텍처에 초점을 두고 비즈니스 로직과 `UI` 를 분리하고 의존성 역전 원칙을 강조하는데 차이가 있다.
<br/>

클린 아키텍쳐가 제대로 동작하기 위해서는 의존성 역전 원칙을 지키는 것이 가장 중요하며, 반드시 외부에서 내부로, 저수준에서 고수준 정책으로 향해야 한다.
<br/>

예를 들어, 안드로이드에서 비즈니스 로직을 담당하는 `ViewModel` 은 로컬 `DB` 혹은 서버와의 통신 같은 세부적인 사항에 의존적이지 않도록 해야한다.
<br/>

클린 아키텍처를 적용을 할 때 계층 간의 의존성 관리와 코드 분리 등의 어려움이 발생할 수 있는데, 이를 해결하기 위해 의존성 주입 프레임워크를 사용하거나, `SOLID` 원칙을 준수하는 방법을 고려해야 한다.
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
- Entity: 앱의 핵심 비즈니스 로직이 포함된 곳이다.
- Use Case: 비즈니스 로직을 실행하는 곳이다.
- Presentation: `UI` 와 관련된 코드를 포함합니다.
- Data: `DB`, `API` 와 상호작용하는 코드가 위치한다.
<br/>
<br/>
<br/>



## SOLID
***
### 단일 책임 원칙 (Single Responsibility Principle)
클래스(객체)는 단 하나의 책임만 가져야 한다는 원칙으로, 책임의 의미는 하나의 기능으로 보면 된다.
<br/>

클래스가 한 가지 책임(기능)만 수행하도록 클래스를 따로따로 여러개 설계하라는 의미이다.
<br/>

단일 책임 원칙의 목적은 프로그램의 유지보수성을 높이기 위한 것으로, 만일 하나의 클래스에 기능(책임)이 여러개 있다면 기능 변경(수정)이 일어났을때 수정해야할 코드가 많아지기 때문이다.
<br/>
<br/>

### 개방-폐쇄 원칙 (Open-Closed Principle)
확장에 열려있어야 하며, 수정에는 닫혀있어야 한다는 원칙으로, 추상 클래스와 상속을 통한 클래스 관계 구축을 의미한다.
<br/>

기능 추가 요청이 오면 클래스를 확장을 통해 손쉽게 구현하고, 확장에 따른 클래스 수정은 최소화 하도록 프로그램을 작성해야 하는 설계 기법이다.
<br/>
<br/>

### 리스코프 치환 원칙 (Liskov Substitution Principle)
자식 타입은 언제나 부모 타입으로 교체할 수 있어야 한다는 원칙이다.
<br/>

다형성의 특징을 이용하기 위해 부모 타입으로 메소드를 실행해도 의도대로 실행되도록 구성을 해줘야 하는 것을 의미한다.
<br/>

기본적으로 부모 메소드의 오버라이딩을 조심스럽게 해야하는데, 부모 클래스와 동일한 수준의 선행 조건을 기대하고 사용하는 프로그램 코드에서 예상치 못한 문제를 일으킬 수 있기 때문이다. 
<br/>
<br/>

### 인터페이스 분리 원칙 (Interface Segregation Principle)
인터페이스를 각각 용도에 맞게 잘게 분리해야한다는 설계 원칙이다.
<br/>

인터페이스는 다중 상속이 가능하기 때문에 분리할 수 있으면 분리하고 각 클래스의 용도에 맞게 구현(<code style="color: #eb5657;">implements</code>)하라는 의미이다.
<br/>

인터페이스를 사용하는 클라이언트를 기준으로 분리함으로써, 클라이언트의 목적과 용도에 적합한 인터페이스만을 제공하는 것이 목적이다.
<br/>
<br/>

### 의존성 역전 원칙 (Dependency Inversion Principle)
구현 클래스에 의존하지 말고 인터페이스에 의존하라는 원칙이다.
<br/>

각 클래스간의 결합도(`coupling`)를 낮추기 위해 객체를 참조해서 사용해야하는 상황이 생긴다면, 그 객체를 직접 참조하는 것이 아니라 그 대상의 상위 요소(추상 클래스 or 인터페이스)를 참조하라는 의미이다.
<br/>
<br/>
<br/>



## 안드로이드 Clean Architecture
***
안드로이드 클린 아키텍처의 목적은 관심사의 분리이다.
<br/>

안드로이드에서 클릭 아키텍처는 `Data`, `Domain`, `UI` 레이어로 구성되며, 각 레이어마다 역할과 책임을 명확히 가지고 있으며, 각 레이어의 모듈들은 단방향의 참조를 한다.
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

단방향 데이터 흐름은 말 그대로 데이터가 한 방향으로 흐르는 것을 의미하는데, `UpStream` 방식과 `DownStream` 방식이 있다.
<br/>

`UpStream` 방식은 클릭 등의 이벤트를 발생시켜 `UI` -> `Domain` -> `Data` 로 전달함으로써 상위 레이어로 전달하는 방식을 말한다.
<br/>

`DownStream` 은 서버 혹은 로컬 `DB` 부터 받은 데이터를 `Data` -> `Domain` -> `UI` 로 전달함으로써 하위 레이어로 전달하는 방식을 말한다.
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



## UI Layer
***
`UI` 에 관련된 기능을 담당하고, `Domain` 레이어에 대한 의존성을 가지고 있는 레이어로, 사용자 입력을 처리하고 도메인 로직 호출을 담당한다.
<br>

<code style="color: #eb5657;">Actvity</code>, <code style="color: #eb5657;">Fragment</code> 등의 `UI` 요소와 `UI` 에 바인딩되는 데이터를 보유하고 있는 `ViewModel` 로 구분할 수 있다.
<br/>

<div style="
background-color: #ffffff;
background-image: url(/assets/images/android/content/clean_architecture-layer-ui.png);
background-size: contain;
background-repeat: no-repeat;
background-position: center center;
">
<img src="/assets/images/android/content/clean_architecture-layer-ui.png" style="visibility: hidden;" />
</div>
<br/>

`UI` 레이어에서 가장 중요한 역할을 하는 것은 `ViewModel` 로 3가지 책임을 가지고 있다.
- `UpStream` 관점에서 비즈니스 로직의 출발 지점으로, 사용자에게 `UI` 이벤트를 받아 이를 `Domain` 혹은 `Data` 레이어에 이벤트를 전달하는 비즈니스 로직의 시작을 트리거하는 책임이 있다.
- `DownStream` 관점에서 `Domain` 혹은 `Data` 레이어로부터 `UI` 에 바인딩할 데이터를 받고, 이를 통해 `UI` 에 데이터를 바인딩 해야하는 책임이 있다.
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
비즈니스 로직을 처리하는 핵심 부분으로, 비즈니스 로직에서 필요한 데이터 모델과 비즈니스 로직을 실행하는 기능 단위의 `UseCase` 를 가지고 있으며, 다른 레이어에 대한 의존성을 가지지 않고 독립적으로 분리되어 있는 레이어이다.
<br/>

보통 외부 서비스에 대한 의존성이 없는 순수한 자바 또는 코틀린 클래스로 이루어지는데, 테스트 용이성, 재사용성, 유지 보수 용이성 등을 향상 시킬 수 있기 떄문이다.
- `Domain` 레이어를 외부 서비스에 의존하지 않는 순수한 코드로 유지하면, 도메인 로직은 독립적으로 테스트하고 재사용할 수 있게 되고, 이것은 안드로이드 프레임워크나 외부 라이브러리의 변경에 영향을 받지 않고 도메인 로직을 변경하거나 확장할 수 있다는 것을 의미한다.
- 외부 서비스에 의존성이 없으면 단위 테스트가 쉬워지는데, 테스트할 때 외부 서비스의 `Mock` 객체를 사용해서 독립적으로 `Domain` 레이어의 로직을 검증할 수 있게 된다.
- `Domain` 레이어를 외부 서비스로부터 분리하면, 동일한 비즈니스 로직을 여러 프로젝트 혹은 모듈에서 재사용할 수 있게 외어 코드의 일관성을 유지하고 개발 생산성을 향상시킨다.
<br/>
<br/>

`Domain` 레이어는 비즈니스 로직을 실행하는 `UseCase` 모듈을 가지고 있는데 보통 "동사(동작) + 명사/대상(`Optional`) + UseCase" 형태로 네이밍을 정의하며, 클래스 이름을 그대로 사용하면서 함수를 호출할 수 있게 해주는 <code style="color: #eb5657;">operator fun invoke()</code> 형태로 정의한다.
<br/>

`Domain` 레이어에서 `UseCase` 모듈을 사옹하는 이유는 `ViewModel` 의 3가지 책임 이외에 관련되지 않은 부분, 예를 들어, `ViewModel` 의 책임 이외의 비즈니스 로직 혹은 여러 `ViewModel` 에서 사용되는 중복 코드 등을 `UseCase` 패턴으로 분리해서 원활한 코드 관리를 하는 것이 목적이다.
<br/>

`Repository` 인터페이스는 `Data` 레이어의 `DataSource` 를 추상화하고 `Domain` 레이어와 `DataSource` 간의 의존성을 분리하는데 사용된다.
<br/>

이를 통해 데이터의 원본(서버 혹은 로컬 DB)에 대한 접근을 추상화하고, `Domain` 레이어의 로직은 실제 `DataSource` 에 독립적으로 작동할 수 있게 된다.
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
서버 혹은 로컬 `DB` 와 통신하여 데이터를 가져오는 역할을 하는 레이어로, `Domain` 레이어에 대한 의존성을 가지고 있으며, 데이터의 `CRUD`(`Create`, `Read`, `Update`, `Delete`) 작업을 처리한다.
<br/>

`Domain` 레이어의 `Repository` 인터페이스에 대한 구현체를 포함하고 있는데, `UI` 레이어는 `Data` 레이어에 직접 접근해서 데이터를 가져올 수 없기 때문에 `Domain` 레이어의 `Repository` 를 통해 데이터를 가져와야 하기 때문이다.
<br/>

`Data` 레이어의 데이터를 `Domain` 레이어의 데이터로 변환해주는 `Mapper` 를 가지고 있는 있는 이유는 `Repository` 가 `DataSource` 의 데이터를 그대로 전달 받아 별다른 처리 없이 그대로 반환하여 이를 `UI` 레이어에서 사용하는 경우 서버 단의 이슈가 `UI` 에 영향을 끼칠 수 있다.
<br/>

필드 삭제, 필드 이름 변경 등 서버가 데이터 구조를 변경하면 이를 참조하고 있는 다른 레이어들에서도 변경이 발생할 수 밖에 없다.
<br/>

예를 들어, 데이터의 정보를 `UI` 에 보여주기 위해 서버에 각각 다른 데이터의 요청을 `N` 번 해야하는 경우에 `N` 개의 데이터에 대한 변경 사항은 모두 클라이언트가 영향을 받게 되어 `Repository` 패턴의 목적인 `Data` 레이어와의 결합도를 느슨하게 하는 것과는 반대로 다시 강한 결합이 되어버린다.
<br/>

따라서 서버 혹은 로컬 `DB` 에서 가져온 데이터를 `Domain` 모델의 데이터로 `mapping` 시켜주는 역할을 하는 `Mapper` 을 사용하여 `UI` 레이어가 `Data` 레이어에 영향을 받지 않도록 할 수 있다.
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
- `DownStream` 관점에서 `DataSource` 로부터 받아온 데이터를 새로운 모델로 가공(`Mapper`)하여 하위 레이어(`Domain` `or` `UI`)에 전달해주는 책임이 있다.
<br/>
<br/>

`DataSource` 는 `API` 서버 혹은 로컬 `DB` 로부터 실제 데이터를 가져오는 역할로 2가지 책임을 가지고 있다.
- `UpStream` 관점에서 서버 혹은 로컬 `DB` 에 데이터를 요청하는 책임이 있다.
- `DownStream` 관점에서 `Repository` 에 데이터를 제공하는 책임이 있다.
<br/>
<br/>

즉, `DataSource` 는 서버 혹은 로컬 `DB` 로부터 받은 실제 데이터를 1차 생산자로 제공해주는 모듈이고, `Repository` 는 `DataSource` 로부터 받은 데이터를 하위 레이어(`Domain` `or` `UI`)에 제공해주는 2차 생산자의 역할이라고 볼 수 있다.
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