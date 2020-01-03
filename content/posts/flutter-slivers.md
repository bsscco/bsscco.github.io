---
title: Flutter로 개발하기(15) - Sliver와 제스처
date: 2019-03-17 17:51:41
tags: [flutter]
---

## Slivers
원문: https://flutter.dev/docs/development/ui/advanced/slivers

Sliver는 스크롤 영역의 일부입니다. 맞춤 스크롤 효과를 만들기 위해 Sliver를 사용할 수 있습니다.

플러터에서 Sliver 구현 예로 [SliverList](https://docs.flutter.io/flutter/widgets/SliverList-class.html), [SliverGrid](https://docs.flutter.io/flutter/widgets/SliverGrid-class.html), [SliverAppBar](https://docs.flutter.io/flutter/material/SliverAppBar-class.html)가 있습니다. [Slivers, Demystified](https://medium.com/flutter-io/slivers-demystified-6ff68ab0296f), [Flutter Publication](https://medium.com/flutter-io)도 참고하세요. 


## 제스처
원문: https://flutter.dev/docs/development/ui/advanced/gestures

플러터의 제스처 체계는 2가지 층으로 나뉩니다. 첫 번째 층은 raw 포인터 이벤트이고, 두 번째 층은 제스처입니다.

### 포인터
포인터 이벤트의 4가지 유형
- PointerDownEvent
- PointerMoveEvent
- PointerUpEvent
- PointerCancelEvent

포인터가 down 됐을 때 프레임워크는 화면에서 해당하는 위치에 위젯이 있는지 결정하기 위해 hit test를 합니다. hit test에서 찾아진 위젯이 있다면 down 이벤트는 트리상에서 가장 앞에 있는 위젯으로 전달됩니다. 포인터 이벤트를 위젯으로부터 직접 감지하려면 Listener 위젯을 사용하면 됩니다. 하지만 일반적으로 포인터 감지보다는 제스처를 감지를 사용합니다.

### 제스처
제스처는 의미적 행동들을 대표합니다. 
- 누르기
    - onTapDown
    - onTapUp
    - onTap
    - onTapCancel
- 두 번 누르기
    - onDoubleTap
- 길게 누르기(long press)
    - onLongPress
- 수직으로 끌기
    - onVerticalDragStart
    - onVerticalDragUpdate
    - onVerticalDragEnd
- 수평으로 끌기
    - onHorizontalDragStart
    - onHorizontalDragUpdate
    - onHorizontalDragEnd
- 휘두르기
    - onPanStart: 만약 dragXXXXStart가 설정돼있다면 이 콜백과 충돌이 일어납니다.
    - onPanUpdate: 만약 dragXXXXUpdate가 설정돼있다면 이 콜백과 충돌이 일어납니다.
    - onPanEnd: 만약 dragXXXXEnd가 설정돼있다면 이 콜백과 충돌이 일어납니다.

제스터를 위젯으로부터 감지하려면 GestureDetector 위젯을 사용하면 됩니다.

#### 중첩된 제스처들 사이에서 처리 순서
만약 수평드래그제스처와 수직드래그제스처가 같은 위치에 있다면, 어느 방향으로 일정한 픽셀만큼 이동했는지에 따라 어떤 방향의 드래그인지 결정됩니다. 하나의 제스처만 있다면 어떤 제스처인지 바로 결정되기 때문에 이벤트를 인식하는 데 더 효과적입니다.  
