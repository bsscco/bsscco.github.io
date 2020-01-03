---
title: Flutter로 개발하기(22) - Bloc 소개
date: 2019-03-24 09:51:41
tags: [flutter, bloc pattern]
---

# Bloc document - 소개
원문: https://felangel.github.io/bloc

## 시작하기
### 개요
Bloc은 세 가지 패키지로 구성됩니다.
- bloc - 코어 라이브러리
- flutter_bloc - bloc을 사용하기 위한 플러터 위젯
- angular_bloc - bloc을 사용하기 위한 앵귤러 컴포넌트

### 설치
플터터의 경우 pubspec.yaml에서
```yaml
dependencies:
  bloc: ^0.10.0
  flutter_bloc: ^0.8.0
```
그리고 `flutter packages get`

### Import
플러터의 경우 
```yaml
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
```

## 왜 Bloc인가요?
> Bloc은 비지니스 로직으로부터 프리젠테이션 로직을 쉽게 분리시켜줍니다. 빠르게 작성할 수 있는 코드, 쉽게 테스트 할 수 있는 코드, 재사용 가능한 코드를 만들 수 있게 해줍니다.

프로덕션 퀄리티의 앱을 제작할 때 상태관리는 매우 중요합니다.

우리는 개발자로써 이런 것들을 원합니다:
- 우리 앱의 상태가 어느 시점에 어떤 상태에 있는지 알기 원합니다.
- 우리 앱이 적절하게 반응하고 있는지 확인할 모든 케이스를 쉽게 테스트할 수 있길 원합니다.
- 모든 단일 사용자 상호작용을 기록하기 원합니다. 그렇게 되면 데이터 기반이 의사결정이 가능해집니다.
- 컴포넌트 재사용을 통해 가능한 한 효과적으로 일하기 원합니다.
- 많은 개발자들이 같은 패턴, 같은 컨벤션을 따라서 끊김없이 일하기 원합니다.
- 빠른 반응형 앱을 만들기 원합니다.

Bloc은 이러한 니즈 이상의 것들을 고려해서 설계됐습니다.

Bloc은 세 가지 핵심 가치와 함께 설계됐습니다.
- 단순함
  - 다양한 스킬의 개발자들에 의해 사용될 수 있고 이해하기 쉽습니다.
- 강력함
  - 작은 컴포넌트들을 구성해서 훨씬 복잡한 앱을 만들 수 있습니다. 
- 테스트 가능
  - 앱의 모든 측면에서 테스트하기 쉽습니다.

Bloc은 상태변경이 일어날 수 있는 시기를 규제하고 상태변경의 방법을 하나로 강제함으로써 상태변경을 예측할 수 있게 해줍니다.

## 핵심 개념
### Events
> Events는 Bloc으로 향하는 입력입니다. 그들은 일반적으로 버튼눌림 같은 사용자 상호작용, 페이지로드 같은 라이프사이클 이벤트에 의해 디스패치됩니다.

카운터앱에서 두 버튼 중 하나를 눌렀을 때 발생하는 이벤트
```dart
enum CounterEvent { increment, decrement }
```
여기서는 enum을 사용해 이벤트를 정의했지만, 더 복잡한 경우엔 class로 정의해야 할 수도 있습니다.

### States
> States는 Bloc의 출력입니다. 그리고 앱의 상태를 대표합니다. UI 컴포넌트들은 상태를 공지받을 수 있고, 그 상태를 기반으로 다시 그릴 수 있습니다. 

카운터앱에서 상태는 아주 단순합니다. 이 상태는 integer이고, 현재 카운트를 대표합니다.

### Transitions
> 한 상태에서 또 다른 상태로의 변경을 Transition이라고 부릅니다. Transition은 현재 상태, 이벤트, 다음 상태로 구성됩니다.

사용자가 카운터과 상호작용 함으로써 Increment 또는 Decrement 이벤트가 트리거되고,  그 이벤트는 현재 상태를 갱신시킬 것입니다. 이런 상태변경을 Transition으로 설명할 수 있습니다.
```dart
{
  "currentState": 0,
  "event": "CounterEvent.increment",
  "nextState": 1
}
```

모든 상태변경은 기록되기 때문에 우리는 쉽게 사용자의 모든 상호작용을 추적할 수 있습니다. 추가적으로 디버깅에서 시간여행 기능을 만들 수도 있습니다.

### Streams
> Stream은 비동기 데이터의 나열입니다.

Bloc은 RxDart 위에 빌드되었습니다. 

### Blocs
> Bloc은 Events의 입력 Stream을 States의 출력 Stream으로 변시켜주는 컴포넌트입니다. 

모든 Bloc은 `Bloc` 클래스를 상속해야 합니다.
```dart
import 'package:bloc/bloc.dart';

class CounterBloc extends Bloc<CounterEvent, int> {

}
```

모든 Bloc은 처음 상태를 정의해야 합니다. 
```dart
@override
int get initialState => 0;
```

모든 Bloc은 mapEventToState() 함수를 구현해야 합니다. 이 함수는 currentState, event 두 인자를 취해서 프리젠테이션 레이어에서 소비될 새로운 상태의 Stream을 반환합니다. 
```dart
@override
Stream<int> mapEventToState(int currentState, CounterEvent event) async* {
    switch (event) {
      case CounterEvent.decrement:
        yield currentState - 1;
        break;
      case CounterEvent.increment:
        yield currentState + 1;
        break;
    }
}
```

`CounterBloc`의 완전한 코드
```dart
import 'package:bloc/bloc.dart';

enum CounterEvent { increment, decrement }

class CounterBloc extends Bloc<CounterEvent, int> {
  @override
  int get initialState => 0;

  @override
  Stream<int> mapEventToState(int currentState, CounterEvent event) async* {
    switch (event) {
      case CounterEvent.decrement:
        yield currentState - 1;
        break;
      case CounterEvent.increment:
        yield currentState + 1;
        break;
    }
  }
}
```
Bloc이 반환한 새 상태가 현재 상태와 같다면 Transition은 발생하지 않습니다.

모든 Bloc은 dispatch 메소드를 가집니다. Dispatch는 event를 취해서 mapEventToState를 트리거 합니다. Dispatch는 프리젠테이션 레이어에서 호출되거나 Bloc 안에서 새로운 이벤트로 그 Bloc에게 알리기 위해 호출될 것입니다.
```dart
void main() {
    CounterBloc bloc = CounterBloc();

    for (int i = 0; i < 3; i++) {
        bloc.dispatch(CounterEvent.increment);
    }
}
```
위 코드에서 Transition들은 다음과 같습니다.
```dart
{
    "currentState": 0,
    "event": "CounterEvent.increment",
    "nextState": 1
}
{
    "currentState": 1,
    "event": "CounterEvent.increment",
    "nextState": 2
}
{
    "currentState": 2,
    "event": "CounterEvent.increment",
    "nextState": 3
}
```

onTransition은 모든 지역 Bloc transition을 다루기 위해 오버라이드 할 수 있는 메소드입니다. onTransition은 Bloc의 상태가 변경되기 전에 호출됩니다.

Tip: onTransition은 Bloc의 로깅과 분석 코드를 추가하기 좋은 위치입니다.
```dart
@override
void onTransition(Transition<CounterEvent, int> transition) {
    print(transition);
}
```

onError는 모든 지역 Bloc 예외를 다루기 위해 오버라이드 할 수 있는 메소드입니다. 기본적으로 모든 예외는 무시되고, Bloc의 기능은 영향을 받지 않을 것입니다.

Tip: onError는 에러 핸들링 코드를 추가하기 좋은 위치입니다.
```dart
@override
void onError(Object error, StackTrace stackTrace) {
  print('$error, $stackTrace');
}
```

### BlocDelegate
모든 Trasition, Exception들에 접근할 수 있는 보너스가 있습니다. 
```dart
class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onTransition(Transition transition) {
    print(transition);
  }

  @override
  void onError(Object error, StackTrace stacktrace) {
    print('$error, $stacktrace');
  }
}

----------------------------------------

void main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  CounterBloc bloc = CounterBloc();

  for (int i = 0; i < 3; i++) {
    bloc.dispatch(CounterEvent.increment);
  }
}
```

BlocSupervisor는 모든 Bloc을 감독하는 싱글톤 객체입니다.

## 아키텍쳐
![bloc-architecture.png](https://felangel.github.io/bloc/bloc_architecture.png)
Bloc을 사용함으로써 우리 앱을 세 가지 레이어로 분리시킬 수 있습니다.
- 데이터
  - Data Provider
  - Repository
- 비지니스 로직
- 프리젠테이션

더 lowest level(UI에서 가장 먼)에서 시작해서 프리젠테이션 레이어를 향해 위 방향으로 작업할 것입니다. 

### 데이터 레이어
> 데이터 레이어는 하나 또는 여러 source들로부터 데이터를 찾고 조작하는 책임을 집니다.

#### Data Provider
Data Provider의 책임은 raw 데이터를 제공하는 것입니다. Data Provider는 범용적이어야 합니다.

Data Provider는 일반적으로 CRUD 명령을 수행합니다. 
```dart
class DataProvider {
    Future<RawData> readData() async {
        // Read from DB or make network request etc...
    }
}
```

#### Repository
Repository는 하나 또는 그 이상의 Data Provider들을 감싸서 Bloc 레이어와 통신합니다.

```dart
class Repository {
    final DataProviderA dataProviderA;
    final DataProviderB dataProviderB;

    Future<Data> getAllDataThatMeetsRequirements() async {
        final RawDataA dataSetA = await dataProviderA.readData();
        final RawDataB dataSetB = await dataProviderB.readData();

        final Data filteredData = _filterData(dataSetA, dataSetB);
        return filteredData;
    }
}
```

### Bloc(Business Logic) 레이어
> Bloc 레이어의 책임은 프리젠테이션으로부터 온 이벤트를 새 상태로 응답하는 것입니다. Bloc 레이어는 빌드에 필요한 데이터를 찾기 위해 하나 이상의 Repository를 의존합니다.

Bloc 레이어가 UI(프리젠테이션 레이어)와 데이터 레이어를 잇는 가교 역할을 한다고 생각하세요. 
```dart
class BusinessLogicComponent extends Bloc {
    final Repository repository;

    Stream mapEventToState(currentState, event) async* {
        if (event is AppStarted) {
            yield await repository.getAllDataThatMeetsRequirements();
        }
    }
}
```

#### Bloc 간의 통신
모든 Bloc은 다른 Bloc들이 구독할 수 있는 상태 Stream을 가집니다.
```dart
class MyBloc extends Bloc {
  final OtherBloc otherBloc;
  StreamSubscription otherBlocSubscription;

  MyBloc(this.otherBloc) {
    otherBlocSubscription = otherBloc.state.listen((state) {
        // 여기서 상태 변경에 반응합니다. 
        // 여기서 MyBloc의 변경을 트리거하기 위해 이벤트를 디스패치 합니다.
    });
  }

  @override
  void dispose() {
    otherBlocSubscription.cancel();
    super.dispose();
  }
}
```

### 프리젠테이션 레이어
> 프리젠테이션 레이어의 책임은 하나 이상의 bloc 상태를 기반으로 렌더링 하는 것입니다. 이것은 사용자 입력이나 앱 라이프사이클 이벤트에 의해 다뤄져야 합니다.

아래 시나리오에서는 프리젠테이션 레이어가 `AppStart` 이벤트를 디스패치합니다. 
```dart
class PresentationComponent {
    final Bloc bloc;

    PresentationComponent() {
        bloc.dispatch(AppStarted());
    }

    build() {
        // bloc 상태를 기반으로 UI를 렌더링 합니다.
    }
}
```

## 테스팅
> Bloc은 극도로 테스트하기 쉽게 설계됐습니다.

CounterBloc을 위한 테스트코드를 작성해봅시다.

CounterBloc의 구현은 다음과 같습니다.
```dart
enum CounterEvent { increment, decrement }

class CounterBloc extends Bloc<CounterEvent, int> {
  @override
  int get initialState => 0;

  @override
  Stream<int> mapEventToState(int currentState, CounterEvent event) async* {
    switch (event) {
      case CounterEvent.decrement:
        yield currentState - 1;
        break;
      case CounterEvent.increment:
        yield currentState + 1;
        break;
    }
  }
}
```

pubspec.yaml에서 test 패키지를 추가합니다.
```yaml
dev_dependencies:
  test: ">=1.3.0 <2.0.0"
```

import
```dart
import 'package:test/test.dart';
```

먼저 테스트 그룹을 만들어야 합니다.
```dart
void main() {
    group('CounterBloc', () {

    });
}
```
group은 같은 컨텍스트 안에 여러 테스트를 묶어놓기 위해 사용합니다. 테스트들은 공통의 setUp과 tearDown을 공유합니다.

CounterBloc의 인스턴스를 만드는 것부터 시작합시다.
```dart
group('CounterBloc', () {
    CounterBloc counterBloc;

    setUp(() {
        counterBloc = CounterBloc();
    });
});
```

이제 각 테스트를 작성할 수 있습니다.
```dart
group('CounterBloc', () {
    CounterBloc counterBloc;

    setUp(() {
        counterBloc = CounterBloc();
    });

    test('initial state is 0', () {
        expect(counterBloc.initialState, 0);
    });
});
```

우리는 모든 테스트를 `pub run test` 명령으로 실행할 수 있습니다.

아래 코드는 더 복잡한 테스트를 작성한 예입니다.
```dart
test('single Increment event updates state to 1', () {
    final List<int> expected = [0, 1];

    expectLater(
        counterBloc.state,
        emitsInOrder(expected),
    );

    counterBloc.dispatch(CounterEvent.increment);
});

test('single Decrement event updates state to -1', () {
    final List<int> expected = [0, -1];

    expectLater(
        counterBloc.state,
        emitsInOrder(expected),
    );

    counterBloc.dispatch(CounterEvent.decrement);
});
```