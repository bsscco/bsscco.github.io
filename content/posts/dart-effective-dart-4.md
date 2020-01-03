---
title: 이펙티브 Dart(4) - 사용성 가이드
date: 2019-03-02 22:05:06
tags: [dart]
---

Effective Dart: Usage
https://www.dartlang.org/guides/language/effective-dart/usage



- [Libraries](/2019/03/02/effective-dart-4/#Libraries)
  - **DO** `part of` 지시어에서 URI를 사용해라.
  - **DON’T** 다른 패키지의 src 디렉토리를 가지고 있는 라이브러리는 import 하지 마라. 
  - **PREFER** relative paths when importing libraries within your own package’s lib directory.
  
- [Strings](/2019/03/02/effective-dart-4/#Strings)
  - 
  
- [Collections](/2019/03/02/effective-dart-4/#Collections)
  -  

- [Functions](/2019/03/02/effective-dart-4/#Functions)
  -  
  
- [Parameters](/2019/03/02/effective-dart-4/#Parameters)
  -

- [Variables](/2019/03/02/effective-dart-4/#Variables)
  -

- [Merbers](/2019/03/02/effective-dart-4/#Members)
  -

- [Constructors](/2019/03/02/effective-dart-4/#Constructors)
  -
  
- [Error handling](/2019/03/02/effective-dart-4/#Error-handling)
  -

- [Asynchrony](/2019/03/02/effective-dart-4/#Asynchrony)
  -
  
## Libraries
##### **DO** use strings in part of directives.
`part of` 지시어에서 URI를 사용해라. `part of`에 URI 대신에 라이브러리 이름을 지정하면 툴이 메인 라이브러리 파일을 찾기 어려워지기 때문이다.
```dart
// 본체 파일
library my_library;

part "some/other/file.dart";
```
```dart
// 일부분을 담당하는 파일
part of "../../my_library.dart"; // (O)
part of my_library; // (X)
```

##### **DON’T** import libraries that are inside the src directory of another package.
다른 패키지의 src 디렉토리를 가지고 있는 라이브러리는 import 하지 마라. src 디렉토리를 가지고 있는 라이브러리는 소스코드들이 유지보수자들에 의해 언제든 바뀔 수 있기 때문에 안전하지 않다.

##### **PREFER** relative paths when importing libraries within your own package’s lib directory.