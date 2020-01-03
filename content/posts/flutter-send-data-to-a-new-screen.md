---
title: Flutter로 개발하기(06) - 새 화면에 데이터 넘기기
date: 2019-03-16 07:51:41
tags: [flutter]
---

# Send data to a new screen
https://flutter.dev/docs/cookbook/navigation/passing-data

기억하세요. 플러터에서 화면은 단지 위젯일 뿐입니다. 이 예제에서 우리는 Todo 목록을 만들고 Todo를 tap 했을 때 Todo를 보여줄 새 화면(위젯)으로 이동할 겁니다. 

## 1. Todo 클래스 정의하기
```dart
class Todo {
  final String title;
  final String description;

  Todo(this.title, this.description);
}
```

## 2. Todo 목록 만들기
```dart
final todos = List<Todo>.generate(
  20,
  (i) => Todo(
        'Todo $i',
        'A description of what needs to be done for Todo $i',
      ),
);

------------------------------------------------------------

ListView.builder(
  itemCount: todos.length,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text(todos[index].title),
    );
  },
);
```

## 3. Todo를 보여줄 상세화면 만들기
```dart
class DetailScreen extends StatelessWidget {
  final Todo todo;

  DetailScreen({Key key, @required this.todo}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(todo.title),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Text(todo.description),
      ),
    );
  }
}
```

## 4. 상세화면으로 이동하면서 데이터도 같이 넘기기
```dart
ListView.builder(
  itemCount: todos.length,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text(todos[index].title),
      onTap: () {
        Navigator.push(
          context,
          MaterialPageRoute(
            builder: (context) => DetailScreen(todo: todos[index]),
          ),
        );
      },
    );
  },
);
```

## 완전한 예제 코드
```dart
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

class Todo {
  final String title;
  final String description;

  Todo(this.title, this.description);
}

void main() {
  runApp(MaterialApp(
    title: 'Passing Data',
    home: TodosScreen(
      todos: List.generate(
        20,
        (i) => Todo(
              'Todo $i',
              'A description of what needs to be done for Todo $i',
            ),
      ),
    ),
  ));
}

class TodosScreen extends StatelessWidget {
  final List<Todo> todos;

  TodosScreen({Key key, @required this.todos}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Todos'),
      ),
      body: ListView.builder(
        itemCount: todos.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(todos[index].title),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => DetailScreen(todo: todos[index]),
                ),
              );
            },
          );
        },
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  final Todo todo;

  DetailScreen({Key key, @required this.todo}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(todo.title),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Text(todo.description),
      ),
    );
  }
}
```

![passing-data.gif](https://flutter.dev/images/cookbook/passing-data.gif)