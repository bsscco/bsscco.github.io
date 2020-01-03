---
title: Udacity Developing Android Apps with Kotlin 레슨8 Connect to the Internet 요약
date: 2019-06-19 00:00:01
tags: [android, kotlin]
---

# Lesson 8: Connect to the Internet

## 1. Greetings Earth Friends!

## 2. Introduction

![](/2019-06-19/Untitled-406f7b9d-12f6-4b7c-b824-3b9a333d4946.png)

## 3. RESTful Services

URI가 URL을 포함하는 개념

![](/2019-06-19/Untitled-4da1af00-81d2-4576-8cb7-da5773608797.png)

## 4. Libraries

## 5. App Walkthrough and Starter Code

![](/2019-06-19/Untitled-469df0d6-abe1-49a1-a1cf-be1f767c238c.png)

## 6. Exercise: Connecting to the Internet

![](/2019-06-19/Untitled-401d64ed-a3fb-427e-8b84-73b548a1ffcd.png)

## 7. Permissions

## 8. Exercise: Parsing the JSON Response

### moshi가 gson이나 jackson보다 더 좋은 이유가 있을까?

[https://medium.com/@IlyaEremin/android-json-parsers-comparison-2017-8b5221721e31](https://medium.com/@IlyaEremin/android-json-parsers-comparison-2017-8b5221721e31)

jackson이 성능에서 가장 좋은데 친숙하지 않은 API가 단점이라고 함.

moshi는 제이크 와튼이 만든 거라 함께 사용한 듯

## 9. Exercise: Coroutines and Deferred

    Deferred<List<MarsProperty>>
    
    ...
    
    private fun getMarsRealEstateProperties() {
       coroutineScope.launch {
           var getPropertiesDeferred = MarsApi.retrofitService.getProperties()
           try {
                var listResult = getPropertiesDeferred.await() // retrofit에 suspend기능의 메소드가 추가됨.
               _response.value = "Success: ${listResult.size} Mars properties retrieved"
           } catch (e: Exception) {
               _response.value = "Failure: ${e.message}"
           }
       }
    }

## 10. Googler Interview: Jake Wharton

## 11. Exercise: Display an Internet Image

## 12. Exercise: Display Images in a Grid

ViewModel의 LiveData<List<dataItem>> → layout xml의 바인딩 어댑터로 적용

## 13. Exercise: Error Handling with RecyclerView

## 14. Parcel and Parcelables

코틀린에서 @Parcelize 어노테이션이면 Parcelable 완성

## 15. Exercise: Add the Detail Screen

## 16. Summary