---
title: Flutter로 개발하기(07) - 닫히는 화면으로부터 데이터 전달 받기
date: 2019-03-16 17:51:41
tags: [flutter]
---

# Return data from a screen
https://flutter.dev/docs/cookbook/navigation/returning-data

## 1. 홈 화면 정의하기
```dart
class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Returning Data Demo'),
      ),
      // 다음 단계에서 SelectionButton 위젯을 만들 겁니다.
      body: Center(child: SelectionButton()),
    );
  }
}
```

## 2. 선택 화면을 실행하는 버튼 추가하기
```dart
class SelectionButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: Text('옵션을 선택하세요!'),
    );
  }

  _navigateAndDisplaySelection(BuildContext context) async {
    // Navigator.push() 는 Future를 반환합니다. 선택 화면이 pop 되면, Future에 완전한 값이 담길 겁니다.
    final result = await Navigator.push(
      context,
      // 다음 단계에서 SelectionScreen 위젯을 만들 겁니다.
      MaterialPageRoute(builder: (context) => SelectionScreen()),
    );
  }
}
```

## 3. 2개의 버튼을 가지는 선택 화면 띄우기
```dart
class SelectionScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('옵션을 선택하세요.'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {},
                child: Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {},
                child: Text('Nope.'),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```

## 4. 버튼을 tap 했을 때 선택 화면 닫기
```dart
child: RaisedButton(
  onPressed: () {
    Navigator.pop(context, 'Yep!');
  },
  child: Text('Yep!'),
),

--------------------------------------------------

child: RaisedButton(
  onPressed: () {
    Navigator.pop(context, 'Nope.');
  },
  child: Text('Nope.'),
),
              
              
```

## 5. 홈 화면으로 돌아왔을 때 SnackBar 보여주기
```dart
_navigateAndDisplaySelection(BuildContext context) async {
    final result = await Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => SelectionScreen()),
    );

    // 선택 화면의 값이 반환된 후에 이전 스낵바를 숨기고 새로운 값을 보여줍니다.
    Scaffold.of(context)
      ..removeCurrentSnackBar()
      ..showSnackBar(SnackBar(content: Text("$result")));
  }
```

## 완전한 예제 코드
```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Returning Data',
    home: HomeScreen(),
  ));
}

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Returning Data Demo'),
      ),
      body: Center(child: SelectionButton()),
    );
  }
}

class SelectionButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: Text('Pick an option, any option!'),
    );
  }

  _navigateAndDisplaySelection(BuildContext context) async {
    final result = await Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => SelectionScreen()),
    );

    Scaffold.of(context)
      ..removeCurrentSnackBar()
      ..showSnackBar(SnackBar(content: Text("$result")));
  }
}

class SelectionScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Pick an option'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  Navigator.pop(context, 'Yep!');
                },
                child: Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  Navigator.pop(context, 'Nope.');
                },
                child: Text('Nope.'),
              ),
            )
          ],
        ),
      ),
    );

```

![returning-data.gif](https://flutter.dev/images/cookbook/returning-data.gif)