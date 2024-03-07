---
layout: post
title: "RecyclerView"
date: 2018-02-13T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "뷰를 재활용하고 데이터를 리스트화해서 보여주는 컨테이너"
thumbnail: "/assets/images/android/banner/android.png"
image: "/assets/images/android/banner/android.png"
comments: false
---

## RecyclerView
***
뷰를 재활용하고 데이터를 리스트화해서 보여주는 컨테이너이다.
<br/>

<code style="color: #eb5657;">ListView</code> 는 스크롤 시 화면에 보이는 만큼 뷰를 생성하고, 보이지 않으면 삭제하길 반복해서 리소스 낭비가 심하다는 단점이 있다.
<br/>

이를 보완하기 위해 나온 <code style="color: #eb5657;">RecyclerView</code> 는 화면에 보이는 만큼 뷰 객체를 생성하고, 스크롤 시 새로 그려져야할 뷰가 있는 위치로 <code style="color: #eb5657;">ViewHolder</code> 를 이동시켜 재사용한다.
<br/>

<code style="color: #eb5657;">ViewHolder</code> 는 화면에 표시될 아이템 뷰를 저장하는 객체로, 화면에 보이는 만큼 생성되고, 새로 그려져야할 뷰가 있다면 가장 위에 생성된 <code style="color: #eb5657;">ViewHolder</code> 를 재사용해서 데이터만 바꾼다.
<br/>
<br/>
<br/>



## RecyclerView 구성 요소
***
### LayoutManager
<code style="color: #eb5657;">RecyclerView</code> 의 레이아웃을 결정하는 역할로, <code style="color: #eb5657;">LinearLayoutManager</code>, <code style="color: #eb5657;">GridLayoutManager</code> 등을 사용할 수 있다.
<br/>

<code style="color: #eb5657;">RecyclerView</code> 자체는 자신이 어떻게 배치될지, 어떤 위치에 놓일지 모르기 때문에 `LayoutManager` 를 사용해서 아이템들을 적절히 배치하는 작업을 한다.
<br/>
<br/>

### Adapter
<code style="color: #eb5657;">RecyclerView</code> 에 보여줄 데이터를 제공하고, 뷰를 생성하며, 뷰에 데이터를 바인딩하는 역할을 한다.
<br/>

여기서 뷰를 생성한다는 것은 레이아웃을 `Inflate` 한 뷰 객체를 가지는 <code style="color: #eb5657;">ViewHolder</code> 를 메모리에 생성한다는 것을 의미한다. 
<br/>

```kotlin
class DataAdapter : RecyclerView.Adapter<DataViewHolder>() {
		
    override fun getItemCount(): Int = itemCount

    override fun onCreateViewHolder(
        parent: ViewGroup,
        viewType: Int
    ): DataViewHolder {
        return DataViewHolder(
            LayoutInflater.from(parent.context).inflate(
                R.layout.items_data,
                parent,
                false
            )
        )
    }

    override fun onBindViewHolder(
        holder: DataViewHolder,
        position: Int
    ) {
        get(position)?.let {
            holder.bind(it)
        }
    }
}
```
- <code style="color: #eb5657;">onCreateViewHolder()</code> 는 <code style="color: #eb5657;">ViewHolder</code> 를 생성하는 함수이다.
- <code style="color: #eb5657;">onBindViewHolder()</code> 는 생성된 <code style="color: #eb5657;">ViewHolder</code> 에 데이터를 바인딩 해주는 함수이다.
- <code style="color: #eb5657;">getItemCount()</code> 는 데이터의 총 갯수를 반환하는 함수이다.
<br/>
<br/>

### ViewHolder
<code style="color: #eb5657;">RecyclerView</code> 의 아이템 뷰를 정하는 역할로, 생성된 <code style="color: #eb5657;">ViewHolder</code> 를 캐시하고 필요할 때 재활용하여 성능을 향샹사키는 역할을 한다.
<br/>
<br/>
<br/>



## RecyclerView 성능 향상
***
### setHasStableIds()
<code style="color: #eb5657;">RecyclerView</code> 의 성능 향상 중 가장 기본은 <code style="color: #eb5657;">onBindViewHolder()</code> 의 호출을 최소화 하는 것이다.
<br/>

<code style="color: #eb5657;">setHasStableIds(true)</code> 를 사용하면 각각 아이템 포지션에 지정된 `id` 를 기준으로 상황에 따라 <code style="color: #eb5657;">onBindViewHolder()</code> 호출을 제외시킨다. 
<br/>

값이 변경된 `id` 만 <code style="color: #eb5657;">onBindViewHolder()</code> 를 호출하고, 호출된 아이템의 `id` 가 이전 포지션의 아이템에 이미 존재할 시 <code style="color: #eb5657;">onBindViewHolder()</code> 함수를 호출하지 않고 이전에 같은 `id` 를 가진 뷰를 대신 보여준다.
<br/>

```kotlin
class RecyclerViewAdapter : RecyclerView.Adapter<...>() { 
    
    init {
        setHasStableIds(true)
    }

    /* ... */

    override fun getItemId(position: Int): Long =
        position.toLong() // or data id
}
```
<br/>

### setItemViewCacheSize()
<code style="color: #eb5657;">ViewHolder</code> 를 캐시에 저장해두면 스크롤 시 화면에서 `UI` 사려지고 다시 화면에 보여질 때 <code style="color: #eb5657;">onBindViewHolder()</code> 호출없이 보여지게 된다.
<br/>

<code style="color: #eb5657;">setItemViewCacheSize()</code> 를 설정하면 스크롤 시 화면에 보이던 뷰가 사라질 때 사라진 뷰를 재활용하는 <code style="color: #eb5657;">RecyclerViewPool</code> 에 들어가지 않고 캐시에 저장할 수 있다.
<br/>

```kotlin
private fun setAdapter() = with(binding.recyclerView) {
    recyclerViewAdapter = RecyclerViewAdapter()
    setItemViewCacheSize(10)
    adapter = recyclerViewAdapter
}
```
<br/>

### setHasFixedSize()
<code style="color: #eb5657;">RecyclerView</code> 는 데이터가 수정될 때 각각 아이템의 레이아웃을 다시 계산한다. 
<br/>

기본값인 <code style="color: #eb5657;">setHasFixedSize(false)</code> 로 설정되면 <code style="color: #eb5657;">requestLayout()</code> 이 호출되어 아이템의 레이아웃을 다시 계산한다.
<br/>

<code style="color: #eb5657;">setHasFixedSize(true)</code> 로 설정하면 고정값으로 인식하기 떄문에 아이템의 `UI` 사이즈가 동일하다면 해당 설정을 하는 것이 성능 향상에 도움이 된다.
<br/>

```kotlin
private fun setAdapter() = with(binding.recyclerView) {
    recyclerViewAdapter = RecyclerViewAdapter()
    setHasFixedSize(true)
    adapter = recyclerViewAdapter
}
```
<br/>

### setRecycledViewPool()
<code style="color: #eb5657;">RecycledViewPool</code> 은 여러 <code style="color: #eb5657;">RecyclerView</code> 사이에서 <code style="color: #eb5657;">ViewHolder</code> 를 공유하는 역할을 한다.
<br/>

<code style="color: #eb5657;">setRecycledViewPool()</code> 설정하여 동일한 `ViewType` 을 공유하는 여러 <code style="color: #eb5657;">RecyclerView</code> 에서 <code style="color: #eb5657;">ViewHolder</code> 를 재사용할 수 있다.
<br/>

<code style="color: #eb5657;">ViewHolder</code> 객체를 캐시하고 재활용하여 메모리 및 성능을 최적화할 수 있게 된다.
<br/>

```kotlin
class RecyclerViewAdapter() : RecyclerView.Adapter<...>() {
    private val pool = RecyclerView.RecycledViewPool()
    val childAdapter1 = ChildAdapter()
    val childAdapter2 = ChildAdapter()

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ChildViewHolder =
        ChildViewHolder(
            ItemsChildBinding.inflate(LayoutInflater.from(parent.context), parent, false),
            pool
        )

    /* ... */
}

class ChildViewHolder(
    val binding: ItemsChildBinding,
    pool: RecyclerView.RecycledViewPool
) : RecyclerView.ViewHolder(binding.root) {

    init {
        val linearLayoutManager = LinearLayoutManager(itemView.context).apply {
            // 부모 RecyclerView 가 자식 RecyclerView 를 분리할 떄 뷰를 재활용할지에 대한 여부
            recycleChildrenOnDetach = true 
            orientation = LinearLayoutManager.HORIZONTAL
        }
        binding.recyclerView.layoutManager = linearLayoutManager
        binding.recyclerView.setRecycledViewPool(pool)
    }
}
```
<br/>
<br/>



## ViewHolder 생성 과정
***
스크롤이 되면 <code style="color: #eb5657;">RecyclerView</code> 는 새로운 아이템 뷰를 보여줘야 한다고 `LayoutManager` 에게 알리며, `LayoutManager` 는 스크롤이 된 어떤 포지션에 아이템 뷰를 위치해야 하는지 계산하고, 이 위치에 구성할 아이템 뷰를 달라고 <code style="color: #eb5657;">RecyclerView</code> 에 요청한다.
<br/>

<code style="color: #eb5657;">RecyclerView</code> 는 <code style="color: #eb5657;">ViewHolder</code> 가 캐시에 저장되어 있다면, 해당 <code style="color: #eb5657;">ViewHolder</code> 를 `LayoutManager` 에게 반환해준다.
<br/>

<code style="color: #eb5657;">ViewHolder</code> 가 캐시에 저장되어 있지 않다면, `Adapter` 에게 해당 아이템 뷰의 `ViewType` 을 요청하고 받아온다.
<br/>

<code style="color: #eb5657;">RecyclerView</code> 는 `ViewType` 에 대한 <code style="color: #eb5657;">ViewHolder</code> 가 <code style="color: #eb5657;">RecyclerViewPool</code> 에 있는지 확인한다.
<br/>

<code style="color: #eb5657;">ViewHolder</code> 가 <code style="color: #eb5657;">RecyclerViewPool</code> 에 있다면, <code style="color: #eb5657;">RecyclerViewPool</code> 에서 <code style="color: #eb5657;">ViewHolder</code> 를 반환하고, `Adapter` 에게 데이터를 바인딩하라고 요청하고, `Adapter`는 아이템 뷰를 `LayoutManager` 에게 전달한다.

<code style="color: #eb5657;">ViewHolder</code> 가 <code style="color: #eb5657;">RecyclerViewPool</code> 에 없다면, `Adapter` 에게 <code style="color: #eb5657;">ViewHolder</code> 의 생성을 요청하고, `Adapter` 는 뷰를 생성하고, 데이터를 바인딩하고, 아이템 뷰를 `LayoutManager` 에게 전달한다.
<br/>

`LayoutManager` 는 포지션에 해당하는 아이템 뷰를 배치한 후 <code style="color: #eb5657;">RecyclerView</code> 에게 알려주고, <code style="color: #eb5657;">RecyclerView</code> 는 `Adapter` 에게 아이템 뷰가 붙었다고 전달한다.







