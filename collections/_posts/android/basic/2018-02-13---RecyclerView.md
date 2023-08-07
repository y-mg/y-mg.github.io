---
layout: post
title: "RecyclerView"
date: 2018-02-13T00:00:00Z
authors: ["y-mg"]
categories: [Android]
description: "뷰를 재활용하고 데이터를 리스트화해서 보여주는 컨테이너"
thumbnail: "/assets/images/android/banner/basic.png"
image: "/assets/images/android/banner/basic.png"
comments: false
---

## RecyclerVIew
***
뷰를 재활용하고 데이터를 리스트화해서 보여주는 컨테이너이다.
<br/>

<code style="color: #eb5657;">ListView</code> 는 스크롤 시 화면에 보이는만큼 뷰를 생성되고 보이지 않으면 삭제하길 반복해서 리소스 낭비가 심하다는 단점이 있다.
<br/>

이를 보완하기 위해 나온 <code style="color: #eb5657;">RecyclerView</code> 에서는 화면에 보이는 정도의 뷰 객체를 생성하고, 스크롤 시 사라지는 뷰를 삭제하는 것이 아니라 새로 그려져야할 뷰가 있는 위치로 <code style="color: #eb5657;">ViewHolder</code> 를 이동시켜 재사용한다.
<br/>

<code style="color: #eb5657;">ViewHolder</code> 는 화면에 표시될 아이템 뷰를 저장하는 객체로, 화면에 보이는 만큼 생성되고 새로 그려져야할 뷰가 있다면 가장 위에 생성된 <code style="color: #eb5657;">ViewHolder</code> 를 재사용해서 데이터만 바꾼다.
<br/>
<br/>
<br/>



## RecyclerView.Adapter
***
데이터를 <code style="color: #eb5657;">RecyclerView</code> 에 보여주기 위한 클래스이다.
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
- <code style="color: #eb5657;">onCreateViewHolder()</code> 는 <code style="color: #eb5657;">ViewHolder</code> 가 생성되는 함수이다.
- <code style="color: #eb5657;">onBindViewHolder()</code> 는 생성된 <code style="color: #eb5657;">ViewHolder</code> 에 데이터를 바인딩 해주는 함수이다.
- <code style="color: #eb5657;">getItemCount()</code> 데이터의 총 개수를 반환하는 함수이다.