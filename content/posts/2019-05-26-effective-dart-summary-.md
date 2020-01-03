---
title: 이펙티브 Dart 한 장으로 요약!
date: 2019-05-26
tags: [dart]
---

원문 : https://dart.dev/guides/language/effective-dart

## 들어가면서
### general한 지향점
- 코드를 일관성 있게 만들자.
- 코드를 간결하게 만들자.

linter를 애용하자! : [enabling linter rules](https://dart.dev/guides/language/analysis-options#enabling-linter-rules)

### 가이드 종류
- **Style Guide** - 코딩 스타일 가이드
- **Documentation Guide** - 문서화 가이드
- **Usage Guide** - language features 사용 가이드
- **Design Guide** - 일관성 있는 설계를 위한 가이드

### 가이드를 어떻게 읽어야 할까?
- **DO** - 꼭 이렇게 하세요!
- **DON’T** - 절대 이렇게 하지 마세요!
- **PREFER** - 특별한 상황이 아니라면 이렇게 하세요.
- **AVOID** - 특별한 이유가 없다면 이렇게 하지 마세요.
- **CONSIDER** - 어떻게 할지 상황에 따라 고려하세요.

## 모든 규칙 요약
### Style
#### Identifiers   
- **DO name types using UpperCamelCase.** 
타입명은 UpperCamelCase로 지으세요. ex) `class UpcomingProduct { ... }`

- **DO name libraries, packages, directories, and source files using lowercase_with_underscores.**
라이브러리, 패키지, 디렉토리, 파일명은 lowercase_with_underscores로 지으세요. 

- **DO name import prefixes using lowercase_with_underscores.**
import문에서 as를 사용해 별명을 지을 땐 lowercase_with_underscores로 지으세요.

- **DO name other identifiers using lowerCamelCase.**
나머지 identifiers는 lowerCamelCase로 지으세요. ex) `void addItem(String item) { ... }`  

- **PREFER using lowerCamelCase for constant names.**
상수명은 lowerCamelCase로 지으세요. 

- **DO capitalize acronyms and abbreviations longer than two letters like words.**
2글자 이상의 축약어는 첫글자만 대문자화 하세요. ex) `URLConnection` 말고 `UrlConnection`  

- **DON’T use prefix letters.**
전치문자를 사용하지 마세요. ex) `iNumOfChildren` 말고 `numOfChildren`

#### Ordering
- **DO place “dart:” imports before other imports.**
`dart:`(빌트인 라이브러리) import문을 가장 앞에 분리된 섹션으로 두세요. 

- **DO place “package:” imports before relative imports.**
`package:` import문을 상대경로 import문(현재 라이브러리의 파일들을 import 하는 것)보다 앞에 분리된 섹션으로 두세요.   

- **PREFER placing external “package:” imports before other imports.**
외부 `package:` import문을 다른 import문(현재 라이브러리의 파일들을 `package:`를 써서 import하는 경우 포함)보다 앞에 분리된 섹션으로 두세요.

- **DO specify exports in a separate section after all imports.**
export문은 모든 import문 뒤에 분리된 섹션으로 두세요.

- **DO sort sections alphabetically.**
각 섹션의 import문들을 알파벳순으로 정렬시키세요.

- ordering 정리 
```dart
import dart:빌트인
import ...

import package:외부
import ...

import package:내부
import ...

import lib/adcd/... 상대경로
import ...

export ...
export ...
```

#### Formatting
- **DO format your code using dartfmt.**
dartfmt를 사용해서 코드를 포맷팅하세요. mac 단축키: `Cmd+Opt+L` 

- **CONSIDER changing your code to make it more formatter-friendly.**
dartfmt에 의해 포맷팅된 코드가 더 읽기 어려워질 수도 있다는 걸 고려하세요.

- **AVOID lines longer than 80 characters.**
한 줄에 80자 이상은 피하세요.  
why? 긴 줄은 눈이 다음 줄 첫글자로 이동하는 데에 어려움을 줍니다.

- **DO use curly braces for all flow control structures.**
모든 조건문에 {}를 사용하세요. else절이 없는 경우는 제외
why?  dangling else 문제를 피할 수 있습니다.

### Usage
#### Libraries
- **DO use strings in part of directives.**
`part of` 지시어에 베이스파일의 라이브러리명 대신에 베이스파일의 uri string을 쓰세요. ex) `part of "../../my_library.dart";`   
why? 라이브러리명을 쓰면 tools가 메인 라이브러리 파일을 찾기 어려워집니다. 

- **DON’T import libraries that are inside the src directory of another package.**
다른 라이브러리의 src 디렉토리 안에 있는 파일들을 import 하지 마세요.
why? lib 안에 있는 src 디렉토리는 그 라이브러리 개발자들에게 private하게 쓰이는 영역입니다.

- **PREFER relative paths when importing libraries within your own package’s lib directory.**
현재 라이브러리의 파일들을 import할 땐 상대경로를 쓰세요. ex) `import 'src/utils.dart';`
why? 외부 패키지 라이브러리의 파일 import와 중복 시 경합되는 것을 피할 수 있습니다.

#### Strings
- **DO use adjacent strings to concatenate string literals.**
문자열 리터럴이 서로 붙어있을 때 + 연산자를 쓰지 마세요.
ex) 
```dart
raiseAlarm(
    'ERROR: Parts of the spaceship are on fire. Other '
    'parts are overrun by martians. Unclear which are which.');
```
 
- **PREFER using interpolation to compose strings and values.**
문자열들을 구성할 땐 interpolation을 쓰세요. ex) `'Hello, $name! You are ${year - birth} years old.'`
why? 문자열이 명확하고 짧아집니다.

- **AVOID using curly braces in interpolation when not needed.**
interpolation에서 불필요한 {}는 빼세요.

#### Collections
- **DO use collection literals when possible.**
size를 넘기는 non-growable list나 상속해서 커스텀한 collection을 생성할 때만 생성자를 쓰고, 나머지 경우엔 collection literal을 쓰세요.
why? 두 방법 사이에 의미가 구분되고 collections literal이 더 간결합니다.

- **DON’T use .length to see if a collection is empty.**
단순히 collection이 비어있는지 확인할 때는 .length 대신에 `isEmpty()` 또는 `isNotEmpty()`를 쓰세요. 
why? `.length`는 성능상 더 느립니다. 

- **CONSIDER using higher-order methods to transform a sequence.**
collection을 가공하고자 할 땐 `map()`, `where()` 같은 operation 메소드를 쓰세요.
why? `map()`, `where()` 등은 더 짧고 선언적이며 새로운 collection을 생성한다는 의미를 명확하게 표현할 수 있고 사이드 이펙트도 없습니다.

- **AVOID using Iterable.forEach() with a function literal.**
collection을 순회할 때는 forEach() 대신에 for-in loop를 쓰세요.
why? break; 같이 일반적인 동작을 할 수 있습니다.
 
- **DON’T use List.from() unless you intend to change the type of the result.**
iterable을 list로 만들 때는 `toList()`를 쓰고, 다른 타입의 list를 만들 때는 `List.from<T>()`을 사용하세요.
why? 두 방법 사이의 의미가 명확하게 구분되며 `List.from()`을 실수로 제너릭 타입명시 없이 사용하면 `List<dynamic>`이 만들어집니다.

- **DO use whereType() to filter a collection by type.**
collection을 타입으로 필터링할 때는 whereType()을 쓰세요.
why? `where()` + `cast()` 연산을 한 번으로 줄여서 성능을 높힙니다.

- **DON’T use cast() when a nearby operation will do.**
collection의 operation에서 타입을 바꿀 수 있을 땐 cast()를 사용하지 마세요.
why? 예를 들어 `[1,2,3].map(n => 1 / n).cast<double>()`은 의도하지 않은 결과를 낼 수도 있고 연산도 두 번 하지만, `[1,2,3].map<double>(n => 1 / n)`은 의도한대로 동작하며 연산도 한 번으로 줄어듭니다.

- **AVOID using cast().**
cast() 연산의 대안들을 사용하세요.
  - 만들 때 올바른 타입으로 만들기
  - 각 iteration 안에서 요소에 접근할 때 캐스팅 하세요
  - 모든 요소를 iteration 해야 할 땐 `List.from<T>`을 쓰세요.
 why? `cast()`는 모든 연산에서 타입 체크를 하는 lazy collection을 반환하기 때문에 성능상 불리합니다.

#### Functions
- **DO use a function declaration to bind a function to a name.**
함수 안에서 람다를 변수에 담아 사용하는 대신에 함수선언문으로 선언한 지역함수를 사용하세요.

- **DON’T create a lambda when a tear-off will do.**
람다를 콜백 인자로 넘겼을 때 람다 안에서 람다가 전달받는 파라메터를 함수가 바로 사용한다면 괄호()를 없애세요.
ex) names.forEach(print);

#### Parameters
- **DO use = to separate a named parameter from its default value.**
파라메터의 디폴트값을 지정할 때 : 대신에 =을 쓰세요.
why? :은 레거시 문법입니다.

- **DON’T use an explicit default value of null.**
파라메터의 디폴트값이 null이어야 한다면 명시하지 마세요.
why? dart 언어에서 모든 객체의 초기값은 null입니다.

#### Variables
- **DON’T explicitly initialize variables to null.**
변수의 초기값이 null이어야 한다면 명시하지 마세요.
why? dart 언어에서 모든 객체의 초기값은 null입니다.

- **AVOID storing what you can calculate.**
클래스에서 계산될 값들도 멤버변수로 저장하지 마세요.
why? 생성자에서 radius로 area를 만들어 저장한 경우 radius가 바뀌면 area와 동기화가 깨집니다. area를 getter에서 계산하게 하면 rsdius와 area가 항상 동기화 되며 area에 대한 메모리 사용도 없어집니다. 
예외: 느린 계산에 대해서는 캐시처리 필요합니다.

#### Members
- **DON’T wrap a field in a getter and setter unnecessarily.**
필드의 getter, setter는 이유없이 만들지 마세요.

- **PREFER using a final field to make a read-only property.**
read-only 속성을 만들기 위해 getter만 만드는 방법 대신에 final을 사용하세요.
why? 의미가 명확하고 코드가 간결해집니다.
예외: 내부적으로 필드를 초기화해야 할 때

- **CONSIDER using => for simple members.**
함수 로직이 한 줄로 끝날 만큼 짧을 때만 =>를 사용하세요.
why? 가독성을 유지할 수 있습니다.

- **DON’T use this. except to redirect to a named constructor or to avoid shadowing.**
필드가 지역변수에 가려질 때, named 생성자를 호출해야 할 때만 this.를 사용하세요.
why? 코드가 간결해집니다.

- **DO initialize fields at their declaration when possible.**
생성자에 의존하지 않는 필드는 선언부에서 초기화 하세요.
why? 초기화를 까먹지 않게 해주고, 코드를 간결하게 만들어줍니다.

#### Constructors
- **DO use initializing formals when possible.**
초기화 양식을 사용하세요.
ex) Point(this.x, this.y)
why? 코드가 간결해집니다.

- **DON’T type annotate initializing formals.**
초기화 양식에서 타입을 지정하지 마세요.
why? 불필요 합니다.

- **DO use ; instead of {} for empty constructor bodies.**
생성자 body가 비었을 때는 `{}` 대신에 `;`를 쓰세요.

- **DON’T use new.**
`new` 키워드를 사용하지 마세요.
why? dart 1의 레거시 문법입니다.

- **DON’T use const redundantly.**
const 선언 안에서 `const` 선언을 하지 마세요.
ex) `const colors = [ Color(0xff00ff) ]`
why? 불필요합니다.

#### Error handling
- **AVOID catches without on clauses.**
- **DON’T discard errors from catches without on clauses.**
- **DO throw objects that implement Error only for programmatic errors.**
- **DON’T explicitly catch Error or types that implement it.**
- **DO use rethrow to rethrow a caught exception.**

#### Asynchrony
- **PREFER async/await over using raw futures.**
- **DON’T use async when it has no useful effect.**
- **CONSIDER using higher-order methods to transform a stream.**
- **AVOID using Completer directly.**
- **DO test for Future<T> when disambiguating a FutureOr<T> whose type argument could be Object.**
