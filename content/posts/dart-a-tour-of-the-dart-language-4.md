---
title: Dart 언어 여행하기 요약(4)
date: 2019-02-25 08:55:16
tags: [dart]
---

Dart A Tour of the Dart Language
https://www.dartlang.org/guides/language/language-tour

- Generics
  - Why use generics?
    - 타입을 명시해서 컴파일타임에 에러를 잡아낼 수 있다.
    - 여러 타입에 사용해야 해서 코드를 중복작성해야 할 때 중복 코드를 없앤다.
  - Using collection literls
    - `<String>['Seth', 'Kathy', 'Lars'];`
  - Using parameterized types with constructors
    - `var names = List<String>();`
  - Generic collections and the types they contain
    - `print(names is List<String>); // Java는 List까지만 체크되지만 Dart에선 제네릭 타입까지 체크가 된다.`
  - Restricting the parameterized type
    - ```dart
      // T는 SomeBaseClass 또는 sub class 들만 대상으로 강제한다.
      class Foo<T extends SomeBaseClass> {
        String toString() => "Instance of 'Foo<$T>'";
      }
      ```
  - Using generic methods
    - ```dart
      // 첫번째 <T>가 리턴타입, 파라메터 ts의 타입, 지역변수 tmp의 타입으로 사용된다.
      T first<T>(List<T> ts) {
        T tmp = ts[0];
        return tmp;
      }
      ```
- LIbraries and visibility
  - Using libraries
    - ```dart
      import 'dart:html'; // built-in 라이브러리는 dart 스키마를 사용한다.
      import 'package:test/test.dart'; // pub에서 가져온 라이브러리는 package 스키마를 사용한다. 다른 라이브러리는 파일패스 형식(URI)으로 명시하면 된다.
      ```
    - 두 라이브러리 안에서 같은 identifier가 있을 땐 as로 한쪽 라이브러리의 별명을 지어주면 구별해 사용할 수 있다.
    - ```dart
      import 'package:lib1/lib1.dart' show foo; // foo만 보이게 하기
      import 'package:lib2/lib2.dart' hide foo; // foo만 숨기기
      ```
    - Lazy loading의 이점
      - 앱의 스타트업 시간을 줄일 수 있다.
      - 예를 들어, 외부 알고리즘의 A/B 테스팅을 할 수 있다.
      - 추가 스크린이나 다이알로그 같이 자주 사용하지 않는 기능을 필요할 때 로드할 수 있다.
    - ```dart
      import 'package:greetings/hello.dart' deferred as hello; // deferred as 필수
      Future greet() async { // 동적 로딩하는 함수에 async 필수
        await hello.loadLibrary();
        hello.printGreeting();
      }
      ```
    - loadLibrary()를 여러 번 호출해도 딱 한 번만 로딩되므로 걱정할 필요 없다.
  - Implementing libraries
    - [Create Library Packages](https://www.dartlang.org/guides/libraries/create-library-packages)
    - export, part, library 지시어
- Asynchrony support
  - Handling Futures
    - 완료된 Future의 값이 필요할 땐 async await를 사용하거나 Future API를 사용한다.
    - ```dart
      Future checkVersion() async {
        var version = await lookUpVersion(); // await는 비동기 함수의 결과를 기다린다.
      }
      ```
    - ```dart
      try {
        version = await lookUpVersion();
      } catch (e) {
        // try~catch~finally문도 사용할 수 있다.
      }
      ```
    - `await expression`에서 `expression`은 보통 Future를 리턴하는데, 그렇지 않다면 자동으로 Future로 레핑된다. `await expression`의 값은 리턴된 값이 된다. 
  - Declare async functions
    - ```dart
      // 리턴타입은 Future를 사용해야 하고, 함수명 뒤에 async를 붙여줘야 한다. 반환값이 Future가 아니어도 Future로 레핑해준다.
      Future<String> lookUpVersion() async => '1.0.0';
      ```
    - 아무것도 반환하지 않는 함수는 리턴형에 `Future<void>`로 써준다.
  - Handling Streams
    - 완료된 Stream의 값이 필요할 땐 async ~ await for를 사용하거나 Stream API를 사용한다.
    - UI event listeners에는 await for를 사용하지 말아야 한다. UI 프레임워크는 이벤트를 끝없이 발생시키기 때문이다.
    - ```dart
      await for (varOrType identifier in expression) {
        // 스트림을 끝내고 싶을 땐 break나 return을 사용하면 된다.
      }
      ```
    - [Dart Language Asynchrony Support: Phase 1](https://www.dartlang.org/articles/language/await-async)
- Generators
  - 동기 제너레이터 : Iterator 객체를 반환한다.
  - 비동기 제너레이터 : Stream 객체를 반환한다.
  - ```dart
    Iterable<int> naturalsTo(int n) sync* { // 동기 제너레이터를 만들 땐 sync*를 쓴다.
      int k = 0;
      while (k < n) yield k++; // 값을 전달하기 위해 yield를 쓴다.
    }
    ```
  - ```dart
    Stream<int> asynchronousNaturalsTo(int n) async* { // 비동기 제너레이터를 만들 땐 async*를 쓴다.
      int k = 0;
      while (k < n) yield k++;
    }
    ```
  - ```dart
    Iterable<int> naturalsDownFrom(int n) sync* {
      if (n > 0) {
        yield n;
        yield* naturalsDownFrom(n - 1); // recursive 함수일 땐 yield*를 쓰면 성능을 높힐 수 있다.
      }
    }
    ```
  - [Dart Language Asynchrony Support: Phase 2](https://www.dartlang.org/articles/language/beyond-async)
- Callable classes
  - ```dart
    class WannabeFunction {
      call(String a, String b, String c) => '$a $b $c!'; // call()을 정의하면 클래스를 함수처럼 쓸 수 있다.
    }
    main() {
      var wf = WannabeFunction();
      var out = wf("Hi","there,","gang");
      print('$out');
    }
    ```
  - [Emulating Functions in Dart](https://www.dartlang.org/articles/language/emulating-functions)
- Isolates
  - 개발자들은 전통적으로 메모리를 공유하는 멀티스레드를 사용해왔지만, 메모리공유 스레드 방식은 에러와 복잡한 코드를 만들어내기 쉽다. Dart는 각각이 고유의 메모리힙을 가지는 Isolates를 제공한다.
  - [https://www.dartlang.org/guides/language/language-tour#isolate](https://api.dartlang.org/stable/dart-isolate)
- Typedefs
  - ```dart
    // typedef를 사용하지 않은 예
    class SortedCollection {
      Function compare; // formal한 타입명시
      SortedCollection(int f(Object a, Object b)) { // 읽기 어려운 파라메터형
        compare = f;
      }
    }
    int sort(Object a, Object b) => 0;
    void main() {
      SortedCollection coll = SortedCollection(sort);
      assert(coll.compare is Function);
    }
    ```
  - ```dart
    // typedef를 사용한 예
    typedef Compare = int Function(Object a, Object b); // 타입이 더 명시적으로 변했다.
    class SortedCollection {
      Compare compare;
      SortedCollection(this.compare); // 파라메터도 읽기 쉬워졌다.
    }
    int sort(Object a, Object b) => 0;
    void main() {
      SortedCollection coll = SortedCollection(sort);
      assert(coll.compare is Function);
      assert(coll.compare is Compare); // 명시적인 타입을 확인할 수 있게 됐다.
    }
    ```
  - 아직까지는 함수에만 typedef를 사용할 수 있는데 앞으로 더 확대할 예정이다.
- Matadata
  - @로 시작하는 Annotation들은 코드에 추가적인 정보를 준다.
  - @deprecated, @override, ...
  - ```dart
    // 어노테이션을 직접 만들 수 있다.
    library todo;
    class Todo {
      final String who;
      final String what;
      const Todo(this.who, this.what);
    }
    ----------------------------------------------
    import 'todo.dart';
    @Todo('seth', 'make this do something')
    void doSomething() {
      print('do something');
    }
    ```
  - 메타데이터는 거의 모든 곳에서 사용할 수 있고, 런타임에 리플렉션 기능을 사용하면 retrieve 할 수도 있다.
- Comments
  - `// 한줄 주석`
  - `/// 한줄 문서화 주석`
  - ```dart
    /*
     * 여러 줄 주석
    Llama larry = Llama();
     */
    ```
  - ```dart
    /**
     * 여러 줄 문서화 주석 
    Feeds your llama [Food]. brackets 안에 클래스, 메소드, 필드, 일급변수, 일급함수, 파라메터 등을 넣으면 문서화할 때 lexical scope에 맞게 링크로 처리된다.
    Llama larry = Llama();
     */
    ```
  - [documentation generation tool](https://github.com/dart-lang/dartdoc#dartdoc)
- Summary
  - [Dart Language Specification](https://www.dartlang.org/guides/language/spec)
  - [Effective Dart](https://www.dartlang.org/guides/language/effective-dart)
  - [A Tour of the Dart Libraries](https://www.dartlang.org/guides/libraries/library-tour)

