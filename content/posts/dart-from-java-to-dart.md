---
title: Flutter 코드랩 리뷰 - 자바에서 다트로 넘어가기
date: 2019-02-14 23:02:51
tags: [dart]
---

Java에서 Dart로 넘어가기
https://codelabs.developers.google.com/codelabs/from-java-to-dart/index.html
- 생성자
  - ```dart
    // 다음 두 생성자는 완전히 같은 동작을 한다.
    Bicycle(this.cadence); // 이거 추천
    Bicycle(int cadence) {
      this.cadence = cadence;
    }
    ```
- 변수 선언
  - ```dart
    var bike = bicycle(1, 2, 0); // 변수 생성
    final bike = bicycle(1, 2, 0); // 상수 생성
    ```
- toString()
  - ```dart
    @override
    String toString() => 'Bicycle: $speed mph';
    ```
- String
  - single qoutes, double qoutes 둘 다 사용 가능하다.
  - string interpolation을 지원한다. `'speed: $speed' // $var, ${exp}`
- 함수&메소드
  - one-line 함수/메소드를 지원한다.
- private 멤버
  - ```dart
    int _speed = 0;
    int get speed => _speed; // getter
    ```
- named 파라메터 & default값
  - ```dart
    Rectangle({this.origin, this.width, this.height}); // curly braces로 감싸면 named 파라메터를 사용하는 생성자
    Rectangle({this.origin = const Point(0, 0), this.width = 0, this.height = 0}); // named파라메터와 default값을 함께 사용한 생성자
    ```
- Factory
  - factory 패턴의 장점
    - factory 메소드에서 상위타입을 반환함으로써 객체 생성의 자세한 부분을 숨길 수 있다.
    - 있는 객체 재사용 (ex. singleton)
  - (전역)함수를 통한 구현
    - ```dart
      Shape shapeFactory(String type) {
        if (type == 'circle') return Circle(2);
        if (type == 'square') return Square(2);
        throw 'Can\'t create $type.';
      }
      ```
  - factory 생성자로 구현
    - ```dart
      abstract class Shape {
        factory Shape(String type) {
          if (type == 'circle') return Circle(2);
          if (type == 'square') return Square(2);
          throw 'Can\'t create $type.';
        }
        num get area;
      }
      ```
- abstract 클래스
  - ```dart
    abstract class Shape {
      num get area;
    }
    ```
- throw~try~catch
  - ```dart
    throw 'This is exception.';
    throw ArgumentError();
    try {
      ...
    } catch(err) {
      ...
    }
    ```
- 인터페이스
  - Dart에서 모든 클래스는 인터페이스이기 때문에 interface 키워드가 없다.
  - ```dart
    class CircleMock implements Circle {
      num area; // Circle의 area getter를 꼭 구현해야 한다.
      num radius; // Circle의 radius 멤버변수를 꼭 구현해야 한다.
    }
    ```
- functional programming
  - functional의 기능
    - 함수를 인자로 넘길 수 있다.
    - 함수를 변수에 할당할 수 있다.
    - 여러 인자를 취하는 함수를 인자 하나만 취하는 함수들의 시퀀스로 분해할 수 있다.
    - 람다식이라 불리는 이름없는 함수를 생성할 수 있다.
  - Dart에서 function은?
    - Function 타입의 객체다.
  - ```dart
    final values = [1, 2, 3, 5, 10, 50];
    values.map((length) => "A${'a' * length}h!").forEach((it)=>print(it));
    ```
