---
title: Flutter로 개발하기(21) - Bloc패턴 샘플
date: 2019-03-23 09:51:41
tags: [flutter, bloc pattern]
---

# bloc_library sample
참고 예제: https://github.com/brianegan/flutter_architecture_samples/tree/master/bloc_library

이 샘플은 상태관리를 위해 [bloc](https://pub.dartlang.org/packages/bloc)과 [flutter_bloc](https://pub.dartlang.org/packages/flutter_bloc) 라이브러리를 사용했습니다.
더 자세한 내용과 튜토리얼은 [bloc library document](https://felangel.github.io/bloc)를 확인하세요.

## 핵심 개념
- 상태를 위로 올리기
  - 여러 위젯에서 같은 bloc이 필요할 땐 공통의 조상 위젯에 `BlocProvider`를 사용합니다.
- `Events`는 bloc의 입력입니다.
  - 이들은 일반적으로 버튼 누르기와 같은 유저 인터렉션 또는 페이지 로드 같은 라이프사이클 이벤트로 디스패치됩니다.
- `States`는 bloc의 출력이며 앱 상태의 한 부분을 대표합니다.
  - 컴포넌트들은 상태를 공지 받을 수 있고 그 상태에 의해 다시 그려질 수 있습니다.
- 한 상태에서 다른 상태로 바뀌는 것을 `Transition`이라고 부릅니다.
  - `Transition`은 현재 상태, 이벤트, 다음 상태로 구성됩니다.
- bloc은 들어오는 `Events` 스트림을 나가는 `States` 스트림으로 변환합니다.

## 앱 상태 갱신하기
모든 bloc은 `dispatch` 메소드를 가집니다. `Dispatch`는 이벤트를 취해서 `mapEventToState`를 트리거합니다. Dispatch는 새로운 이벤트를 bloc에게 알리고 상태 변경을 트리거 합니다.

## Bloc 사이에서 데이터 공유하기
이 샘플에서 `FilteredTodosBloc`은 `TodosBloc`을 의존합니다. `TodosBloc`의 변경에 의해 filtered todos도 갱신됩니다. 이 접근은 어떻게 우리가 다른 bloc들로부터 bloc들을 구성해서 반응형 앱을 빌드할 수 있는지 설명합니다. 앱이 시작되면 `TodosBloc`은 todos를 레포지토리로부터 공급받고, 그 순간부터 모든 todos의 변경은 `TodosBloc`을 통해 실행됩니다. 이는 변경사항을 비동기적으로 영속화합니다. `FilteredTodosBloc`은 `TodosBloc` 상태 변경들을 감지해 filtered todos를 갱신합니다. 

## UI 갱신하기
`BlocBuilder`는 bloc과 빌더함수가 요구되는 플러터 위젯입니다. `BlocBuilder`는 새로운 상태에 대해 위젯을 빌드합니다. `BlocBuilder`는 `StreamBuilder`와 비슷하지만 중복코드를 감소시켜주고 불필요한 리빌드를 피할 수 있도록 최적화되어 있습니다.

## 테스트하기
일반적으로 이 앱은 "테스팅 피라미드: 많은 유닛테스트, 보다 적은 위젯테스트, 보다 적은 통합테스트"를 준수합니다.