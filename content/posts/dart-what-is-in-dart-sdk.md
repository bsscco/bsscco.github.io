---
title: 서버사이드 Dart(2) - 다트 SDK에 있는 도구들
date: 2019-03-03 09:48:14
tags: [dart]
---

# What’s in the SDK
https://www.dartlang.org/tools/sdk#whats-in-the-sdk

### dart
standalone VM. 

### [dartdoc](https://github.com/dart-lang/dartdoc#dartdoc)
API 문서 생성기
```
# 패키지의 루트 디렉토리에서 사용해야 한다.
$ dartdoc
Generating documentation for 'server_code_lab' into <path-to-server-code-lab>/server_code_lab/doc/api/

parsing lib/client/piratesapi.dart...
parsing lib/common/messages.dart...
parsing lib/common/utils.dart...
parsing lib/server/piratesapi.dart...
Parsed 4 files in 8.1 seconds.

generating docs for library pirate.messages from messages.dart...
generating docs for library pirate.server from piratesapi.dart...
generating docs for library pirate.utils from utils.dart...
generating docs for library server_code_lab.piratesApi.client from piratesapi.dart...
Documented 4 libraries in 9.6 seconds.

Success! Docs generated into <path-to-server-code-lab>/server_code_lab/doc/api/index.html
```

### [dart2js](https://webdev.dartlang.org/tools/dart2js)
Dart를 Javascript로 변환하는 컴파일러. Web 개발에만 쓰임.
```
# 기본 사용법
dart2js -O2 -o test.js test.dart
```

### [dartfmt](https://github.com/dart-lang/dart_style#readme)
Dart 코드 포매터

### [dartanalyzer](https://github.com/dart-lang/sdk/tree/master/pkg/analyzer_cli#dartanalyzer)
Dart 정적 분석기. 컴파일타임에 [Dart 언어 스펙](https://www.dartlang.org/docs/spec/)에 기술된 에러나 경고를 잡아낸다.
```
# 기본 사용법
$ dartanalyzer bin/test.dart
```

### [pub](https://www.dartlang.org/tools/pub)
Dart 패키지 매니저. Flutter는 자체적으로 Dart 패키지를 갱신하고 관리한다.

### [dartdevc](https://webdev.dartlang.org/tools/dartdevc)
모던 웹브라우저에서 Dart 웹앱을 실행하고 디버깅 할 수 있게 해주는 Dart Dev Compiler(줄여서 DDC). Web 개발에만 쓰임.

