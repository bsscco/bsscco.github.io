---
title: 서버사이드 Dart(3) - 다트의 주요 라이브러리들
date: 2019-03-03 21:16:19
tags: [dart]
---

# Dart API
https://api.dartlang.org/stable/2.2.0/index.html

기초가 되는 Dart 라이브러리:
- `dart:core`: strings, numbers, collections, errors, URIs 같은 코어 기능들이 있다.
- `dart:html`: 웹앱을 위한 DOM 조작 기능들이 있다.
- `dart:io`: 웹앱이 아닌 앱을 위한 I/O 기능들이 있다.

`dart:core`를 제외하고 필요한 라이브러리는 import 해야 한다.
```dart
import 'dart:async';
import 'dart:math';
``` 
[pub 패키지 매니저](pub package manager)에서 더 많은 라이브러리들을 설치할 수 있다.
 
## Libraries
### Core
**[dart:async](https://api.dartlang.org/stable/2.2.0/dart-async/dart-async-library.html)**

Future, Stream 같은 클래스와 함께 비동기 프로그래밍을 지원하는 기능들이 들어있다.

**[dart:collection](https://api.dartlang.org/stable/2.2.0/dart-collection/dart-collection-library.html)**

`dart:core`의 collection 클래스를 보충하는 클래스와 유틸이 들어있다.

**[dart:convert](https://api.dartlang.org/stable/2.2.0/dart-convert/dart-convert-library.html)**

json, utf-8 등 다른 데이터 표현들 사이에 컨버팅을 위한 인코더, 디코더가 들어있다.

**[dart:core](https://api.dartlang.org/stable/2.2.0/dart-core/dart-core-library.html)**

빌트인 타입들과 collection, 핵심 기능들이 들어있다.

**[dart:developer](https://api.dartlang.org/stable/2.2.0/dart-developer/dart-developer-library.html)**

디버거, 인스펙터 같은 개발도구와 인터렉션하는 기능들이 들어있다.

**[dart:math](https://api.dartlang.org/stable/2.2.0/dart-math/dart-math-library.html)**

수학 상수와 함수들, 랜덤수 발생기가 들어있다.

**[dart:typed_data](https://api.dartlang.org/stable/2.2.0/dart-typed_data/dart-typed_data-library.html)**

unsigned 8 byte integer, SIMD numeric type 등 고정사이즈 데이를 효과적으로 다룰 수 있는 list들이 들어있다.
 
### VM
**[dart:cli](https://api.dartlang.org/stable/2.2.0/dart-cli/dart-cli-library.html)**

**[dart:io](https://api.dartlang.org/stable/2.2.0/dart-io/dart-io-library.html)**

웹앱이 아닌 앱을 위한 파일, 소켓, HTTP 등의 IO 기능들이 들어있다.

**[dart:isolate](https://api.dartlang.org/stable/2.2.0/dart-isolate/dart-isolate-library.html)**

isolate를 사용해 병행프로그래밍을 할 수 있다. isolate는 스레드와 달리 isolate 간에 메모리를 공유하지 않고 메시지로만 통신한다.

**[dart:mirrors](https://api.dartlang.org/stable/2.2.0/dart-mirrors/dart-mirrors-library.html)**

Dart의 기초적인 리플렉션 기능들이 들어있다. 리플렉션은 인스펙션과 동적 invocation을 지원한다.

### Web
**[dart:html](https://api.dartlang.org/stable/2.2.0/dart-html/dart-html-library.html)**

브라우저, DOM과 인터렉션이 필요한 웹앱을 위한 HTML 요소와 기능들이 들어있다.

**[dart:indexed_db](https://api.dartlang.org/stable/2.2.0/dart-indexed_db/dart-indexed_db-library.html)**

클라이언트에서 쓰는 key-value 저장소에 대한 기능들이 들어있다. 이 저장소는 인덱스를 지원한다.

**[dart:js](https://api.dartlang.org/stable/2.2.0/dart-js/dart-js-library.html)**

자바스크립트를 운용하기 위한 기능들이 들어있다.

**[dart:js_util](https://api.dartlang.org/stable/2.2.0/dart-js_util/dart-js_util-library.html)**

Utility methods to efficiently manipulate typed JSInterop objects in cases where the name to call is not known at runtime. You should only use these methods when the same effect cannot be achieved with @JS annotations. These methods would be extension methods on JSObject if Dart supported extension methods.

**[dart:svg](https://api.dartlang.org/stable/2.2.0/dart-svg/dart-svg-library.html)**

SVG포맷을 지원하는 기능들이 들어있다.

**[dart:web_audio](https://api.dartlang.org/stable/2.2.0/dart-web_audio/dart-web_audio-library.html)**

브라우저에서 높은 수준의 오디오 프래그래밍을 할 수 있는 기능들이 들어있다.

**[dart:web_gl](https://api.dartlang.org/stable/2.2.0/dart-web_gl/dart-web_gl-library.html)**

브라우저에서 3D 프로그래밍을 할 수 있는 기능들이 들어있다.

**[dart:web_sql](https://api.dartlang.org/stable/2.2.0/dart-web_sql/dart-web_sql-library.html)**

브라우저에서 SQL로 데이터를 저장할 수 있는 기능들이 들어있다.