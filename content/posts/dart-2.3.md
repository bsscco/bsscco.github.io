---
title: Dart 2.3 - UI를 빌딩에 최적화 됐어요.
date: 2019-05-25
tags: [dart, flutter]
---

원문 : https://medium.com/dartlang/announcing-dart-2-3-optimized-for-building-user-interfaces-e84919ca1dff

 
## 새로운 language features
### spread 연산자
리스트의 요소들을 다른 리스트 안으로 풀어서 넣어주는 ... 연산자 (spread operator)
```dart
Widget build(BuildContext context) {
  return Column(children: [
    Header(),
    ...buildMainElements(), // spread 연산자
    Footer(),
  ]);
}

List<Widget> buildMainElements() { ... }
```

### collection if
조건에 맞지 않으면 요소가 들어가지 않게 해주는 문법. 단, list 리터럴에서만 사용할 수 있어요!
```dart
Widget build(BuildContext context) {
  return Column(children: [
    Text(mainText),
    if (page != pages.last) // collection if
      FlatButton(child: Text('Next')),
  ]);
}
```

### collection for
리스트의 요소들을 다른 리스트 안으로 풀어서 넣되, 들어가는 요소들에 가공을 더해주는 문법. 단, list 리터럴에서만 사용할 수 있어요!
```dart
Widget build(BuildContext context) {
  return Column(children: [
    Text(mainText),
    for (var section in sections) // collection for
      HeadingAction(section.heading),
  ]);
}
```
