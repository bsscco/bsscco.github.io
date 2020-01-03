---
title: Udacity Developing Android Apps with Kotlin 레슨4 Activity & Fragment Lifecycle 요약
date: 2019-06-05 00:00:01
tags: [android, kotlin]
---

# Lesson 4: Activity & Fragment Lifecycle

## Activity lifecycle

![](/2019-06-05/Untitled-3047bc6f-d8a6-4d9e-972d-6b8bacc93304.png)

네모:state, 네모없는 텍스트: callback

액티비티가 생성(객체 생성)됐으면 initialized

액티비티가 보이면 started 상태 또는 resumed 상태

액티비티에 포커스가 있으면 resumed 상태

액티비티가 안 보이면 created

액티비티가 파괴됐으면 destroyed

 

## Fragment lifecycle

onCreate에서 레이아웃을 inflate 하지 말고 onCreateView에서 inflate 하기

프래그먼트가 처음 그려질 때 activity를 참조하지 말고 onActivityCreated에서 참조하기

onAttach : 프래그먼트가 액티비티에 붙었을 때 딱 한 번만 호출됨

onActivityCreated : 액티비티의 onCreate가 반환된 직후에 호출됨. 액티비티가 이미 created 상태라도 호출됨.

onViewCreated에서 rootview를 써야 한다.

onStop : 액티비티의 onStop이 호출될 때 호출됨.

onDestroyView : 액티비티와 달리 off screen 될 때 호출됨.

onDestroy : 액티비티의 onDestroy가 호출될 때 호출됨.

[프래그먼트 라이프사이클 medium 글 링크](https://medium.com/androiddevelopers/the-android-lifecycle-cheat-sheet-part-iii-fragments-afc87d4f37fd)

[ViewModel 라이프사이클 medium 글 링크](https://medium.com/androiddevelopers/the-android-lifecycle-cheat-sheet-part-iv-49946659b094)

## Lifecycle 라이브러리

![](/2019-06-05/Untitled-738fed7c-374a-435d-905b-8e79e0119d2e.png)

어느 곳에서든 라이프사이클 상태를 확인할 수 있다.

액티비티와 프래그먼트는 lifecycleOwner 인터페이스가 구현돼있다.

Lifecycle 라이브러리는 Observer 패턴으로 만들어졌다. 라이프사이클 Observer를 상속하면 lifecycle owner를 구독할 수 있다.

![](/2019-06-05/Untitled-4ded38c1-83ed-499c-9a01-5c7f6a6a5c79.png)

라이프사이클 오너의 lifecycle 객체에 Observer 자신을 등록하고, Annotate를 사용해 라이프사이클 콜백을 선언한다.

## Timber 로깅 라이브러리

![](/2019-06-05/Untitled-1755e63d-f31f-4573-98d3-f498ec13c37f.png)

로그에 자동으로 태그 생성!

![](/2019-06-05/Untitled-b5d44dd6-21d2-4cfe-9e07-54858820b314.png)

## 궁금한 것

라이프사이클 옵저버를 라이프사이클 객체에 등록시킨 후 따로 등록해제해주지 않아도 되는 이유는? 라이프사이클 객체가 라이프사이클이 종료되는 시점에 알아서 해제시킬 수 있기 때문일까?