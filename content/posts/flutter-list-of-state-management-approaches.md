---
title: Flutter로 개발하기(18) - 상태관리 접근법들
date: 2019-03-18 09:51:41
tags: [flutter]
---

# List of state management approaches
원문: https://flutter.dev/docs/development/data-and-backend/state-mgmt/options

## 개요
- 구글 IO 2018에서 발표했던 영상 [플러터로 반응형 모바일앱 만들기](https://www.youtube.com/watch?v=RS36gBEp8OI&feature=youtu.be)와, [관련 글](https://medium.com/flutter-io/build-reactive-mobile-apps-in-flutter-companion-article-13950959e381)
- Brian Egan의 [플러터 설계 샘플](http://fluttersamples.com/)

## setState
- 플러터 튜토리얼 [플러터앱에 상호작용 추가하기](https://flutter.dev/docs/development/ui/interactive)
- Agung Surya의 [플러터에서 기본 상태 관리](https://medium.com/@agungsurya/basic-state-management-in-google-flutter-6ee73608f96d) 

## InheritedWidget & Scoped model
- Eric Windmill의 [효과적으로 플러터 InheritedWidget 사용하기](https://ericwindmill.com/posts/inherited_widget/)
- Ryan Edge의 [당신은 Redux가 필요 없을지도 모른다.](https://proandroiddev.com/you-might-not-need-redux-the-flutter-edition-9c11eba006d7)
- Tensor Programming 영상 [scoped model 패턴으로 상태 관리하기](https://www.youtube.com/watch?v=-MCeWP3rgI0)
- MTechViral의 [Inherited Widget과 Scoped Model 설명](https://www.youtube.com/watch?v=j-27MZwRbFw)
- 영상 [scoped model](https://www.youtube.com/watch?v=Oql5bU-Uvso)
- [scoped model 패키지](https://pub.dartlang.org/packages/scoped_model)
- Didier Bolelens의 [Widget-State-Context-InheritedWidget](https://www.didierboelens.com/2018/06/widget---state---context---inheritedwidget/)

## Redux
- DartConf 2018에서 발표했던 영상 [Redux로 애니메이션 관리하기](https://www.youtube.com/watch?v=9ZkLtr0Fbgk)와 [관련 글](https://medium.com/flutter-io/animation-management-with-flutter-and-flux-redux-94729e6585fa)
- [플러터 Redux 패키지](https://pub.dartlang.org/packages/flutter_redux)
- Xavi Rigau의 [플러터 Redux 소개](https://blog.novoda.com/introduction-to-redux-in-flutter/)
- Paulina Szklarska의 [플러터+Redux로 쇼핑앱을 만드는 방법](https://hackernoon.com/flutter-redux-how-to-make-shopping-list-app-1cd315e79b65)
- Tensor Programming 영상 [플러터 Redux로 Todo앱 만들기](https://www.youtube.com/watch?v=Wj216eSBBWs)
- Jack Wong의 [플러터 Redux 예제](https://medium.com/flutterpub/flutter-redux-thunk-27c2f2b80a3b)
- Hillel Coren의 [Redux로 규모가 큰 플러터앱 만들기](https://hillelcoren.com/2018/06/01/building-a-large-flutter-app-with-redux/)
- Alibaba의 [리더스 기반으로 플러터 앱 다루기](https://github.com/alibaba/fish-redux/) 

## BLoC / Rx
- Sagar Suri의 [BLoC 패턴을 사용한 플러터 프로젝트 설계](https://medium.com/flutterpub/architecting-your-flutter-project-bd04e144a8f1)
- Felix Angelov의 [Bloc 라이브러리](https://felangel.github.io/bloc)
- Didier Boelens의 [반응형 프로그래밍, 스트림, BLoC Usecase](https://www.didierboelens.com/2018/12/reactive-programming---streams---bloc---practical-use-cases)

## MobX
- [MobX.dart](https://github.com/mobxjs/mobx.dart)
- [MobX 시작하기](https://mobx.pub/getting-started)
