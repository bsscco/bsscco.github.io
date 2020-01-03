---
title: 서버사이드 Dart(5) - 서버사이드 시작하기
date: 2019-03-05 08:49:14
tags: [dart]
---

# Get Started with Server-Side Dart
https://www.dartlang.org/tutorials/server/get-started

### What's the point?
- Dart SDK에는 개발도구들과 라이브러리들이 담겨있다.
- 앱을 만들기 위해 WebStorm과 같은 IDE를 써라.
- 모든 Dart 앱들은 `main()` 함수에서 시작한다.
- Dart는 top-level 함수를 지원한다.  

## Download Dart and an IDE
[Get the Dart SDK](https://www.dartlang.org/tools/sdk#install)

## What did you get?
다운로드한 **Dart SDK** 보면 그 안에서 bin 디렉토리의 도구들과 lib 디렉토리의 라이브러리들을 볼 수 있다. 

lib 디렉토리엔 dart:core, dart:html, dart:io와 같은 라이브러리가 들어있다. bin 디렉토리엔 **pub** 패키지 관리자, Dart VM의 커맨드라인 버전 같은 유용한 커맨드라인 도구들이 들어있다.

**WebStorm**에는 Dart 플러그인이 이미 설치돼있다. 이 플러그인을 사용하려면 약간의 설정만 해주면 된다.     

## About Dart apps
대부분의 미니멀한 Dart 앱은 다음을 따른다.:
- .dart 확장자를 가지는 하나의 Dart 소스파일
- 하나의 top-level main() 함수. 이 함수는 앱의 엔트리 포인트다.

단순한 Dart 앱은 커맨드라인에서 Dart VM인 `dart`를 사용해서 실행시킬 수 있다.
```dart
dart main.dart
```
**이보다 복잡한 앱은 pubspec 파일을 함께 가져야 한다. pubspec.yaml 파일은 앱의 이름과 설명을 포함한다.** 예를 들면:
```
name: examples
descriptions: dartlang.org example code.
```

### Pub package manager
pub 도구는 Dart 패키지를 관리할 수 있게 해준다. Pub은 그밖에도 Dart앱을 생성, 개발, 실행, 배포하는 명령도 포함한다.

Pub은 앱의 의존성들과 특별히 설정되어야 하는 정보를 알기 위해 pubspec.yaml 파일을 사용한다. Pub은 Dart 앱의 파일과 디렉토리들이 일정한 규칙으로 분류돼있다고 간주한다. 다음 다이어그램은 pub에 의해 사용되는 컨벤션을 보여준다. 이 디렉토리들이 꼭 필요한 것은 아니다.

![pub-directory-structure.png](https://www.dartlang.org/tutorials/server/images/pub-directory-structure.png)

**bin**
커맨드라인 앱의 주요 파일들이 들어있다. 이 파일들 중 하나는 반드시 top-level의 main() 함수를 포함해야 한다.

**lib**
앱에서 사용하는 추가적인 코드

**pubspec.yaml**
앱의 의존성, 버전, 정보 등을 포함하는 메타데이터

### Command-line apps
Dart 커맨드라인 앱들은 커맨드라인에서 독자적으로 실행될 수 있다. 커맨드라인 앱들은 주로 웹앱에 서버사이드를 지원하기 위해 실행되지만 스크립트가 될 수도 있다.

Dart VM은 Dart 코드를 중간 컴파일 단계 없이 직접 실행할 수 있다.

![dartvm-cmd-line.png](https://www.dartlang.org/tutorials/server/images/dartvm-cmd-line.png)
 
## Create a command-line app
1. WebStorm을 켠다. 
2. 만약 처음 WebStorm을 실행했다면, SDK 경로를 설정해줘야 한다. [Configuring Dart support](https://webdev.dartlang.org/tools/webstorm#configuring-dart-support)를 참고하자.
3. Create New Project를 선택하자. 
4. Dart를 선택하자.
5. untitled 를 hello_world로 바꾸자. 이 이름은 앱의 디렉토리 이름과 패키지 이름으로 사용된다. 일반적으로 lowercase_with_underscore를 따른다.
6. Generate sample content를 체크하자.
7. Console app을 선택하자.
8. Create를 누른다.

이제 WebStorm은 boilerplate 파일들이 담긴 hello_world 디렉토리를 생성할 것이다. 그 디렉토리에서 pub get을 실행시켜서 앱이 의존하는 패키지들을 다운로드 받는다.

hello_world 디렉토리가 포함하는 파일과 디렉토리들은 다음과 같다. :

**.dart_tool**
pub과 다른 Dart 도구들이 사용하는 지원파일들이 들어있다.

**bin**
앱 소스파일들이 들어있다. main.dart 파일도 여기 있다.

**pubspec.yaml**
앱에 필요한 패키지들이 선언돼있다.

**pubspec.lock**
앱에 필요한 패키지들의 버전이 적혀있다.

**lib**
hello_world.dart 같은 라이브러리 파일들이 들어있다.

**.packages**
앱에서 사용되는 패키지들이 들어있다. 이 파일들은 pub get에 의해 다운로드 받은 파일들이다.

## Run a command-line app
WebStorm에서 Run 버튼을 눌러 실행시킬 수 있다.

커맨드라인에서도 실행시킬 수 있다.
```
pub run bin/main.dart
``` 

## About main() and other top-level functions
Dart의 모든 앱들은 최소 하나 이상의 main() 함수를 포함해야 한다. 위 예제에서 봤던 `print()`는 `dart:core`에 들어있는 top-level 함수다.

## About file naming conventions
앱 이름은 일반적으로 lowercase_with_underscore를 따른다.

## Other resources
[WebStorm](https://webdev.dartlang.org/tools/webstorm), [pub](https://www.dartlang.org/tools/pub)

## What next?
[Write Command-Line apps](/2019/03/05/dart-write-command-lin-apps)
