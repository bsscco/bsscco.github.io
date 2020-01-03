---
title: Flutter Today I Learned - 2019-04-23
date: 2019-04-23 09:51:41
tags: [flutter]
---

배울 문서 : https://drive.google.com/file/d/1d19SQry7jXwGd-wy3MeXJ35uCoy4veib/view
예제 소스코드 : https://github.com/markclow/flutter_book_examples

 
## Dart 클래스 이어서
- 연산자 오버라이딩 - `operator ==(orther) => (other is Car) && (_make == other._make) && (_model == other._model);`. == 연산자를 오버라이딩 할 땐 hashCode 메소드도 적절하게 오버라이딩 해줘야 함. collections 프레임워크에서 hashCode 메소드를 사용하기 때문
- 리플렉션 - `dart:mirrors`
- mixin - 다른 클래스에 의해 사용되는 메소드를 포함하는 클래스

## Dart Collections
- 라이브러리 - `dart:collection`
- List - 순서가 있는 컬렉션
- Map - key, value 쌍을 담는 컬렉션

## Dart Assertions
- 방어적 프로그래밍을 가능케 함.
- 사용 - `assert(age < 112, "bad age ${age}");`
- 플러터를 개발할 때 checked 모드 또는 debug 모드를 사용해서 assertions를 동작시킬 수 있음.

## Dart Errors and Exceptions
- Error - 복구할 수 없는. RangeError, OutOfMemoryError
- Exception - 복구할 수 있는. FormatException
- Error 핸들링 - Catcher 라이브러리
- Exception 핸들링 - try~on~catch~fInally, throw, rethrow

## Dart Console output
- `print()`

## Dart 비동기 처리
- Future - 처리가 완료되지 않은 상태로 시작되는 객체. 처리가 끝나면 콜백으로 알려줌. 미래에 아무것도 리턴하지 않을 거라면 Future<void> 
- 이젠 Future API (`future.then((val) => ... ).catchError((err) => ... );`)대신에 주로 await ~ async 문을 사용
- Async ~ Await
  ```dart
  void countUpAsynchronously(int count) async {
    print('Async operation start');
    String value = await createFutureCounter(count); 
    print('Async operation succeeded: ${value}');
  }
  ```
## Dart 반응형 프로그래밍
- 데이터 스트림을 사용한 선언적 프로그래밍 패러다임. arrays 같은 정적 처리와 event emmiters 같은 동적 처리 둘 다 쉽게 가능해짐.
- Stream API 
- 참고: https://medium.com/dartlang/making-dart-a-better-language-for-ui-f1ccaf9f546c