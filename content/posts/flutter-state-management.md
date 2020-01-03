---
title: Flutter로 개발하기(16) - 상태 관리
date: 2019-03-17 21:51:41
tags: [flutter]
---

## 상태 관리
원문: https://flutter.dev/docs/development/data-and-backend/state-mgmt/intro

![state-mgmt.gif](https://flutter.dev/development/data-and-backend/state-mgmt/state-management-explainer-5495afe6c3d6162f145107fe45794583bc4f2b55be377c76a92ab210be74c033.gif)

## 선언적으로 생각하기
원문: https://flutter.dev/docs/development/data-and-backend/state-mgmt/declarative

플러터는 안드로이드 SDK, iOS UIKit과 달리 선언적입니다. 선언적이라는 뜻은 플러터가 알아서 현재 상태에 맞는 UI를 빌드한다는 뜻입니다.

![declarative.png](https://flutter.dev/development/data-and-backend/state-mgmt/ui-equals-function-of-state-54b01b000694caf9da439bd3f774ef22b00e92a62d3b2ade4f2e95c8555b8ca7.png)

사용자가 액션을 취했을 때 우리가 상태를 바꾸고 UI를 갱신시키는 것(like widget.setText())은 선언적이지 않습니다. 플러터에서는 우리가 상태를 바꾸기만 하면 UI는 알아서 리빌드 됩니다.

## 임시상태와 앱상태 구분하기
원문: https://flutter.dev/docs/development/data-and-backend/state-mgmt/ephemeral-vs-app

### 임시 상태
임시 상태는 위젯 안에 담을 수 있는 상태를 말합니다. 따라서 UI 상태, 지역 상태라도 불립니다. 

임시 상태의 몇 가지 예시
- PageView안에 있는 현재 페이지
- 복잡한 애니메이션의 현재 진행률
- BottomNavigationBar의 현재 선택된 탭

이러한 종류의 상태는 위젯 안에서만 주로 사용되기 때문에 상태 관리 기술(ScopedModel, Redux 등)을 쓸 정도로 복잡하지 않습니다. 이런 경우 우리가 필요한 건 StatefulWidget 뿐입니다.

아래 예제는 BottomNavigationBar의 현재 선택된 아이템을 어떻게 다루는지 보여줍니다.
```dart
class MyHomepage extends StatefulWidget {
  @override
  _MyHomepageState createState() => _MyHomepageState();
}

class _MyHomepageState extends State<MyHomepage> {
  int _index = 0; // _index가 UI 상태입니다.

  @override
  Widget build(BuildContext context) {
    return BottomNavigationBar(
      currentIndex: _index,
      onTap: (newIndex) {
        setState(() {
          _index = newIndex;
        });
      },
      // ... items ...
    );
  }
}
```

StatefulWidget의 State 클래스에서 setState()를 사용하는 것은 완전히 자연스럽습니다. _index는 _MyHomepageState 위젯 안에서만 다뤄지며, 그 외에서는 접근할 일이 없습니다. 앱이 재시작됐을 때 _index이 0으로 초기화된다 해도 아무런 문제가 없습니다.

### 앱 상태
앱 상태는 임시적이지 않습니다. 앱의 여러 곳에서 공유되어야 하거나 유저 세션 안에서 유지되어야 하는 상태가 앱 상태입니다.

앱 상태의 몇 가지 예시
- 유저 설정
- 로그인 정보
- SNS앱에서 알림들
- 이커머스앱의 장바구니
- 뉴스앱에서 기사들의 읽음/안 읽음 상태

### 명확한 규칙은 없습니다.
명확하게 모든 곳에서 State와 setState()만을 사용할 수도 있습니다.

반면에 BottomNavigationBar의 현재 선택된 탭을 세션마다 유지되도록 외부의 클래스에서 바꾸고 싶을 수도 있습니다. 이런 경우 _index는 앱 상태입니다.

명확한 규칙은 없지만 아마도 앱이 성장함에 따라 임시 상태들을 앱 상태로 바꾸어야 하는 필요가 생길 수도 있습니다.

다음 다이어그램은 상태의 유형을 구분하는 예입니다. 
![rule.png](https://flutter.dev/development/data-and-backend/state-mgmt/ephemeral-vs-app-state-3137024aa509b4df5d20ed7ed30fb8a0f7cff54ebc8ab0d6e39794bced87e27c.png)    