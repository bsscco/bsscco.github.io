---
title: Flutter 코드랩 리뷰 - 플러터로 아름다운 UI 만들기
date: 2019-02-14 23:11:44
tags: [flutter]
---

Flutter로 아름다운 UI 만들기
https://codelabs.developers.google.com/codelabs/flutter/index.html

프로젝트 소스 : https://github.com/bsscco/friendly_chat

- Flutter의 장점
  - 한 번 작성하면 iOS, Android에 배포 가능하다.
  - Material Design과 Cupertino 위젯과 동작들을 지원한다.
  - 스크롤링, 네비게이션 패턴, 폰트, 그 이상을 지원한다.
  - 반응형 functional 프로그래밍을 지원한다.
  - 겁나 빠른 핫리로드를 지원한다.
  - Dart로 쓰여진 코드는 Android, iOS의 환경에 맞게 컴파일된다.
- 눈에 띄는 내용
  - 마진에 들어가는 숫자는 디바이스에 따라 적절한 픽셀로 변환된다. (Android는 dip로 변환)
  - CrossAxisAlignment.start 는 부모의 메인 Axis의 교차 Axis를 기준으로 앞쪽에 위치하도록 하겠다는 뜻이다.
  - setState(() => {...})에 들어가는 람다는 동기적으로 작업을 완료해야 한다. 프레임워크가 작업을 완료하기 전에 위젯을 rebuild 할 수 있기 때문이다.
  - 사용하지 않는 파라메터는 _로 이름지어놓는 게 관례다.
  - AnimationController의 생성자에 넘기는 vsync 인자는 offscreen에서도 그려지는 낭비를 없애기 위해 사용된다.
  - 위젯의 dispose() 메소드는 멀티 스크린 같은 복잡한 구조에서 호출된다.
  - IconButton의 onPressed값이 null이면 IconButton은 자동으로 disable된다. (색상도 설정된 테마에 맞게 disable색상으로 바뀐다.)

