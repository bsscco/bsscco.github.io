---
title: Flutter로 개발하기(10) - 애니메이션 소개
date: 2019-03-17 00:51:41
tags: [flutter]
---

# Introduction to animations
https://flutter.dev/docs/development/ui/animations

플러터 애니메이션 프레임워크를 배울 수 있는 리소스는 다음과 같습니다.
- [애니메이션 튜토리얼](https://flutter.dev/docs/development/ui/animations/tutorial)
플러터 애니메이션 패키지에 있는 기본 클래스들(controllers, Animatable, curves, listeners, builders)을 설명합니다. 

- [플러터로 0에서 1까지 part1](https://medium.com/dartlang/zero-to-one-with-flutter-43b13fd7b354), [part2](https://medium.com/dartlang/zero-to-one-with-flutter-part-two-5aa2f06655cb)
어떻게 움직이는 표를 만드는지 보여주는 미디엄 아티클입니다.

- [플러터로 아름다운 UI 만들기](https://codelabs.developers.google.com/codelabs/flutter)
간단한 채팅앱을 만드는 코드랩입니다. 스탭7에서 메시지를 추가할 때 애니메이션을 사용합니다.

## 애니메이션 유형
### 트윈 애니메이션
트윈 애니메이션에는 시작점과 끝점이 있고, 그 사이에 속도가 변화되는 커브를 지정할 수 있습니다.  

### 물리기반 애니메이션
물리기반 애니메이션에서 이동궤적은 현실세계와 같게 적용됩니다. 

## 일반적인 애니메이션 패턴
### 움직이는 리스트와 그리드
이 패턴은 리스트와 그리드에서 항목이 추가, 삭제되는 과정에 애니메이션을 추가하는 것과 연관돼있습니다.
- [움직이는 리스트 예제](https://flutter.dev/docs/catalog/samples/animated-list)

### 공유 요소 전환
이 패턴은 사용자가 어떤 페이지에서 요소를 클릭하고, 그 요소에 대한 상세 페이지가 열릴 때 사용되는 전환 애니메이션과 연관돼있습니다.
- [히어로 애니메이션](https://flutter.dev/docs/development/ui/animations/hero-animations)
- [플러터 갤러리](https://github.com/flutter/flutter/tree/master/examples/flutter_gallery)

### 엇갈리는 애니메이션
이 패턴은 작은 모션들을 조합하는 것과 연관돼있습니다.
- [엇갈리는 애니메이션](https://flutter.dev/docs/development/ui/animations/staggered-animations)

## 기타 리소스
- [애니메이션 기술 개요](https://flutter.dev/docs/development/ui/animations/overview.html)
- [애니메이션과 모션 위젯](https://flutter.dev/docs/development/ui/widgets/animation) 