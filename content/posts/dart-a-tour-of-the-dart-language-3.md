---
title: Dart 언어 여행하기 요약(3)
date: 2019-02-24 22:44:28
tags: [dart]
---

Dart A Tour of the Dart Language
https://www.dartlang.org/guides/language/language-tour

- Classes
  - 개요
    - Dart 클래스는 단일상속과 mixin을 지원한다. 모든 클래스는 Object의 후손이다.
  - Using class members
  - Using constructors
    - ```dart
      var p1 = Point(2, 2); // 일반 생성자
      var p2 = Point.fromJson({'x': 1, 'y': 2}); // named 생성자
      ```
    - `var p = const ImmutablePoint(2, 2); // 생성자 앞에 const를 붙이면 컴파일타임에 상수로 만들어버린다. 상수화가 가능한 클래스가 되려면 조건을 만족해야 한다.`
    - ```dart
      var a = const ImmutablePoint(1, 1);
      var b = const ImmutablePoint(1, 1);
      assert(identical(a, b)); // 상수화된 두 객체는 정확히 일치한다.
      ```
    - ```dart
      const pointAndLine = const {
        'point': const [const ImmutablePoint(0, 0)],
        'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
      };
      const pointAndLine = { // 변수 선언 시 const만 지정해도 초기화 값들 전부 상수가 된다.
        'point': [ImmutablePoint(0, 0)],
        'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
      };
      ```
  - Getting an object’s type
    - `print('The type of a is ${a.runtimeType}'); // 객체의 타입(Type객체)을 가져온다`
  - Instance variables
    - final이 아닌 인스턴스 변수는 getter와 setter가 암시적으로 생성된다.
    - final인 인스턴스 변수는 getter만 생성된다.
    - `assert(point.x == 4); // x를 위한 getter 메소드를 사용한 예`
  - Constructors
    - ```dart
      class Point {
        num x, y;
        Point(num x, num y) {
          this.x = x;
          this.y = y;
        }
        Point(this.x, this.y); // 위 생성자와 같은 동작을 한다.
      }
      ```
    - sub class는 super class의 생성자를 상속하지 않는다.
    - ```dart
      // named 생성자를 사용하면 생성자의 의미가 더 명확해진다.
      class Point {
        num x, y;
        Point(this.x, this.y);
        Point.origin() {
          x = 0;
          y = 0;
        }
      }
      ```
    - Initializer list, super class 생성자, sub class 생성자 순으로 실행된다.
    - ```dart
      // sub class의 생성자에서 super class의 생성자를 명시적으로 호출할 수 있다.
      Employee.fromJson(Map data) : super.fromJson(data) { 
        print('in Employee');
      }
      ```
    - ```dart
      // 생성자 body가 실행되기 전에 initializer list나 assert를 실행할 수 있다.
      // initializer list는 final 멤버변수를 초기화할 때 유용하다.
      Point.fromJson(Map<String, num> json) : x = json['x'], y = json['y'] {
        print('In Point.fromJson(): ($x, $y)');
      }
      Point.withAssert(this.x, this.y) : assert(x >= 0) {
        print('In Point.withAssert(): ($x, $y)');
      }
      ```
    - `Point.alongXAxis(num x) : this(x, 0); // 생성자를 redirecting 할 수 있다.`
    - ```dart
      // 클래스의 모든 멤버가 const이면 컴파일 타임에 const 객체로 만들 수 있는 조건을 충족한다.
      class ImmutablePoint {
        static final ImmutablePoint origin = const ImmutablePoint(0, 0);
        final num x, y;
        const ImmutablePoint(this.x, this.y);
      }
      ```
    - ```dart
      // 싱글톤이나 풀을 만들고 싶을 때 factory 생성자를 사용한다.
      factory Logger(String name) {
        if (_cache.containsKey(name)) {
          return _cache[name];
        } else {
          final logger = Logger._internal(name);
          _cache[name] = logger;
          return logger;
        }
      }
      ```
  - Method
    - 암시적으로 생성되는 getter와 setter는 나중에 wrapping해야 할 상황이 생길 때 유용하다.
    - ```dart
      abstract class Doer {
        // abstract 메소드를 가지면 abstract class가 된다.
        void doSomething();
      }
      ```
  - Abstract class
    - factory 생성자를 사용하면 abstract 클래스는 인스턴스화 할 수 있다.
  - Implicit interfaces
    - 모든 class는 자신의 인스턴스 멤버를 포함하는 interface를 암시적으로 정의한다. 만약 class A를 지원하는 class B를 만들어서 다형성을 이용하려면 `class B implements A`와 같이 구현하면 된다.
    - ```dart
      class Person {
        final _name;
        Person(this._name);
        String greet(String who) => 'Hello, $who. I am $_name.';
      }
      class Impostor implements Person {
        get _name => ''; // _name의 getter를 구현한다.
        String greet(String who) => 'Hi $who. Do you know who I am?'; // greet()를 구현한다.
      }
      String greetBob(Person person) => person.greet('Bob'); // 다형성을 이용하는 함수
      void main() {
        print(greetBob(Person('Kathy')));
        print(greetBob(Impostor()));
      }
      ```
  - Extending a class
    - ```dart
      class SmartTelevision extends Television { // 상속
        @override // override 어노테이션
        void turnOn() {...}
      }
      ```
    - 눈에 띄는 overridable 연산자
      - `[]`
      - `[]=`
      - `==` : ==를 override 하려면 hashcode의 getter도 override 해야 한다.
      - `+`
    - ```dart
      class A {
        // Unless you override noSuchMethod, using a
        // non-existent member results in a NoSuchMethodError.
        @override
        void noSuchMethod(Invocation invocation) {
          print('You tried to use a non-existent member: ' +
            '${invocation.memberName}');
        }
      }
      ```
    - [noSuchMethod forwarding specification](https://github.com/dart-lang/sdk/blob/master/docs/language/informal/nosuchmethod-forwarding.md)
  - Enumerated types
    - ```dart
      enum Color { red, green, blue }
      assert(Color.red.index == 0);
      ```
  - Adding features to a class: mixins
    - mixin은 클래스 코드를 재사용할 수 있게 해주는 방법이다.
    - ```dart
      // mixin을 구현하려면 Object만 상속해야 하고, 생성자가 없어야 한다.
      mixin Musical {
        bool canPlayPiano = false;
        bool canCompose = false;
        bool canConduct = false;
        void entertainMe() {
          if (canPlayPiano) {
            print('Playing piano');
          } else if (canConduct) {
            print('Waving hands');
          } else {
            print('Humming to self');
          }
        }
      }
      ```
    - ```dart
      // 특정 클래스만 mixin 될 수 있게 하려면 on 키워드를 사용한다.
      mixin MusicalPerformer on Musician {
        // ···
      }
      ```
  - Class variables and methods
    - 정적 변수, 정적 메소드 지원
    - Dart 스타일 가이드는 상수를 lowerCamelCase로 이름짓기를 권장한다.
    - 공통적으로, 유틸리티처럼 사용되길 원한다면 정적 메소드 대신에 일급함수를 고려하자.
