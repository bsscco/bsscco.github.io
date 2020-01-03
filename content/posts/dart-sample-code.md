---
title: Dart 샘플코드 번역
date: 2019-03-02 22:35:49
tags: [dart] 
---

# Sample Code 
https://www.dartlang.org/samples

## Hello world
모든 앱은 main() 함수에서 시작한다.
```dart
void main() {
  print('Hello, World!');
}
```

## Variables
type-safe인 Dart에서 대부분의 변수들은 타입 명시가 필요하지 않다. 타입 추론 덕분이다.
```dart
var name = 'Voyager I';
var year = 1977;
var antennaDiameter = 3.7;
var flybyObjects = ['Jupiter', 'Saturn', 'Uranus', 'Neptune'];
var image = {
  'tags': ['saturn'],
  'url': '//path/to/saturn.jpg'
};
```

## Control flow statements
```dart
if (year >= 2001) {
  print('21st century');
} else if (year >= 1901) {
  print('20th century');
}

for (var object in flybyObjects) {
  print(object);
}

for (int month = 1; month <= 12; month++) {
  print(month);
}

while (year < 2016) {
  year += 1;
}
```

## Functions
함수의 파라메터 타입과 리턴타입을 명시할 것을 추천한다.
```dart
int fibonacci(int n) {
  if (n == 0 || n == 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

var result = fibonacci(20);
```
함수가 한줄이라면 arrow syntax를 지원한다. 람다를 사용할 때 유용하다.

```dart
flybyObjects.where((name) => name.contains('turn')).forEach(print);
```

## Comments
Dart 주석은 일반적으로 `//`로 시작한다.
```dart
// 한줄 주석

/// 라이브러리, 클래스, 멤버들을 위한 doc 주석이다.
/// IDE나 dartdoc 같은 툴이
/// doc 주석을 특별하게 다룬다.

/* 여러줄 주석도 지원하지만 권장하지 않는다. */
```

## Imports
다른 라이브러리의 API에 접근할 때 `import`를 쓴다.
```dart
// 코어 라이브러리를 import
import 'dart:math';

// 외부 패키지를 import
import 'package:test/test.dart';

// 상대경로의 파일을 import
import 'path/to/my_other_file.dart';
```

## Classes
```dart
class Spacecraft {
  String name;
  DateTime launchDate;

  // 멤버 할당을 동시에 해주는 syntactic sugar
  Spacecraft(this.name, this.launchDate) {
    // Initialization code goes here.
  }

  // 생성의도를 명확히 해주는 네임드 생성자
  Spacecraft.unlaunched(String name) : this(name, null);

  int get launchYear => launchDate?.year; // getter만 정의하면 read-only가 된다.

  // 메소드
  void describe() {
    print('Spacecraft: $name');
    if (launchDate != null) {
      int years = DateTime.now().difference(launchDate).inDays ~/ 365;
      print('Launched: $launchYear ($years years ago)');
    } else {
      print('Unlaunched');
    }
  }
}
```

## Inheritance
Dart는 단일 상속만 지원한다.
```dart
class Orbiter extends Spacecraft {
  num altitude;
  Orbiter(String name, DateTime launchDate, this.altitude) : super(name, launchDate);
}
```

## Mixins
mixin은 클래스 코드를 재사용할 수 있는 방법이다. 다중 상속의 효과를 주어 단일 상속을 보완한다.
```dart
class Piloted {
  int astronauts = 1;
  void describeCrew() {
    print('Number of astronauts: $astronauts');
  }
}
```
```dart
class PilotedCraft extends Spacecraft with Piloted {
  // 이 클래스는 이제부터 Piloted의 astronauts와 descripbeCrew()를 가진다.
}
```

## Interfaces and abstract classes
Dart엔 interface 키워드가 없다. 대신에 암시적으로 모든 클래스의 인터페이스를 정의해준다.
```dart
class MockSpaceship implements Spacecraft {
  // 실제로 Spacecraft는 클래스다.
}

abstract class Describable {
  void describe(); // 추상 메소드를 가지면 추상 클래스가 된다.
}
```

## Async
async await를 통해 콜백 지옥을 피하고 가독성 있는 코드를 만들 수 있다.
```dart
const oneSecond = Duration(seconds: 1);
// ···
Future<void> printWithDelay(String message) async {
  await Future.delayed(oneSecond);
  print(message);
}
```
위 코드는 아래 코드와 같은 동작을 한다.
```dart
Future<void> printWithDelay(String message) {
  return Future.delayed(oneSecond).then((_) {
    print(message);
  });
}
```
다음 예제에서 async, await가 비동기 코드를 읽기 쉽게 만들어준다. 
```dart
Future<void> createDescriptions(Iterable<String> objects) async {
  for (var object in objects) {
    try {
      var file = File('$object.txt');
      if (await file.exists()) {
        var modified = await file.lastModified();
        print(
            'File for $object already exists. It was modified on $modified.');
        continue;
      }
      await file.create();
      await file.writeAsString('Start describing $object in this file.');
    } on IOException catch (e) {
      print('Cannot create description for $object: $e');
    }
  }
}
```
async*를 사용해서 steam을 만드는 코드에 가독성을 높인다.
```dart
Stream<String> report(Spacecraft craft, Iterable<String> objects) async* {
  for (var object in objects) {
    await Future.delayed(oneSecond);
    yield '${craft.name} flies by $object';
  }
}
```

## Exceptions
```dart
try {
  for (var object in flybyObjects) {
    var description = await File('$object.txt').readAsString();
    print(description);
  }
} on IOException catch (e) {
  print('Could not describe object: $e');
} finally {
  flybyObjects.clear();
}
```

## Other topics
[Language Tour](https://www.dartlang.org/guides/language/language-tour), [Library Tour](https://www.dartlang.org/guides/libraries/library-tour), [Dart API reference](https://api.dartlang.org/)