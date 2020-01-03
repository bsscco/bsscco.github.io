---
title: 이펙티브 Dart(2) - 스타일 가이드
date: 2019-02-28 08:05:06
tags: [dart]
---

Effective Dart: Style
https://www.dartlang.org/guides/language/effective-dart/style

naming, ordering, formatting의 일관성은 다른 사람들이 코드를 배우고, 공유하기 쉽게 해준다.  

- [Identifiers](/2019/02/27/effective-dart-2/#Identifiers)
  - **DO** 타입 이름은 UpperCamelCase로 지어라.
  - **DO** 라이브러리, 패키지, 디렉토리, 소스파일 이름은 lowercase_with_underscores로 지어라.
  - **DO** import prefix(alias)는 lowercase_with_underscores로 지어라.
  - **DO** 나머지 다른 identifier는 lowerCamelCase로 지어라.
  - **PREFER** 상수 이름은 lowerCamelCase로 지어라.
  - **DO** 2글자보다 더 긴 축약어는 첫글자만 대문자화 해라. // ex) IOStream (X)
  - **DON’T** prefix 문자를 사용하지 마라. ex) kDefaultTimeout (X)
- [Ordering](/2019/02/27/effective-dart-2/#Ordering)
  - **DO** `dart:` import를 다른 라이브러리 import 이전에 써라.
  - **DO** `package:` import를 상대경로에 있는 라이브러리 import보다 이전에 써라.
  - **PREFER** 외부의 `package:` import를 내 라이브러리의 import 이전에 써라.
  - **DO** export는 모든 import 뒤에 구분된 섹션에 써라.
  - **DO** 모든 import문 섹션들을 알파벳 순서로 정렬해라.
- [Formatting](/2019/02/27/effective-dart-2/#Formatting)
  - **DO** `dartfmt`를 사용해서 포맷팅 해라.
  - **CONSIDER** 당신의 코드를 더 formatter-friendly하게 만들 수 있는지 고려해라.
  - **AVOID** 80글자보다 더 긴 줄은 피해라.
  - **DO** 모든 조건문에 curly braces {}를 사용해라.

## Identifiers
##### **DO** name types using UpperCamelCase.
타입 이름은 UpperCamelCase로 지어라. 
```dart
class SliderMenu { ... }
typedef Predicate<T> = bool Function(T value);
```

##### **DO** name libraries, packages, directories, and source files using lowercase_with_underscores.
라이브러리, 패키지, 디렉토리, 소스파일 이름은 lowercase_with_underscores로 지어라.
```dart
library peg_parser.source_scanner;
import 'file_system.dart';
```

##### **DO** name import prefixes using lowercase_with_underscores.
 import prefix(alias)는 lowercase_with_underscores로 지어라.
 ```dart
import 'dart:math' as math;
import 'package:angular_components/angular_components'
    as angular_components;
import 'package:js/js.dart' as js;
```

##### **DO** name other identifiers using lowerCamelCase.
나머지 다른 identifier는 lowerCamelCase로 지어라.
```dart
var item;

HttpRequest httpRequest;

void align(bool clearItems) { ... }
```
 
##### **PREFER** using lowerCamelCase for constant names.
상수 이름은 lowerCamelCase로 지어라.
```dart
const pi = 3.14;
const defaultTimeout = 1000;
```
자바의 SCREAMING_CAPS 스타일을 사용하지 않는 이유
- 예를 들면, CSS 컬러값들 에서처럼 많은 경우에 SCREAMING_CAPS는 읽기에 나쁘다.
- 상수는 자주 변수로 바뀌는데, 그럴 때마다 이름을 lowerCamelCase 스타일로 바꾸어야 한다.

##### **DO** capitalize acronyms and abbreviations longer than two letters like words.
**2글자**보다 더 긴 축약어는 첫글자만 대문자화 해라.
```dart
HttpConnectionInfo // HTTPConnectionInfo (X)
IoStream // IOStream (X)
DB // Bb (X)
Id // ID (X) Id, Mr. 같은 2글자 축약어는 여전히 단어처럼 대문자로 표기된다.
```

##### **DON’T** use prefix letters.
prefix 문자를 사용하지 마라. 
```dart 
defaultTimeout // kDefaultTimeout (X)
```

## Ordering
##### **DO** place “dart:” imports before other imports.
“dart:” import를 다른 라이브러리 import 이전에 써라.
```dart
import 'dart:html'; 
                                       // 섹션 구분은 빈 한줄로 한다.
import 'package:bar/bar.dart';
```

##### **DO** place “package:” imports before relative imports.
“package:” import를 상대경로에 있는 라이브러리 import보다 이전에 써라.
```dart
import 'package:foo/foo.dart';

import 'util.dart'; // 상대경로에 있는 라이브러리
```

##### **PREFER** placing external “package:” imports before other imports.
외부의 “package:” import를 내 라이브러리의 import 이전에 써라.
```dart
import 'package:foo/foo.dart';

import 'package:my_package/util.dart';
```

##### **DO** specify exports in a separate section after all imports.
export는 모든 import 뒤에 구분된 섹션에 써라.
```dart
import 'src/foo_bar.dart';

export 'src/error.dart';
```

##### **DO** sort sections alphabetically.
모든 import문 섹션들을 알파벳 순서로 정렬해라.
```dart
import 'package:bar/bar.dart';
import 'package:foo/foo.dart';

import 'foo.dart';
import 'foo/foo.dart';
```

## Formatting
##### **DO** format your code using dartfmt.
`dartfmt`를 사용해서 포맷팅 해라. [some documentation](https://github.com/dart-lang/dart_style/wiki/Formatting-Rules)

##### **CONSIDER** changing your code to make it more formatter-friendly.
당신의 코드를 더 formatter-friendly하게 만들 수 있는지 고려해라.

##### **AVOID** lines longer than 80 characters.
80글자보다 더 긴 줄은 피해라. 

보통 VeryLongCamelCaseClassNames과 같이 긴 이름을 지을 때 이런 상황이 발생하며, 이런 경우엔 dartfmt가 긴 문자열을 자르지 않는다. 당신이 판단해서 처리하길 바란다.

##### **DO** use curly braces for all flow control structures.
모든 조건문에 curly braces {}를 사용해라.
```dart
if (isWeekDay) {
  print('Bike to work!');
} else {
  print('Go dancing or read a book!');
}

if (arg == null) return defaultValue; // else절이 따라오지 않고 문장이 짧을 땐 한 줄로 써도 된다.
```