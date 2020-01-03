---
title: 이펙티브 Dart(1) - 개요
date: 2019-02-27 08:05:06
tags: [dart]
---

Effective Dart
https://www.dartlang.org/guides/language/effective-dart

## 모든 걸 아우르는 테마 2가지
- 코드를 일관성 있게 만들자.
- 코드를 간결하게 만들자.

Dart 분석기엔 좋은 코드를 돕는 linter가 있다. [prefer_collection_literals](https://dart-lang.github.io/linter/lints/prefer_collection_literals.html), [enabling linter rules](https://www.dartlang.org/guides/language/analysis-options#enabling-linter-rules)

## The guides
- **Style Guide** - 코드 배치와 조직화, dartfmt가 다루지 않는 부분을 위한 규칙을 정의한다. `camelCase`, `using_underscores`와 같이 identifier의 이름을 어떻게 짓는지도 정의한다.
- **Documentation Guide** - 문서화에 필요한 모든 걸 알려준다.
- **Usage Guide** - Dart 언어의 특징을 잘 사용할 수 있는 방법을 가르쳐준다.
- **Design Guide** - 가장 넓은 영역을 담는다. 일관성 있게 설계하는 법과 라이브러리 API 사용법 등

## How to read the guides
각 가이드는 몇가지 섹션으로 나뉜다. 섹션은 가이드라인 목록으로 이루어지고, 각 가이드라인은 몇가지 단어들 중 하나로 시작한다. :
- **DO(꼭 해라)**
- **DON’T(절대 하지 마라)**
- **PREFER(특별한 이유가 없다면 해라)**
- **AVOID(특별한 이유가 없다면 하지 마라)**
- **CONSIDER(고려하면 좋다)**

## Glossary
이 가이드라인을 간결하게 유지하기 위해 짧은 용어를 사용한다.
- **library member**는 top-level에 있는 필드, 게터, 세터, 함수를 의미한다. 타입을 제외하고 top-level에 있는 어떤 것이든 해당된다.
- **class member**는 클래스 안에 정의된 생성자, 필드, 게터, 세터, 메소드를 의미한다. 클래스의 멤버는 instance, static, abstract, concrete로 분류할 수 있다.
- **member**는 library member 또는 class member를 의미한다.
- **variable**은 top-level 변수, 파라메터, 지역 변수를 의미한다. static, instance 필드는 포함하지 않는다.
- **type**은 class, typedef, enum의 이름으로 선언된 타입을 의미한다.
- **property**는 top-level의 변수, 게터, 세터와 클래스의 게터, 세터, 필드를 의미한다. field-like

## [Summary of all rules](https://www.dartlang.org/guides/language/effective-dart#summary-of-all-rules)   