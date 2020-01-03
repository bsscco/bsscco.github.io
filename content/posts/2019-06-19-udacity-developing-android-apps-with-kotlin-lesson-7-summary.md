---
title: Udacity Developing Android Apps with Kotlin 레슨7 RecyclerView 요약
date: 2019-06-19 00:00:00
tags: [android, kotlin]
---

# Lesson 7: RecyclerView 

# RecyclerView

---

## 1. Recycle Woman

## 2. Introduction

## 3. Your first RecyclerView

![](/2019-06-19/Untitled-8cc83f7f-6ff5-41cc-b288-fdb2cc6fe053.png)

Room+ViewModel과 Adapter의 연결 구조

![](/2019-06-19/Untitled-2b6cd176-f853-4959-abb7-4f621a41b8d1.png)

ViewHolder의 역할

## 4. Exercise: Add a RecyclerView

## 5. Exercise: Display SleepQuality Data

프래그먼트에서 db 데이터를 observe 하다가 데이터가 갱신되면 adapter.data에 대입해주는 부분이 있음.

## 6. Exercise: Recycling ViewHolders

## 7. Displaying Sleep Quality

ViewHolder에는 많은 기능이 있다. 활용하자.

- getAdapterPosition()
- getLayoutPosition()
- getItemId()

## 8. Exercise: Display the SleepQuality List

## 9. Exercise: Refactor onBindViewHolder

onBindViewHolder 로직을 ViewHolder의 bind 메소드로 이동

## 10. Exercise: Refactor onCreateViewHolder

onCreateViewHolder 로직을 ViewHolder의 from 메소드로 이동

## 11. Improving Data Refresh

![](/2019-06-19/Untitled-f5c5f583-1fb0-4a9d-9973-a3e11c78dd89.png)

RecyclerView의 Rich API를 활용하기에 딱 좋은 DiffUtil

![](/2019-06-19/Untitled-b2e20f48-7eb5-495f-ac74-c6327f082e2f.png)

DiffUtil의 이점

# 12. Exercise: Refresh Data with DiffUtil

    class SleepNightDiffCallback : DiffUtil.ItemCallback<SleepNight>() {
      override fun areItemsTheSame(oldItem: SleepNight, newItem: SleepNight): Boolean {
          return oldItem.nightId == newItem.nightId
      }
    
      override fun areContentsTheSame(oldItem: SleepNight, newItem: SleepNight): Boolean {
          return oldItem == newItem // data 클래스이기에 비교 가능!
      }
    }
    
    ...
    
    adapter.submitList() // DiffUtil을 사용해 리스트를 갱신한다.

# Data Binding 더하기

---

## 13. Exercise: Add DataBinding to the Adapter

    fun from(parent: ViewGroup): ViewHolder {
    	val layoutInflater = LayoutInflater.from(parent.context)
    	val binding = ListItemSleepNightBinding.inflate(layoutInflater, parent, false)
    	return ViewHolder(binding)
    }

    class ViewHolder private constructor(val binding: ListItemSleepNightBinding):
    	RecyclerView.ViewHolder(binding.root) {
    }

## 14. Exercise: Add Binding Adapters

    @BindingAdapter("sleepImage")
    fun ImageView.setSleepImage(item: SleepNight) {
        setImageResource(when (item.sleepQuality) {
            0 -> R.drawable.ic_sleep_0
            1 -> R.drawable.ic_sleep_1
            2 -> R.drawable.ic_sleep_2
            3 -> R.drawable.ic_sleep_3
            4 -> R.drawable.ic_sleep_4
            5 -> R.drawable.ic_sleep_5
            else -> R.drawable.ic_sleep_active
       })
    }

코틀린 확장함수를 활용한 바인딩 메소드

    binding.sleep = item
    binding.executePendingBindings() // 바인딩 어댑터를 적용시키기

    <ImageView
      android:id="@+id/quality_image"
      android:layout_width="@dimen/icon_size"
      android:layout_height="60dp"
      android:layout_marginStart="16dp"
      android:layout_marginTop="8dp"
      android:layout_marginBottom="8dp"
      app:layout_constraintBottom_toBottomOf="parent"
      app:layout_constraintStart_toStartOf="parent"
      app:layout_constraintTop_toTopOf="parent"
      tools:srcCompat="@drawable/ic_sleep_5"
      app:sleepImage="@{sleep}"/> <!--요기-->

### 바인딩 어댑터를 썼을 때와 그냥 메소드를 썼을 때의 차이는?

바인딩 어댑터는 static 메소드라고 보면 됨.

## 15. Finishing Your First RecyclerView

![](/2019-06-19/Untitled-2e45fcab-3ed9-48ff-af99-004e7b50ff02.png)

RecyclerView를 사용하는 구조 정리

## 16. Googler Interview: Romain Guy and Chet Haase

## 17. Using GridLayout

## 18. Exercise: Change LinearLayout to GridLayout

## 19. Interacting with List Items

![](/2019-06-19/Untitled-af1f1cff-dc5d-4c83-9a2b-a49d87c3bc74.png)

## 20. Exercise: Implement a Click Listener

    <variable
         name="clickListener" type="com.example.android.trackmysleepquality.sleeptracker.SleepNightListener"/>

## 21. Exercise: Navigate on Click

## 22. Adding Headers to the RecyclerView

## 23. Extra Credit: Add a List Header

    sealed class DataItem {
         data class SleepNightItem(val sleepNight: SleepNight): DataItem() {
             override val id = sleepNight.nightId
         }
    
         object Header: DataItem() {
             override val id = Long.MIN_VALUE
         }
    
         abstract val id: Long
     }

### sealed?

enum 클래스의 확장

    fun addHeaderAndSubmitList(list: List<SleepNight>?) {
        adapterScope.launch {
            val items = when (list) {
                null -> listOf(DataItem.Header)
                else -> listOf(DataItem.Header) + list.map { DataItem.SleepNightItem(it) }
            }
            withContext(Dispatchers.Main) {
                submitList(items)
            }
        }
    }

## 24. Headers in GridLayout

## 25. Extra Credit: Add a Header to the GridLayout

## 26. Summary

## 마치면서

이 레슨에서도 Room~LiveData<List> 자동 갱신 연계가 인상적이었다. DB data Item이 추가 되면, DB data list가 갱신되는 것.

페이징 라이브러리, Room, LiveData 연계 더 정확히 알아보기

그리고 View 이벤트 리스닝의 흐름도 명확해서 좋았다. Fragment가 Adapter를 만들 때 생성자로 리스너를 넘겨주고, 리스너는 아이템뷰에 바인딩 된다. View 이벤트가 발생되면 리스너 구현부에서 ViewModel의 메소드를 호출하게 된다.