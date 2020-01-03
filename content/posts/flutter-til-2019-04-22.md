---
title: Flutter Today I Learned - 2019-04-22
date: 2019-04-22 09:51:41
tags: [flutter]
---

배울 문서 : https://drive.google.com/file/d/1d19SQry7jXwGd-wy3MeXJ35uCoy4veib/view
예제 소스코드 : https://github.com/markclow/flutter_book_examples

 
## Flutter 빅픽쳐
- 구글과 애플이 제공한 United API는 서로를 100% 커버하지 못함. Xamarin, Appcelerator, Nativescript 
- WebView를 사용한 방법은 앱 속도가 느림. Cordova, PhoneGap
- ReactNative는 bridge를 사용해 WebView보다 빠르지만 여전히 네이티브 코드보다 느림.
- Flutter는 자체 렌더링 엔진을 사용하기 때문에 네이티브 만큼 빠르고 플랫폼에 독립적. 여기에 더해서 핫리로딩 & 가상돔을 제공. 

## Dart 소개
- Java, C# like 언어
- SDK
  - dart - 코어 라이브러리
  - dart2js - 소스코드 dart to js
  - dartdevc - dart to js. 개발용으로 변환해주기 때문에 dart2js의 기능에서 최적화만 빠짐.
  - dartanalyzer - 소스코드의 error와 warning을 잡아줌.
  - dartdoc - 소스코드 문서화 도구
  - dartfmt - 소스코드에 표준 포매팅을 적용해주는 도구
  - pub: 패키지 매니저
- 라이브러리
  - dart:core - built-in type, collection 등
  - dart:async - 비동기 지원. Future와 Stream 클래스를 제공
  - dart:math - 수학 관련 라이브러리
  - dart:convert - JSON, UTF-8 인코더, 디코더

## Dart Typing
- 정적 언어 - AOT 컴파일러를 사용해 컴파일. 정적 분석을 통해 에러를 잡아내기 쉬워서 복잡한 시나리오에 유리 
- 동적 언어 - 타입을 기술하지 않아도 돼서 개발속도는 빠르지만 에러를 잡아내기 어려워서 단순한 시나리오에 유리
- Dart Typing - AOT에 타입 체킹, runtime에도 타입 체킹 둘 다 가능
- 정적 타입 
  - int
  - double
  - bool
  - String - immutable string
  - StringBuffer - mutable string
  - RegExp
  - List, Map, Set
  - DateTime
  - Duration
  - Uri
  - Error
- 동적 타입
  - var - 변수의 자료형이 동적으로 선택되게 함. (= 컴파일 타임에 타입추론)
  - dynamic - 어떤 자료형도 담을 수 있음. (= Object타입)
- is 키워드
- runtimeType 속성

## Dart String
- interpolation - `'name is ${p.firstName}'`
- raw string - `r'this\nstring'`
- runes - `var clapping = '\u{1f44f}';`

## Dart 클래스
- 싱글 파일에 멀티 클래스 가능
- 생성자 syntactic sugar - `Name(this.firstName, this.lastName);`
- new 키워드 필요 없음.
- named 생성자 
- optional 생성자 파라메터 - `Person({this.name, this.age});`
- required 생성자 파라메터 - `Person({@required this.name, this.age});`
- positional optional - `Car(this.make, [this.model])`
- factory 생성자 - `factory Printer(){ ... }`
- private 변수 - `_name;`
- default value - `null`
- method cascade 
  ```dart
  Logger()
      ..log('program started')
      ..log('going something')
      ..log('program finished');
      }
  ```