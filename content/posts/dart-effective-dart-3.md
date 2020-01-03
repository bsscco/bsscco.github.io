---
title: 이펙티브 Dart(3) - 문서화 가이드
date: 2019-03-02 08:05:06
tags: [dart]
---

Effective Dart: Documentation
https://www.dartlang.org/guides/language/effective-dart/documentation

간결하고 정확한 주석은 몇초면 쓸 수 있고, 여러 사람의 코드파악 시간을 아껴준다. 

- [Comments](/2019/03/02/effective-dart-3/#Comments)
  - **DO** 주석은 문장으로 써라.
  - **DON'T** 문서화를 위해 block 주석(`/**/`)을 쓰지 마라. 

- [Doc comments](/2019/03/02/effective-dart-3/#Doc-comments)
  - **DO** 멤버와 타입의 문서화를 위해 doc 주석(`///`)을 사용해라. 
  - **PREFER** public API를 위한 doc 주석을 써라.
  - **CONSIDER** library를 위한 doc 주석 쓰기를 고려해라.
  - **CONSIDER** private API를 위한 doc 주석 쓰기를 고려해라.
  - **DO** doc 주석을 쓸 때 한줄의 문장으로 된 요약으로 시작해라.
  - **DO** doc 주석의 첫 문장과 나머지 문장들 사이를 구분해라.
  - **AVOID** 중복설명을 피해라.
  - **PREFER** 제 3자의 시점에서 함수나 메소드의 주석을 써라.
  - **PREFER** 변수, 게터, 세터 주석은 명사구로 시작해라.
  - **PREFER** 라이브러리나 타입 주석은 명사구로 시작해라.
  - **CONSIDER** doc 주석에 샘플코드 넣는 걸 고려해라.
  - **DO** doc 주석에서 in-scope identifier를 참조시키려면 square brackets[]를 사용해라.
  - **DO** 파라메터, 리턴값, 예외 설명은 산문체로 써라. 
  - **DO** 메타데이터 annotation 이전 위치에 doc 주석을 써라.
  
- [Markdown](/2019/03/02/effective-dart-3/#Markdown)
  - **AVOID** 마크다운을 지나치게 사용하는 건 피해라. 
  - **AVOID** 포맷팅을 위한 HTML은 피해라. 
  - **PREFER** 코드 블록을 표시하기 위해 backtick fences(` * 3개)를 사용해라. 

- [Writing](/2019/03/02/effective-dart-3/#Writing)
  - **PREFER** 명확하고 정확하게 짧게 써라.
  - **AVOID** 축약어는 피해라.
  - **PREFER** 인스턴스 멤버를 가리킬 땐 the 대신에 this를 써라. 

## Comments
##### **DO** format comments like sentences.
주석은 문장으로 써라.
```dart
// Not if there is nothing before it. <= 문장 양식으로 쓴 주석
if (_chunks.isEmpty) return false;
```

##### **DON’T** use block comments for documentation.
문서화를 위해 block 주석(/**/)을 쓰지 마라. block 주석에서 여분의 첫, 마지막 줄(/* */)은 깔끔하지 않기 때문에 Dart는 한줄 주석을 선호한다. 
```dart
// Assume we have a valid name. (O)
/* Assume we have a valid name. (X) */ 
  print('Hi, $name!');
```
코드 섹션 밖에 일시적으로 주석을 남기기 위해서 block 주석을 사용하는 건 괜찮다.

## Doc comments
[dartdoc](https://github.com/dart-lang/dartdoc)

##### **DO** use /// doc comments to document members and types.
멤버와 타입의 문서화를 위해 doc 주석(`///`)을 사용해라.
 ```dart
/// The number of characters in this chunk when unsplit. (O)
// The number of characters in this chunk when unsplit. (X)
int get length => ...
```

##### **PREFER** writing doc comments for public APIs.
public API를 위한 doc 주석을 쓰길 권장한다.
 
 ##### **CONSIDER** writing a library-level doc comment.
library를 위한 doc 주석을 쓰는 걸 고려해라.
- 라이브러리가 무엇을 하는지 한줄로 요약해라. 
- 라이브러리에 사용되는 용어를 설명해라.
- 라이브러리의 API로 동작하는 완벽한 코드 샘플들을 제공해라. 
- 대부분의 중요하고 공통적인 클래스나 함수들에 대한 링크를 제공해라.
- 라이브러리가 의존하는 외부 참조들에 대한 링크를 제공해라.
위 내용들을 `library` 지시어 위에 위치시켜라.

##### **CONSIDER** writing doc comments for private APIs.
private API를 위한 doc 주석을 쓰는 걸 고려해라.

##### **DO** start doc comments with a single-sentence summary.
doc 주석을 쓸 때 한줄의 문장으로 된 요약으로 시작해라. 읽을 사람이 이 주석을 계속 읽을지 말지 결정할 수 있을 만큼의 충분한 내용문 한줄로 써야 한다.
```dart 
// (O)
/// Deletes the file at [path] from the file system.
void delete(String path) { ... }
```
```dart
// (X)
/// Depending on the state of the file system and the user's permissions,
/// certain operations may or may not be possible. If there is no file at
/// [path] or it can't be accessed, this function throws either [IOError]
/// or [PermissionError], respectively. Otherwise, this deletes the file.
void delete(String path) { ... }
```

##### **DO** separate the first sentence of a doc comment into its own paragraph.
doc 주석의 첫 문장과 나머지 문장들 사이를 구분해라.
```dart
/// Deletes the file at [path].
///
/// Throws an [IOError] if the file could not be found. Throws a
/// [PermissionError] if the file is present but could not be deleted.
void delete(String path) { ... }
```

##### **AVOID** redundancy with the surrounding context.
중복설명을 피해라.

##### **PREFER** starting function or method comments with third-person verbs.
제 3자의 시점에서 함수나 메소드의 주석을 쓰는 걸 권장한다.
```dart
/// Returns `true` if every element satisfies the [predicate].
bool all(bool predicate(T element)) => ...

/// Starts the stopwatch if not already running.
void start() { ... }
``` 

##### **PREFER** starting variable, getter, or setter comments with noun phrases.
변수, 게터, 세터 주석은 명사구로 시작하는 걸 권장한다. 게터, 세터 둘 다 doc 주석을 다는 것은 피해라. dartdoc은 하나만 보여줄 것이다.
```dart
/// The current day of the week, where `0` is Sunday.
int weekday;

/// The number of checked buttons on the page.
int get checkedCount => ...
```

##### **PREFER** starting library or type comments with noun phrases.
라이브러리나 타입 주석은 명사구로 시작하는 걸 권장한다.
```dart
/// A chunk of non-breaking output text terminated by a hard or soft newline.
///
/// ...
class Chunk { ... }
```

##### **CONSIDER** including code samples in doc comments.
doc 주석에 샘플코드 넣는 걸 고려해라. 예제가 있으면 API를 배우기 쉽다. 
```dart
/// Returns the lesser of two numbers.
///
/// `` `dart
/// min(5, 3) == 3
/// `` `
num min(num a, num b) => ...
```

##### **DO** use square brackets in doc comments to refer to in-scope identifiers.
doc 주석에서 in-scope identifier를 참조시키려면 square brackets[]를 사용해라.
 ```dart
/// Throws a [StateError] if ...
/// similar to [anotherMethod()], but ...
```

##### **DO** use prose to explain parameters, return values, and exceptions.
파라메터, 리턴값, 예외 설명은 산문체로 써라. squre brackets[]를 쓰면 파라메터를 하이라이팅이 돼서 가독성이 떨어지지 않기 때문이다. 
```dart
// (X)
/// Defines a flag with the given name and abbreviation.
///
/// @param name The name of the flag.
/// @param abbr The abbreviation for the flag.
/// @returns The new flag.
/// @throws ArgumentError If there is already an option with
///     the given name or abbreviation.
Flag addFlag(String name, String abbr) => ...
``` 
```dart
// (O)
/// Defines a flag.
///
/// Throws an [ArgumentError] if there is already an option named [name] or
/// there is already an option using abbreviation [abbr]. Returns the new flag.
Flag addFlag(String name, String abbr) => ...
```

##### **DO** put doc comments before metadata annotations.
메타데이터 annotation 이전 위치에 doc 주석을 써라.
```dart
/// A button that can be flipped on and off.
@Component(selector: 'toggle')
class ToggleComponent {}
```

## Markdown
doc 주석에 대부분의 마크다운 포맷을 사용할 수 있다.

##### **AVOID** using markdown excessively.
마크다운을 지나치게 사용하는 건 피해라. 마크다운으로 렌더링하기 전에는 보기가 어렵기 때문이다.

##### **AVOID** using HTML for formatting.
포맷팅을 위한 HTML은 피해라. 테이블을 만들기 위해 HTML을 사용할 수 있지만 보기가 어려워진다.

##### **PREFER** backtick fences for code blocks.
코드 블록을 표시하기 위해 backtick fences(` * 3개)를 사용해라. 들여쓰기로도 표시할 수 있지만 들여쓰기는 마크다운 리스트를 표시할 때 사용하는 게 일반적이기 때문이다.
```dart
/// You can use [CodeBlockExample] like this:
///
/// `` `
/// var example = CodeBlockExample();
/// print(example.isItGreat); // "Yes."
/// `` `
```

## Writing
##### **PREFER** brevity.
명확하고 정확하게 짧게 써라.

##### **AVOID** abbreviations and acronyms unless they are obvious.
축약어는 피해라. 많은 사람들은 “i.e.”, “e.g.” and “et al.” 이런 축약어의 뜻을 모른다.

##### **PREFER** using “this” instead of “the” to refer to a member’s instance.
인스턴스 멤버를 가리킬 땐 the 대신에 this를 써라. the object라고 쓰면 무엇을 가리키는 건지 헷갈릴 수 있기 때문이다.
```dart
class Box {
  /// The value this wraps.
  var _value;

  /// True if this box contains a value.
  bool get hasValue => _value != null;
}
```