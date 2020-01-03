---
title: 네트워크 이미지를 효율적으로 사용하기 
date: 2019-10-06 00:00:00
tags: [android]
---

## 네트워크 이미지를 효율적으로 사용하기

### 리사이징 방법

**1. 서버로부터 리사이징된 이미지를 다운로드 받는다.**

**예)** [https://image.ohou.se/image/central_crop/bucketplace-v2-development/xxxxxxx.jpg/{가로px}/{세로px}](https://image.ohou.se/image/central_crop/bucketplace-v2-development/xxxxxxx.jpg/%7B%EA%B0%80%EB%A1%9Cpx%7D/%7B%EC%84%B8%EB%A1%9Cpx%7D)

**장점 :** 네트워크 통신리소스 감소

**2. 내려받은 이미지를 ImageView에 넣을 때 Bitmap 사이즈를 리사이징 한다.**

**예)** Glide.override()

**장점 :** 비트맵용 앱메모리 감소

**override()를 호출하지 않아도 되는 경우**

내려받은 이미지가 ImageView로 들어갈 때 Gide가 Bitmap 사이즈를 ImageView의 사이즈 만큼 자동으로 맞춰줌!

**override()를 호출해야 하는 경우**

ImageView의 가로세로 사이즈가 모두 WrapContent인 경우엔 자동으로 맞춰주지 않음!