---
title: Dart 비동기 프로그래밍 Streams 요약
date: 2019-02-21 00:19:30
tags: [dart]
---

https://www.dartlang.org/tutorials/language/streams

Future는 결과가 준비됐을 때 우리에게 알려준다.
Stream은 이벤트가 발생할 때마다 우리에게 알려준다.

```dart
import 'dart:async';

Stream<Future<int>> countStream(int to) async* {
  for (int i = 1; i <= to; i++) {
    yield Future.delayed(Duration(seconds: 1), () => i); // 1초 후에 i를 반환하는 Future를 발생시킨다.
  }
}

Future<void> printSumStream(Stream<Future<int>> stream) async {
  var sum = '';
  await for (var value in stream) {
    // 스트림의 이벤트 발생이 끝날 때가지 기다린다.
    sum += (await value).toString(); // 이벤트도 Future라서 Future의 작업이 끝날 때까지 기다린다.
    print(sum); // sum에 Future가 반환한 값을 더해서 출력한다.
  }
}

printSyncMsg() {
  print('This is sync msg.');
}

main() async {
  var stream = countStream(3);
  printSumStream(stream);
  printSyncMsg();
}
```