---
title: Flutter로 개발하기(23) - Bloc 튜토리얼
date: 2019-03-25 09:51:41
tags: [flutter, bloc pattern]
---

# Bloc document - 튜토리얼
원문: https://felangel.github.io/bloc

## 플러터
### Counter
![flutter_counter.gif](https://felangel.github.io/bloc/gifs/flutter_counter.gif)

#### Setup
터미널에서
```
flutter create flutter_counter
```

pubspec.yaml에서
```yaml
name: flutter_counter
description: A new Flutter project.
version: 1.0.0+1

environment:
  sdk: ">=2.0.0-dev.68.0 <3.0.0"

dependencies:
  flutter:
    sdk: flutter
  bloc: ^0.10.0
  flutter_bloc: ^0.8.0
  meta: ^1.1.6

dev_dependencies:
  flutter_test:
    sdk: flutter

flutter:
  uses-material-design: true
```

다시 터미널에서
```
flutter packages get
```

#### Counter Events
```dart
enum CounterEvent { increment, decrement }
```

#### Counter Bloc(Counter States 포함)
```dart
class CounterBloc extends Bloc<CounterEvent, int> {
  @override
  int get initialState => 0; // 초기 상태 생성

  @override
  Stream<int> mapEventToState(int currentState, CounterEvent event) async* {
    // 이벤트 처리
    switch (event) {
      case CounterEvent.decrement:
        yield currentState - 1;
        break;
      case CounterEvent.increment:
        yield currentState + 1;
        break;
    }
  }
}
```

#### Counter App
```dart
void main() => runApp(MyApp());

// CounterBloc을 생성하고 dispose할 수 있도록 StatefulWidget으로 선언합니다.
class MyApp extends StatefulWidget {
  @override
  State<StatefulWidget> createState() => MyAppState();
}

class MyAppState extends State<MyApp> {
  final CounterBloc _counterBloc = CounterBloc();

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      // CounterPage의 위젯트리에서 CounterBloc 인스턴스를 사용할 수 있도록 CounterPage를 BlocProvider로 감쌉니다.
      home: BlocProvider<CounterBloc>(
        bloc: _counterBloc,
        child: CounterPage(),
      ),
    );
  }

  @override
  void dispose() {
    _counterBloc.dispose();
    super.dispose();
  }
}
```

#### Counter Page
```dart
class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // BlocProvider.of<CounterBloc>(context)를 사용해서 CounterBloc 인스턴스 접근합니다.
    final CounterBloc _counterBloc = BlocProvider.of<CounterBloc>(context);

    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      // 바뀐 상태를 기반으로 리빌드 하기 위해서 BlocBuilder로 감쌉니다.
      body: BlocBuilder<CounterEvent, int>(
        bloc: _counterBloc,
        builder: (BuildContext context, int count) {
          return Center(
            child: Text(
              '$count',
              style: TextStyle(fontSize: 24.0),
            ),
          );
        },
      ),
      floatingActionButton: Column(
        crossAxisAlignment: CrossAxisAlignment.end,
        mainAxisAlignment: MainAxisAlignment.end,
        children: <Widget>[
          Padding(
            padding: EdgeInsets.symmetric(vertical: 5.0),
            child: FloatingActionButton(
              child: Icon(Icons.add),
              onPressed: () {
                _counterBloc.dispatch(CounterEvent.increment);
              },
            ),
          ),
          Padding(
            padding: EdgeInsets.symmetric(vertical: 5.0),
            child: FloatingActionButton(
              child: Icon(Icons.remove),
              onPressed: () {
                _counterBloc.dispatch(CounterEvent.decrement);
              },
            ),
          ),
        ],
      ),
    );
  }
}
```

### Infinite List
![flutter_infinite_list.gif](https://felangel.github.io/bloc/gifs/flutter_infinite_list.gif)

원문: https://felangel.github.io/bloc/#/flutterinfinitelisttutorial?id=flutter-infinite-list-tutorial

### Login
![flutter_login.gif](https://felangel.github.io/bloc/gifs/flutter_login.gif)

원문: https://felangel.github.io/bloc/#/flutterlogintutorial?id=flutter-login-tutorial

### Weather
![flutter_weather.gif](https://felangel.github.io/bloc/gifs/flutter_weather.gif)

원문: https://felangel.github.io/bloc/#/flutterweathertutorial?id=flutter-weather-tutorial

### Todos
![flutter_todos.gif](https://felangel.github.io/bloc/gifs/flutter_todos.gif)

원문: https://felangel.github.io/bloc/#/fluttertodostutorial?id=flutter-todos-tutorial

## Angular Dart
생략

## Flutter + Angular Dart
생략
