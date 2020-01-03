---
title: Dart 언어 여행하기 요약(1)
date: 2019-02-21 22:57:49
tags: [dart]
---

Dart A Tour of the Dart Language
https://www.dartlang.org/guides/language/language-tour

- Inportant concepts  
  - 모든 게 객체(1, null, function, true, ...)
  - var/final형 선언 : 타입 추론 , 어느 타입이든 담게 하려면 dynamic형 선언
  - 제네릭 타입 List<int>
  - 일급함수, 정적메소드, nested 함수, 지역함수
  - 일급변수, 정적변수
  - private 변수/메소드화 : _(underscore)로 시작해서 이름짓기. 라이브러리 내에서만 접근 가능
  - is-else같은 statements는 expression이 될 수 없다.
- Keywords
  - 생소한 키워드
    - as
      - 형변환 연산자, import문에서 alias 연산자
    - covariant
      - 상위타입을 지정해야 하는 곳에 하위 타입을 지정할 수 있게 해준다. 이렇게 하면 런타임에 유요하지 않은 인수타입을 확인할 수 있다.
      - ```dart
        class Animal {
          void chase(Animal x) { ... }
        }
        class Mouse extends Animal { ... }
        class Cat extends Animal {
          void chase(covariant Mouse x) { ... }
        }
        ```
    - deferred
      - 라이브러리 로딩을 지연시켜준다.
      - ```dart
        import 'package:greetings/hello.dart' deferred as hello;
        Future greet() async {
          await hello.loadLibrary();
          hello.printGreeting();
        }
        ```
    - export
      - 라이브러리 프로젝트에서 lib/src의 어떤 파일들을 공개할지 지정할 때 사용
      - `export 'src/cascade.dart';`
    - external
      - 다른 위치에서 body를 정의하도록 만든 일급함수. 다른 환경에서 구현되는 DartVM을 위해 사용된다고 한다.
    - factory
      - 팩토리 생성자를 지정한다.
    - Function
      - 함수의 자료형
    - get
      - getter로 지정
    - hide
      - import문에서 부분적으로만 비공개하게 만듦. 
    - is
      - 형비교 연산자
    - library
      - ??
    - mixin
      - 믹스인 클래스가 일반적인 클래스로 사용되지 않고 기능만 가질 때 class 키워드 대신에 mixin을 쓴다.
    - show
      - import문에서 부분적으로만 공개하게 만듦. 
      - `import 'package:lib1/lib1.dart' show foo; // Import only foo.`
    - on
      - try~catch문에서 특정 예외타입을 잡아내고 싶을 때 on을 쓴다.
      - `try {...} on SpecificException catch (e) {...} catch (e, s) {...}`
      - mixin 정의부에서 특정 클래스에서만 사용될 수 있게 할 때 on을 쓴다.
    - operator
      - 연산자 오버라이딩을 위한 연산자
      - `Vector operator +(Vector v) => Vector(x + v.x, y + v.y);`
    - part
      - export와 관련
    - rethrow
      - catch절에서 exception을 다시 throw한다.
    - set
      - setter로 지정
    - sync
      - Iterator를 반환하는 generator 함수를 만들 때 지정한다.
    - typedef
      - 함수(=객체)를 형으로 사용할 수 있게 해준다.
      - `typedef Compare = int Function(Object a, Object b);`
    - var
      - 변수 선언 시 타입추론을 가능케 한다.
    - with
      - 다른 클래스의 기능을 가져다 쓰게 해준다.
    - yield
      - generator 함수를 만들 때 반환값에 지정한다.
- Variables
  - ```dart
    var a; // 초기화 되면서 타입도 추론된다.
    dyanmic or Object a; // 여러 타입을 담을 수 있다.
    final a; // 한 번 초기화된 값은 바꿀 수 없다. 초기화 되면서 타입도 추론된다.
    const a; // 컴파일 타임부터 바뀌지 않을 값만 초기화할 수 있다.
    ```
  - 모든 변수의 기본값은 null이다. 모든 게 객체이기 때문에
  - 객체 생성 시 앞에 const를 붙이면 상수값이 된다.
    - ```dart
      var foo = const []; // 리스트를 수정할 수 없다.
      final bar = const []; // bar도 리스트도 수정할 수 없다.
      const baz = []; // 다음과 같다. final bar = const [];
      ```
- Built-in types
  - 개요
    - numbers : 0, 1.2, 
    - strings : 'this is', "string", r'raw string'
    - booleans : true, false
    - lists : [1, 2, 3]
    - maps : {a: 1, b: 2}
    - runes : '\u{1f44f}'
    - symbols : #radix
  - Numbers
    - int
      - num을 상속, without decimal, 플랫폼마다 다르지만 보통 8bytes
      - ```dart
        var x = 1;
        var one = int.parse('1');
        String oneAsString = 1.toString();
        ```
    - double
      - num을 상속, include decimal 값, 8bytes
      - ```dart
        var y = 1.1;
        var onePointOne = double.parse('1.1');
        String piAsString = 3.14159.toStringAsFixed(2);
        ```
  - Strings
    - utf-16 유니코드로 이뤄진 시퀀스
    - 비교연산을 할 때 toString()으로 비교된다.
    - interpolation, multi-line 리터럴, raw 리터럴
    - ```dart
      var s1 = '''You can create
      multi-line strings like this one.
      ''';
      var s2 = """This is also a
      multi-line string.""";
      var s = r'In a raw string, not even \n gets special treatment.';
      ```
  - Booleans
    - Dart가 type safety이기 때문에 if나 assert에 boolean이 아닌 값을 사용할 수 없다.
  - Lists
    - `var list = [1, 2, 3];`
  - Maps
    - `var nobleGases = { 2: 'helium', 18: 'argon', 10: 'neon', };`
    - ```dart
      var nobleGases = Map();
      nobleGases[2] = 'helium';
      nobleGases[10] = 'neon';
      nobleGases[18] = 'argon';
      ```
    - `assert(gifts['fifth'] == null); // key가 없으면 null`
  - Runes
    - utf-32 코드 string
    - ```dart
      main() {
        var clapping = '\u{1f44f}';
        print(clapping);
        print(clapping.codeUnits);
        print(clapping.runes.toList());
        Runes input = new Runes(
          '\u2665  \u{1f605}  \u{1f60e}  \u{1f47b}  \u{1f596}  \u{1f44d}');
        print(new String.fromCharCodes(input));
      }
      ```
  - Symbols
    - 내가 쓸 일은 없을 거란다.
    - `#radix #bar`

