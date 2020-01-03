---
title: Dart 비동기 프로그래밍 Futures 요약
date: 2019-02-20 23:59:00
tags: [dart]
---

https://www.dartlang.org/tutorials/language/futures

```dart
import 'dart:async';

Future<void> printAsyncMsg() async {
  String result = await execLongTask(); // 반환값 Future<String>가 작업을 끝낼 때까지 기다린다.
  print(result); // Future의 작업이 끝나면 result에 String값이 담기고, 그 다음 print()가 호출된다.
}

Future<String> execLongTask() {
  // Future를 반환한다.
  return Future.delayed(Duration(seconds: 1), () => 'This is async msg.');
}

printSyncMsg() {
  print('This is sync msg.'); // 호출받자마자 바로 출력한다.
}

main() {
  printAsyncMsg(); // 비동기 함수를 먼저 호출한다.
  printSyncMsg(); // 동기 함수를 뒤에 호출한다.
}

```
