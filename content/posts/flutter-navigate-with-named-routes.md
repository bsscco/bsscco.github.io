---
title: Flutter로 개발하기(08) - named route를 통해 화면 관리하기
date: 2019-03-16 18:51:41
tags: [flutter]
---

# Navigate with named routes
https://flutter.dev/docs/cookbook/navigation/named-routes

만약 여러 곳에서 같은 화면으로 navigate 해야 할 때 매번 Route 코드를 추가해야 하는 중복이 발생할 수 있습니다. 이 문제를 해결해주는 게 "named route"입니다. named route로 navigate를 하려면 Navigator.pushNamed()를 이용해야 합니다.

## 1. 두 화면 만들기
```dart
class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('첫 번째 화면'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('새 화면 실행하기'),
          onPressed: () {
          },
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("두 번째 화면"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
          },
          child: Text('돌아가기'),
        ),
      ),
    );
  }
}
```

## 2. Route들 정의하기
MaterialApp 위젯의 속성들 중 initialRoute 속성은 앱의 시작 Route를 설정하게 해줍니다. routes 속성은 앱에서 사용가능한 Route들을 설정하게 해줍니다.
```dart
MaterialApp(
  // 앱은 '/' named route에서 시작됩니다. 이 예제에서는 FirstScreen이 '/'가 될 것입니다.
  initialRoute: '/',
  routes: {
    // '/'와 FirstScreen을 매치시킵니다.
    '/': (context) => FirstScreen(),
    // '/second'와 SecondScreen을 매치시킵니다.
    '/second': (context) => SecondScreen(),
  },
);
```
initialRoute 속성을 사용할 때는 home 속성을 사용하지 말아야 합니다.

## 3. 두 번째 화면으로 이동하기
```dart
// in FirstScreen
onPressed: () {
  Navigator.pushNamed(context, '/second');
}
```

## 4. 첫 번째 화면으로 돌아오기
```dart
// in SecondScreen
onPressed: () {
  Navigator.pop(context);
}
```

## 완전한 예제 코드
```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Named Routes Demo',
    initialRoute: '/',
    routes: {
      '/': (context) => FirstScreen(),
      '/second': (context) => SecondScreen(),
    },
  ));
}

class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Screen'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('Launch screen'),
          onPressed: () {
            Navigator.pushNamed(context, '/second');
          },
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Screen"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            Navigator.pop(context);
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```

![navigation-basics.gif](https://flutter.dev/images/cookbook/navigation-basics.gif)