---
title: Flutter Today I Learned - 2019-04-24
date: 2019-04-24 09:51:41
tags: [flutter]
---

배울 문서 : https://drive.google.com/file/d/1d19SQry7jXwGd-wy3MeXJ35uCoy4veib/view
예제 소스코드 : https://github.com/markclow/flutter_book_examples

 
## Flutter 소개
- Dart 언어 사용
- One source, multi flatform
- hot reload
- 높은 퀄리티의 기본 UI 위젯들, material, cupertino
- 구글 차세대 OS Fuchsia용 앱 개발
- Android Studio Flutter 플러그인
- VS Code Flutter Extension

## Flutter Hot Restarting & Reloading
- hot restarting - 바뀐 코드를 Dar VM으로 로드시키고 앱을 재실행
- hot reloading - 바뀐 코드를 Dart VM으로 로드시키고 앱 상태 변경 없이 갱신

## Flutter Dependencies & Packages
- https://pub.dartlang.org
- 가장 유용한 external 패키지
  - http - http 연결 라이브러리
  - rxdart - 반응형 함수형 프로그래밍 라이브러
  - datetime_picker_formfield
  - image_picker
  - zoomable_image - 이미지를 터치해서 panning과 zomming 할 수 있음. 
  - shared_preferences - 로컬 셋팅과 데이터를 저장할 수 있게 해줌
  - cached_network_image - 네트워크 이미지를 보여주는 라이브러리. 캐시 디렉토리를 사용함. 그밖에 placeholder 기능도 제공
- external 패키지 사용하기 - `scoped_model: ^1.0.1` `import 'package:scoped_model/scoped_model.dart';`
- 패키지 버전 기술하기
  - `any` - 어느 버전이든
  - `1.2.3` - 1.2.3 버전만
  - `>=1.2.3` - 1.2.3 버전보다 높거나 같은
  - `^1.2.3` - 1.2.3과 호환되는 버전 (ex_ `>=1.2.3 <2.0.0`)
  - `^0.2.3` - 1.2.3과 호환되는 버전 (ex_ `>=0.2.3 <0.3.0`)
  
## Flutter 프로젝트 파일
- .packages - `packagers get` 명령을 통해 내려받은 패키지들의 이름이 저장된 파일
- pubspec.lock - 내려받은 패키지를 계속 사용하도록 버전들을 저장하는 파일

## Flutter 패키지 publish
- dart 패키지 - dart로 쓰여진 패키지
- plugin 패키지 - Android나 iOS 플랫폼 기술 코드로 쓰여진 dart 패키지
- dart 패키지 셋팅
  - root - pubspec.yaml을 포함하는 루트 폴더
  - android - 안드로이드 관련 파일들이 들어있는 폴더
  - ios - ios 관련 파일들이 들어있는 폴더 
  - lib - main.dart를 포함하는 어플리케이션 소스코드 파일들이 들어있는 폴더
  - test - 유닛테스트 코드들이 들어있는 폴더
- adding documentation - README.md, CHANGELOG.md, dartdoc 도구를 사용해서 api문서 만들기
- publish dry-run - `flutter packages pub publish --dry-run`
- 실제 publish - `flutter packages pub publish`

## Flutter 위젯
- 위젯들을 composition 해서 또 더 큰 덩치의 위젯을 만들어낸다.
- build 메소드 - 위젯을 반환하는 메소드
- build context - build 메소드의 첫번째 파라메터. 위젯트리에서 어느 위치에 있는지에 대한 정보가 담겨있음.'
- 플러터 위젯은 immutable. StatefulWidget은 분리된 State 객체와 연결되며 상태는 State객체에 담긴다.   
 