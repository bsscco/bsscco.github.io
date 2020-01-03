---
title: Udacity Developing Android Apps with Kotlin 레슨1 Build your First App 요약
date: 2019-05-30 00:00:00
tags: [android, kotlin]
---

# Lesson 1: Build your First App

- lateinit 키워드
    - findViewById()를 반복적으로 사용할 때 view hierarchy를 찾는 불필요 연산을 없애기 위해 View를 멤버변수에 홀딩시켜놓는데, 멤버변수를 val로 선언해서 null check가 필요 없도록 하려면 멤버변수 선언 앞에 lateinit 키워드를 붙여서 나중에 꼭 초기화될 것임을 알린다.
- vector drawable min sdk 21
- namespace:tools
    - 미리보기용
    - 빌드할 땐 없어져서 개발하기 편함