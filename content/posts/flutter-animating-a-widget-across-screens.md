---
title: Flutter로 개발하기(09) - 화면 전환에 애니메이션 넣기
date: 2019-03-16 22:51:41
tags: [flutter]
---

# Animating a Widget across screens
https://flutter.dev/docs/cookbook/navigation/hero-animations

현재 화면에서 다른 화면을 열 때 사용자에게 연속성을 주어야 하는 경우가 종종 있습니다. 연속성을 구현하기 위한 일반적인 기술은 현재 화면의 위젯이 다음 화면으로 이동하는 애니메이션을 적용하는 것입니다. 두 화면 간에 시각적 요소를 연결하려면 Hero 위젯을 사용하면 됩니다.   

## 같은 이미지를 보여주는 2개의 화면 만들기
```dart
class MainScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Main Screen'),
      ),
      body: GestureDetector(
        onTap: () {
          Navigator.push(context, MaterialPageRoute(builder: (_) {
            return DetailScreen();
          }));
        },
        child: Image.network(
          'https://picsum.photos/250?image=9',
        ),
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GestureDetector(
        onTap: () {
          Navigator.pop(context);
        },
        child: Center(
          child: Image.network(
            'https://picsum.photos/250?image=9',
          ),
        ),
      ),
    );
  }
}
```

## 첫 번째 화면에 Hero 위젯 추가하기
```dart
Hero(
  tag: 'imageHero',
  child: Image.network(
    'https://picsum.photos/250?image=9',
  ),
);
```

## 두 번째 화면에 Hero 위젯 추가하기
두 화면 사이의 tag 속성값은 동일해야 합니다.
```dart
Hero(
  tag: 'imageHero',
  child: Image.network(
    'https://picsum.photos/250?image=9',
  ),
);
```

## 완전한 예제 코드
```dart
import 'package:flutter/material.dart';

void main() => runApp(HeroApp());

class HeroApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Transition Demo',
      home: MainScreen(),
    );
  }
}

class MainScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Main Screen'),
      ),
      body: GestureDetector(
        child: Hero(
          tag: 'imageHero',
          child: Image.network(
            'https://picsum.photos/250?image=9',
          ),
        ),
        onTap: () {
          Navigator.push(context, MaterialPageRoute(builder: (_) {
            return DetailScreen();
          }));
        },
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GestureDetector(
        child: Center(
          child: Hero(
            tag: 'imageHero',
            child: Image.network(
              'https://picsum.photos/250?image=9',
            ),
          ),
        ),
        onTap: () {
          Navigator.pop(context);
        },
      ),
    );
  }
}
```

![hero.gif](https://flutter.dev/images/cookbook/hero.gif)