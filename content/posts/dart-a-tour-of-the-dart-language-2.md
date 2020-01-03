---
title: Dart 언어 여행하기 요약(2)
date: 2019-02-21 23:30:49
tags: [dart]
---

Dart A Tour of the Dart Language
https://www.dartlang.org/guides/language/language-tour

- Functions
  - 개요
    - 일급함수로 사용할 수 있다.
    - Effective Dart는 리턴형에 type 어노테이션을 사용하길 권장한다.
    - ```dart
      bool isNoble(int atomicNumber) {
        return _nobleGases[atomicNumber] != null;
      }
      ```
    - one-line은 shorthand로 쓸 수 있다. 
    - `bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;`
    - required와 optional 두 종류의 파라메터를 사용할 수 있다.
  - Optional parameters
    - optional named 파라메터
      - ```dart
        void enableFlags({bool bold, bool hidden}) {...} // 파라메터들을 named 파라메터로 선언
        enableFlags(bold: true, hidden: false); // named 인자로 사용
        ```
      - required 파라메터
        - ```dart
          const Scrollbar({Key key, @required Widget child}) // optional named 파라메터들 중 child파라메터는 꼭 넣어야 한다.
          ```
    - optional positional 파라메터
      - `String say(String from, String msg, [String device]) {...} // devide파라메터는 인자로 넣지 않아도 된다.`
    - default 파라메터값
      - optional 파라메터에 기본값이 지정돼있지 않으면 null이 default값이다.
      - ```dart
        void enableFlags({bool bold = false, bool hidden = false}) {...} // optional named 파라메터에 기본값을 지정한다.
        String say(String from, String msg,[String device = 'carrier pigeon', String mood]) {...} // optional positional 파라메터에 default값을 지정한다.
        ```
  - The main() function
    - ```dart
      void main() {...}
      void main(List<String> arguments) {...}
      ```
  - Functions as first-class objects
    - ```dart
      void printElement(int element) {
        print(element);
      }
      list.forEach(printElement); // 함수를 파라메터로 넘긴다.
      ```
    - `var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!'; // 함수 리터럴을 변수에 할당한다.`
  - Anonymous functions
    - = 람다, 클로저
    - ```dart
      list.forEach((item) {
        print('${list.indexOf(item)}: $item');
      });
      ```
  - Lexical scope
    - 안에서 curly braces {} 밖에 있는 변수에 접근할 수 있다.
    - ```dart
      bool topLevel = true;
      void main() {
        var insideMain = true;
        void myFunction() {
          var insideFunction = true;
          void nestedFunction() {
            var insideNestedFunction = true;
            assert(topLevel);
            assert(insideMain);
            assert(insideFunction);
            assert(insideNestedFunction);
          }
        }
      }
      ```
  - Lexical closures
    - 함수가 만들어진 곳 외부 {} 에서도 만들어진 곳의 변수를 사용할 수 있는 함수 = 클로저
    - ```dart
      Function makeAdder(num addBy) {
        return (num i) => addBy + i; // 클로저가 만들어질 때마다 addBy를 캡처한다.
      }
      void main() {
        var add2 = makeAdder(2); // add2 클로저
        var add4 = makeAdder(4); // aad4 클로저
        assert(add2(3) == 5);
        assert(add4(3) == 7);
      }
      ```
  - Testing functions for equality
    - 서로 다른 인스턴스 간에 같은 함수 비교는 같을 수 없다.
    - ```dart
      var v = A(); // Instance #1 of A
      var w = A(); // Instance #2 of A
      assert(v.baz != w.baz);
      ```
  - Return values
    - 모든 함수는 값을 리턴하는데, return을 명시하지 않았을 경우 null을 리턴한다.
- Operators
  - Arithmetic operators 
    - 눈에 띄는 수학연산자
      - `~/` : 몫 연산자
  - Equality and relational operators
    - `==` 연산자는 `==()` 메소드로 비교한다. 두 객체가 정확하게 일치하는지 비교하려면 `identical()` 메소드를 사용한다.
  - Type test operators
    - `as` : 타입캐스트
    - `is` : 서브클래스인지 확인. if문 안에서 스마트 캐스트를 지원한다.
  - Assignment operators
    - 눈에 띄는 할당연산자
      - `v ??= 10` : 변수가 null일 때만 할당하는 연산자. 
  - Logical operators
  - Bitwise and shift operators
  - Conditional expressions
    - 눈에 띄는 조건연산자
      - `expr1 ?? expr2` : expr1이 null이면 exp2을, null이 아니면 expr1을 반환한다. 다른 언어에선 `?:`로 쓰기 때문에 elvis 연산자라고도 부른다.
  - Cascade notation
    - ```dart
      void main() {
        querySelector('#sample_text_id')
          ..text = 'Click me!'
          ..onClick.listen(reverseText); // 한 객체의 여러 멤버의 연산을 수행하게끔 해주는 문법 like 메소드체이닝
      }
      ```
  - Other operators
    - 눈에 띄는 조건멤버연산자
      - `foo?.bar` :  foo가 null이면 null을 반환. safe call이라고 부른다.
- Control flow statements
  - If and else
  - For loops
    - for 루프 : 자바랑 다르지 않다. forEach()도 지원한다. List나 Set에서 for-in 표기법을 사용할 수도 있다.
    - ```dart
      candidates
        .where((c) => c.yearsExperience >= 5) // Iterable을 다룰 때 break를 구현하려면 where()를 쓴다.
        .forEach((c) => c.interview());
      ```
  - While do-while
  - Break and continue
  - Switch and case
    - case값으로 상수만 사용할 수 있다.
    - ```dart
      var command = 'CLOSED';
      switch (command) {
        case 'CLOSED':
          executeClosed();
          continue nowClosed;
        // 특정 작업 후 다음 case에도 걸리게 하려면 래이블을 사용한다.
        nowClosed:
        case 'NOW_CLOSED':
          // Runs for both CLOSED and NOW_CLOSED.
          executeNowClosed();
          break;
      }
      ```
  - Assert
    - `assert`는 개발코드에서만 동작한다. 특히 Flutter에선 디버그 모드에서만 동작한다.
    - `assert(urlString.startsWith('https'), 'URL ($urlString) should start with "https".'); // assert에 걸렸을 때 메시지를 지정할 수도 있다.`
- Exceptions
  - 개요
    - try~[on] catch~finally
    - Java와 달리 Exception이 unchecked라서 메소드 선언에 어떤 Exception을 던질지 쓸 필요가 없다.
    - `Exception`과 `Error` 두 종류가 있고, null이 아닌 어떤 객체든 throw 할 수 있다.
  - Throw
  - Catch
    - ```dart
      try {
        breedMoreLlamas();
      } on OutOfLlamasException { // 특정 Exception 잡기
        buyMoreLlamas();
      } catch(e, s) { // 파라메터를 하나도 받지 않는 것부터 2개 받는 것까지 선택할 수 있다.
        print('Exception details:\n $e');
        print('Stack trace:\n $s');
      }
      ```
    - `rethrow`를 지원한다.
  - Finally