---
title: 서버사이드 Dart(4) - dart:io 라이브러리 여행하기
date: 2019-03-03 22:16:46
tags: [dart]
---

# A Tour of the dart:io Library
https://www.dartlang.org/server/io-library-tour

`dart:io`는 files, directories, processes, sockets, WebSockets, and HTTP clients and servers를 다루는 API를 제공한다.

> note: 웹서버를 위해 쓸 수 있지만 웹앱을 위한 라이브러리는 아니다.

`dart:io` 라이브러리는 비동기 지향적으로 API를 구현했다. 동기 메소드는 앱을 쉽게 블로킹 시키기 때문이다. 대부분의 연산들은 Future 또는 Stream을 리턴한다. 이런 패턴은 모던 서버 플랫폼인 `Node`와 같다.

`import 'dart:io';` dart:io를 사용하려면 먼저 import 해야 한다.  

## Files and directories
`dart:io`는 커맨드라인 앱에서 파일을 읽고 쓰거나 디렉토리들을 탐색하는 데 사용할 수 있다. 파일 내용을 읽기 위한 2가지 선택지가 있다. 하나는 한 번에 다 읽어오는 것이고, 다른 하나는 스트리밍으로 읽어오는 것이다. 한 번에 다 읽어오려면 충분한 메모리가 필요하다. 만약, 파일이 매우 크거나 파일내용을 읽는 동안 다른 작업을 하고 싶다면 Stream을 사용해야 한다. 

### Reading a file as text
utf-8을 사용해서 텍스트파일을 읽을 때 `readAsString()`으로 전체 내용을 읽어올 수 있다. 줄별로 끊어서 읽고 싶을 땐 `readAsLines()`를 사용한다. 두 메소드는 `Future` 객체를 반환한다.
```dart
Future main() async {
  var config = File('config.txt');
  var contents;

  contents = await config.readAsString();
  print('The file is ${contents.length} characters long.');

  contents = await config.readAsLines();
  print('The file is ${contents.length} lines long.');
}
```

### Reading a file as binary
다음 코드는 파일의 전체내용을 bytes로 읽어서 int형 list에 넣는다. 이때 `readAsBytes()`를 사용하며, 이 메소드는 Future를 반환한다.
```dart
Future main() async {
  var config = File('config.txt');

  var contents = await config.readAsBytes();
  print('The file is ${contents.length} bytes long.');
}
```

### Handling errors
에러를 잡기 위해선 Future에 `catchError` 핸들러를 등록하거나, `try~catch`로 감싸면 된다.
```dart
// try~catch문으로 감싼 예
Future main() async {
  var config = File('config.txt');
  try {
    var contents = await config.readAsString();
    print(contents);
  } catch (e) {
    print(e);
  }
}
```

### Streaming file contents
파일을 읽기 위해 Stream API 또는 `await for`문을 사용할 수 있다.
```dart
import 'dart:io';
import 'dart:convert';

Future main() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

  var lines = inputStream // Stream API를 사용한다.
      .transform(utf8.decoder) // 스트림을 쓰면 변환이 쉬워진다.
      .transform(LineSplitter()); // 스트림을 쓰면 변환이 쉬워진다.
  try {
    await for (var line in lines) { // await for문을 사용한다.
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } catch (e) {
    print(e);
  }
}
```

### Writing file contents
파일에 데이터를 쓰기 위해 [IOSink](https://api.dartlang.org/stable/dart-io/IOSink-class.html)를 사용할 수 있다. IOSink를 얻기 위해 `openWrite()` 메소드를 사용한다. 기본 모드는 `FileMpde.write`다. 이 모드는 파일내용을 완전히 대체한다.
```dart
var logFile = File('log.txt');
var sink = logFile.openWrite(); // IOSink 객체를 반환한다.
sink.write('FILE ACCESSED ${DateTime.now()}\n');
await sink.flush();
await sink.close();
```
파일 끝에 내용을 추가하려면 `FileMode.append` 모드를 사용한다.
```dart
var sink = logFile.openWrite(mode: FileMode.append);
```
바이너리 데이터를 쓰려면 `add(List<int> data)` 메소드를 사용한다.

### Listing file in a directory
특정 디렉토리에서 모든 파일과 서브 디렉토리를 찾는 작업은 비동기로 진행된다. `list()` 메소드는 파일 또는 디렉토리를 찾을 때마다 emit하는 Steam을 반환한다.
```dart
Future main() async {
  var dir = Directory('tmp'); // 탐색할 디렉토리

  try {
    var dirList = dir.list(); // Stream을 반환한다.
    await for (FileSystemEntity f in dirList) { // Stream에 await for문 사용
      if (f is File) {
        print('Found file ${f.path}');
      } else if (f is Directory) {
        print('Found dir ${f.path}');
      }
    }
  } catch (e) {
    print(e.toString());
  }
}
```

### Other common functionality
`File`, `Directory` 클래스의 기본 메소드
- 파일 또는 디렉토리 만들기: `create()`
- 파일 또는 디렉토리 삭제하기: `delete()`
- 파일의 길이 가져오기: `length()`
- 파일 열기: `open()`

API 전체 목록: [File](https://api.dartlang.org/stable/dart-io/File-class.html), [Directory](https://api.dartlang.org/stable/dart-io/Directory-class.html)

## HTTP clients and servers
`dart:io` 라이브러리는 커맨드라인 앱이 HTTP server를 실행할 수 있도록 HTTP 리소스에 접근할 수 있는 클래스들을 제공한다.

### HTTP server
[HttpServer](https://api.dartlang.org/stable/dart-io/HttpServer-class.html) 클래스는 웹서버를 만들기 위한 low-level의 기능을 제공한다.
다음 샘플 웹서버는 단순한 텍스트를 반환한다. 
```dart
Future main() async {
  final requests = await HttpServer.bind('localhost', 8888); // 8888 포트로 바인딩
  await for (var request in requests) { // 리스닝
    processRequest(request); // 요청 처리
  }
}

void processRequest(HttpRequest request) {
  print('Got request for ${request.uri.path}');
  final response = request.response;
  if (request.uri.path == '/dart') { // 요청 라우팅 & 핸들링
    response
      ..headers.contentType = ContentType( // cascade 표기법 사용
        'text',
        'plain',
      )
      ..write('Hello from the server'); // 응답 보내기
  } else {
    response.statusCode = HttpStatus.notFound;
  }
  response.close();
}
```  

### HTTP client
[HttpClient](https://api.dartlang.org/stable/dart-io/HttpClient-class.html) 클래스는 Dart 커맨드라인 또는 서버사이드 앱에서 HTTP 리소스에 연결할 수 있도록 돕는다. HttpClient 클래스를 브라우저기반 앱에서 사용하지 마라. 브라우저에서 프로그래밍을 할 땐 [dart:html의 HttpRequest](https://api.dartlang.org/stable/dart-html/HttpRequest-class.html) 클래스는 사용해라.
```dart
Future main() async {
  var url = Uri.parse('http://localhost:8888/dart');
  var httpClient = HttpClient(); // dart:io의 HttpClient를 생성
  var request = await httpClient.getUrl(url); // HTTP 요청하기
  var response = await request.close(); // 요청 닫기
  var data = await response.transform(utf8.decoder).toList(); // 요청에서 데이터 가져오기
  print('Response ${response.statusCode}: $data');
  httpClient.close();
}
```

## More information
이 페이지에서 어떻게 `dart:io`의 메인 피쳐를 사용하는지를 보여줬다. `dart:io` 라이브러리는 그밖에 [processes](https://api.dartlang.org/stable/dart-io/Process-class.html), [sockets](https://api.dartlang.org/stable/dart-io/Socket-class.html), [web sockets](https://api.dartlang.org/stable/dart-io/WebSocket-class.html)를 위한 API도 제공한다.