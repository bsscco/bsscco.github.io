---
title: Flutter로 개발하기(24) - Bloc 튜토리얼 Todos
date: 2019-03-30 09:51:41
tags: [flutter, bloc pattern]
---

# Bloc document - Todos 튜토리얼
원문: https://felangel.github.io/bloc

![flutter_todos.gif](https://felangel.github.io/bloc/gifs/flutter_todos.gif)

## Setup
```cli
flutter create flutter_todos
```
```yaml
name: flutter_todos
description: A new Flutter project.

environment:
  sdk: ">=2.0.0 <3.0.0"

dependencies:
  meta: ">=1.1.0 <2.0.0"
  equatable: ^0.2.0
  flutter_bloc: ^0.9.0
  flutter:
    sdk: flutter

dependency_overrides:
  todos_app_core:
    git:
      url: https://github.com/felangel/flutter_architecture_samples
      path: todos_app_core
      ref: expose-repositories
  todos_repository_core:
    git:
      url: https://github.com/felangel/flutter_architecture_samples
      path: todos_repository_core
      ref: expose-repositories
  todos_repository_simple:
    git:
      url: https://github.com/felangel/flutter_architecture_samples
      path: todos_repository_simple
      ref: expose-repositories
flutter:
  uses-material-design: true
```
```cli
flutter packages get
```

> 우리는 몇개의 의존성들을 오버라이딩 할 겁니다. [Brian Egan's Flutter Architecture Samples](https://github.com/brianegan/flutter_architecture_samples)로부터 그 의존성들을 재사용할 것이기 때문입니다.

## Todos Repository
TodosRepository의 구현에 대해 살펴보진 않을 겁니다. TodosRepository를 하이레벨에서만 보면 loadTodos 메소드와 saveTodos 메소드를 가집니다.

## Todos Bloc
> Todos Bloc은 TodosEvent를 TodosState로 변환하는 책임을 집니다. 그리고 Todos 목록도 관리합니다.

### Model
가장 먼저 할 것은 Todo 모델을 정의하는 겁니다. 각 Todo는 id, task, note, completed flag를 가집니다.

models/todo.dart를 만들어봅시다. 
```dart
import 'package:todos_app_core/todos_app_core.dart';
import 'package:meta/meta.dart';
import 'package:equatable/equatable.dart';
import 'package:todos_repository_core/todos_repository_core.dart';

@immutable
class Todo extends Equatable {
  final bool complete;
  final String id;
  final String note;
  final String task;

  Todo(this.task, {this.complete = false, String note = '', String id})
      : this.note = note ?? '',
        this.id = id ?? Uuid().generateV4(),
        super([complete, id, note, task]);

  Todo copyWith({bool complete, String id, String note, String task}) {
    return Todo(
      task ?? this.task,
      complete: complete ?? this.complete,
      id: id ?? this.id,
      note: note ?? this.note,
    );
  }

  @override
  String toString() {
    return 'Todo { complete: $complete, task: $task, note: $note, id: $id }';
  }

  TodoEntity toEntity() {
    return TodoEntity(task, id, note, complete);
  }

  static Todo fromEntity(TodoEntity entity) {
    return Todo(
      entity.task,
      complete: entity.complete ?? false,
      note: entity.note,
      id: entity.id ?? Uuid().generateV4(),
    );
  }
}
```
> Todo 인스턴스들을 `==`와 `hashCode` 없이 비교하기 위해 Equatable 패키지를 사용합니다.

다음으로 프리젠테이션 레이어가 전달받을 TodosState를 만들 겁니다.

### States
blocs/todos/todos_state.dart를 만들고 State들을 정의합시다.

3개의 State는 다음과 같습니다.
- TodosLoading - 앱이 Repository로부터 todos를 fetching 하고 있는 상태
- TodosLoaded - todos가 성공적으로 로드된 상태
- TodosNotLoaded - todos 로드에 실패한 상태

```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/models/models.dart';

@immutable
abstract class TodosState extends Equatable {
  TodosState([List props = const []]) : super(props);
}

class TodosLoading extends TodosState {
  @override
  String toString() => 'TodosLoading';
}

class TodosLoaded extends TodosState {
  final List<Todo> todos;

  TodosLoaded([this.todos = const []]) : super([todos]);

  @override
  String toString() => 'TodosLoaded { todos: $todos }';
}

class TodosNotLoaded extends TodosState {
  @override
  String toString() => 'TodosNotLoaded';
}
```
> immutable 데코레이터를 TodosState에 annotating 해습니다. 이것은 모든 TodosState가 변경될 수 없음을 지시합니다.

다음으로 이벤트들을 구현합시다.

### Events
TodosBloc에서 다룰 이벤트들은 다음과 같습니다.
- LoadTodos - bloc에게 TodosRepository로부터 Todos를 로드해야 한다고 말합니다.
- AddTodo - bloc에게 새로운 Todo를 Todos 목록에 추가해야 한다고 말합니다. 
- UpdateTodo - bloc에게 Todo를 갱신해야 한다고 말합니다.
- DeleteTodo - bloc에게 Todo를 삭제해야 한다고 말합니다.
- ClearCompleted - bloc에게 complete된 모든 Todos를 삭제해야 한다고 말합니다.
- ToggleAll - bloc에게 모든 Todos의 completed 상태를 토글해야 한다고 말합니다.

blocs/todos/todos_event.dart를 만들고 위에서 설명한 이벤트들을 구현합시다.
```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/models/models.dart';

@immutable
abstract class TodosEvent extends Equatable {
  TodosEvent([List props = const []]) : super(props);
}

class LoadTodos extends TodosEvent {
  @override
  String toString() => 'LoadTodos';
}

class AddTodo extends TodosEvent {
  final Todo todo;

  AddTodo(this.todo) : super([todo]);

  @override
  String toString() => 'AddTodo { todo: $todo }';
}

class UpdateTodo extends TodosEvent {
  final Todo updatedTodo;

  UpdateTodo(this.updatedTodo) : super([updatedTodo]);

  @override
  String toString() => 'UpdateTodo { updatedTodo: $updatedTodo }';
}

class DeleteTodo extends TodosEvent {
  final Todo todo;

  DeleteTodo(this.todo) : super([todo]);

  @override
  String toString() => 'DeleteTodo { todo: $todo }';
}

class ClearCompleted extends TodosEvent {
  @override
  String toString() => 'ClearCompleted';
}

class ToggleAll extends TodosEvent {
  @override
  String toString() => 'ToggleAll';
}
```
이제 우리는 TodosState와 TodosEvent로 TodosBloc을 구현할 수 있습니다.

 ### Bloc
 blocs/todos/todos_bloc.dart를 만들고 initialState 메소드와 mapEventToState 메소드를 구현합시다.
 ```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/blocs/todos/todos.dart';
import 'package:flutter_todos/models/models.dart';
import 'package:todos_repository_simple/todos_repository_simple.dart';

class TodosBloc extends Bloc<TodosEvent, TodosState> {
  final TodosRepositoryFlutter todosRepository;

  TodosBloc({@required this.todosRepository});

  @override
  TodosState get initialState => TodosLoading();

  @override
  Stream<TodosState> mapEventToState(TodosEvent event) async* {
    if (event is LoadTodos) {
      yield* _mapLoadTodosToState();
    } else if (event is AddTodo) {
      yield* _mapAddTodoToState(event);
    } else if (event is UpdateTodo) {
      yield* _mapUpdateTodoToState(event);
    } else if (event is DeleteTodo) {
      yield* _mapDeleteTodoToState(event);
    } else if (event is ToggleAll) {
      yield* _mapToggleAllToState();
    } else if (event is ClearCompleted) {
      yield* _mapClearCompletedToState();
    }
  }

  Stream<TodosState> _mapLoadTodosToState() async* {
    try {
      final todos = await this.todosRepository.loadTodos();
      yield TodosLoaded(
        todos.map(Todo.fromEntity).toList(),
      );
    } catch (_) {
      yield TodosNotLoaded();
    }
  }

  Stream<TodosState> _mapAddTodoToState(AddTodo event) async* {
    if (currentState is TodosLoaded) {
      // 역주: ..add() 대신에 .add()를 쓰면 upadtedTodos엔 void가 할당되어야 하므로 문법적 오류가 됩니다.
      // ..add()로 쓰면 updatedTodos엔 List.from((currentState as TodosLoaded).todos)가 할당되고, updatedTodos.add()가 호출됩니다.
      final List<Todo> updatedTodos = List.from((currentState as TodosLoaded).todos)..add(event.todo);
      yield TodosLoaded(updatedTodos);
      _saveTodos(updatedTodos);
    }
  }

  Stream<TodosState> _mapUpdateTodoToState(UpdateTodo event) async* {
    if (currentState is TodosLoaded) {
      final List<Todo> updatedTodos =
          (currentState as TodosLoaded).todos.map((todo) {
        return todo.id == event.updatedTodo.id ? event.updatedTodo : todo;
      }).toList();
      yield TodosLoaded(updatedTodos);
      _saveTodos(updatedTodos);
    }
  }

  Stream<TodosState> _mapDeleteTodoToState(DeleteTodo event) async* {
    if (currentState is TodosLoaded) {
      final updatedTodos = (currentState as TodosLoaded)
          .todos
          .where((todo) => todo.id != event.todo.id)
          .toList();
      yield TodosLoaded(updatedTodos);
      _saveTodos(updatedTodos);
    }
  }

  Stream<TodosState> _mapToggleAllToState() async* {
    if (currentState is TodosLoaded) {
      final allComplete = (currentState as TodosLoaded).todos.every((todo) => todo.complete);
      final List<Todo> updatedTodos = (currentState as TodosLoaded)
          .todos
          // 역주: complete값을 바꾸는데 copyWith()를 호출하는 이유는 Todo 모델이 immutable이기 때문입니다.
          .map((todo) => todo.copyWith(complete: !allComplete)) 
          .toList();
      yield TodosLoaded(updatedTodos);
      _saveTodos(updatedTodos);
    }
  }

  Stream<TodosState> _mapClearCompletedToState() async* {
    if (currentState is TodosLoaded) {
      final List<Todo> updatedTodos = (currentState as TodosLoaded)
          .todos
          .where((todo) => !todo.complete)
          .toList();
      yield TodosLoaded(updatedTodos);
      _saveTodos(updatedTodos);
    }
  }

  Future _saveTodos(List<Todo> todos) {
    return todosRepository.saveTodos(
      todos.map((todo) => todo.toEntity()).toList(),
    );
  }
}
```
> mapEventToState 메소드에서 state를 yielding 할 때 항상 currentState를 변경하는 대신에 새로운 state를 만듭니다. 그 이유는 bloc이 currentState와 nextState를 비교해서 오직 두 state가 다를 때만 transition을 트리거 하기 때문입니다.

### Barrel File
이제 우리는 bloc 파일들을 export 해서 import를 편리하게 해주는 barrel file을 만들 수 있습니다.

blocs/todos/todos.dart를 만들고 bloc, event, state를 export 합시다.
```dart
export './todos_bloc.dart';
export './todos_event.dart';
export './todos_state.dart';
```

## Filtered Todos Bloc
> FilteredTodosBloc은 TodosBloc에서의 상태 변경에 반응하는 책임을 집니다. 

### Model
TodosState를 정의하고 구현하기 전에 VisibilityFilter 모델을 구현해야 합니다. VisibilityFilter는 FilteredTodosState가 포함할 Todos를 결정합니다. 우리는 3개의 필터를 가질 겁니다.
- all - 모든 Todos를 보여줍니다. (default)
- active - complete 되지 않은 Todos만 보여줍니다. 
- completed - complete 된 Todos만 보여줍니다.

models/visibility_filter.dart를 만들고 filter enum을 정의합시다.
```dart
enum VisibilityFilter { all, active, completed }
```

### States
TodosBloc에서 했던 것처럼, 우리는 FilteredTodosBloc을 위한 States를 만들 겁니다.
2개의 States를 가집니다.
- FilteredTodosLoading - Todos를 fetching 하는 동안의 상태입니다.
- FilteredTodosLoaded - 더 이상 fetching 할 Todos가 없는 상태입니다.

blocs/filtered_todos/filtered_todos_state.dart를 만들고 2개의 States를 구현합시다.
```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/models/models.dart';

@immutable
abstract class FilteredTodosState extends Equatable {
  FilteredTodosState([List props = const []]) : super(props);
}

class FilteredTodosLoading extends FilteredTodosState {
  @override
  String toString() => 'FilteredTodosLoading';
}

class FilteredTodosLoaded extends FilteredTodosState {
  final List<Todo> filteredTodos;
  final VisibilityFilter activeFilter;

  FilteredTodosLoaded(this.filteredTodos, this.activeFilter)
      : super([filteredTodos, activeFilter]);

  @override
  String toString() {
    return 'FilteredTodosLoaded { filteredTodos: $filteredTodos, activeFilter: $activeFilter }';
  }
}
```

### Events
우리는 FilteredTodosBloc을 위한 2개의 이벤트를 만들 겁니다.
- UpdateFilter - bloc에게 visibility filter가 바뀌었다는 걸 알립니다.
- UpdateTodos - bloc에게 Todos가 바뀌었다는 걸 알립니다.

blocs/filtered_todos/filtered_todos_event.dart를 만들고 2개의 이벤트를 구현합시다.
```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/models/models.dart';

@immutable
abstract class FilteredTodosEvent extends Equatable {
  FilteredTodosEvent([List props = const []]) : super(props);
}

class UpdateFilter extends FilteredTodosEvent {
  final VisibilityFilter filter;

  UpdateFilter(this.filter) : super([filter]);

  @override
  String toString() => 'UpdateFilter { filter: $filter }';
}

class UpdateTodos extends FilteredTodosEvent {
  final List<Todo> todos;

  UpdateTodos(this.todos) : super([todos]);

  @override
  String toString() => 'UpdateTodos { todos: $todos }';
}
```
이제 FIlteredTodosBloc을 구현할 준비가 됐습니다!

### Bloc
FilteredTodosBloc은 TodosBloc과 유사합니다. 하지만 TodosRepository 의존성을 가지는 대신에 TodosBloc 의존성을 가집니다. 이것은 TodosBloc에서 상태가 변경될 때 FilteredTodosBloc의 상태도 갱신될 수 있게 해줍니다.

blocs/filtered_todos/filtered_todos_bloc.dart를 만들고 시작합시다.
```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/blocs/filtered_todos/filtered_todos.dart';
import 'package:flutter_todos/blocs/todos/todos.dart';
import 'package:flutter_todos/models/models.dart';

class FilteredTodosBloc extends Bloc<FilteredTodosEvent, FilteredTodosState> {
  final TodosBloc todosBloc;
  StreamSubscription todosSubscription;

  FilteredTodosBloc({@required this.todosBloc}) {
    // 역주: todosBloc의 상태 변화를 감지하기 시작합니다.
    todosSubscription = todosBloc.state.listen((state) {
      if (state is TodosLoaded) {
        dispatch(UpdateTodos((todosBloc.currentState as TodosLoaded).todos));
      }
    });
  }

  @override
  FilteredTodosState get initialState {
    return todosBloc.currentState is TodosLoaded
        ? FilteredTodosLoaded(
            (todosBloc.currentState as TodosLoaded).todos,
            VisibilityFilter.all,
          )
        : FilteredTodosLoading();
  }

  @override
  Stream<FilteredTodosState> mapEventToState(FilteredTodosEvent event) async* {
    if (event is UpdateFilter) {
      yield* _mapUpdateFilterToState(event);
    } else if (event is UpdateTodos) {
      yield* _mapTodosUpdatedToState(event);
    }
  }

  Stream<FilteredTodosState> _mapUpdateFilterToState(
    UpdateFilter event,
  ) async* {
    if (todosBloc.currentState is TodosLoaded) {
      yield FilteredTodosLoaded(
        _mapTodosToFilteredTodos(
          (todosBloc.currentState as TodosLoaded).todos,
          event.filter,
        ),
        event.filter,
      );
    }
  }

  Stream<FilteredTodosState> _mapTodosUpdatedToState(
    UpdateTodos event,
  ) async* {
    final visibilityFilter = currentState is FilteredTodosLoaded
        ? (currentState as FilteredTodosLoaded).activeFilter
        : VisibilityFilter.all;
    yield FilteredTodosLoaded(
      _mapTodosToFilteredTodos(
        (todosBloc.currentState as TodosLoaded).todos,
        visibilityFilter,
      ),
      visibilityFilter,
    );
  }

  List<Todo> _mapTodosToFilteredTodos(
      List<Todo> todos, VisibilityFilter filter) {
    return todos.where((todo) {
      if (filter == VisibilityFilter.all) {
        return true;
      } else if (filter == VisibilityFilter.active) {
        return !todo.complete;
      } else if (filter == VisibilityFilter.completed) {
        return todo.complete;
      }
    }).toList();
  }

  @override
  void dispose() {
    todosSubscription.cancel();
    super.dispose();
  }
}
```
> TodosBloc에서 상태 변경을 감지할 수 있는 TodosState들의 스트림인 StreamSubscription을 만듭니다. bloc의 dispose 메소드를 오버라이드 해서 bloc이 dispose 될 때 subscription을  취소할 수 있습니다.

### Barrel File
filtered todos 클래스들을 편리하게 import 하기 위해 barrel file을 만듭시다.

blocs/filtered_todos/filtered_todos.dart를 만들고 3개의 파일을 export 합니다.
```dart
export './filtered_todos_bloc.dart';
export './filtered_todos_event.dart';
export './filtered_todos_state.dart';
```
다음으로 StatsBloc을 구현할 겁니다.

## StatsBloc
> StatsBloc은 active 상태 todos와 completed 상태 todos의 통계 데이터를 관리하는 책임을 집니다. FilteredTodosBloc과 유사하게 TodosBloc의 의존성을 갖습니다. 그래서 TodosBloc의 상태가 바뀌면 함께 반응합니다.

### State
StatsBloc은 2개의 상태를 가집니다.
- StatsLoading - 아직 통계 데이터가 계산되지 않은 상태
- StatsLoaded - 통계 데이터가 계산된 상태

blocs/stats/stats_state.dart를 만들고 StatsState를 구현합시다.
```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';

@immutable
abstract class StatsState extends Equatable {
  StatsState([List props = const []]) : super(props);
}

class StatsLoading extends StatsState {
  @override
  String toString() => 'StatsLoading';
}

class StatsLoaded extends StatsState {
  final int numActive;
  final int numCompleted;

  StatsLoaded(this.numActive, this.numCompleted)
      : super([numActive, numCompleted]);

  @override
  String toString() {
    return 'StatsLoaded { numActive: $numActive, numCompleted: $numCompleted }';
  }
}
```
다음으로 StatsEvent들을 구현합시다.

### Events
StatsBloc에는 UpdateStats라는 하나의 이벤트만 있습니다. 이 이벤트는 TodosBloc의 상태가 바뀔 때마다 디스패치 됩니다. 이에 따라 StatsBloc도 새로운 통계데이터를 다시 계산합니다.

blocs/stats/stats_event.dart를 만들고 구현합시다.
```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/models/models.dart';

@immutable
abstract class StatsEvent extends Equatable {
  StatsEvent([List props = const []]) : super(props);
}

class UpdateStats extends StatsEvent {
  final List<Todo> todos;

  UpdateStats(this.todos) : super([todos]);

  @override
  String toString() => 'UpdateStats { todos: $todos }';
}
```
이제 우린 FilteredTodosBloc과 매우 유사한 StatsBloc을 구현할 겁니다.

### Bloc
StatsBloc은 TodosBloc의 의존성을 가집니다. TodosBloc에서 상태가 변경되면 따라서 StatsBloc의 상태도 갱신됩니다.

bloc/stats/stats_bloc.dart를 만들고 시작합시다.
```dart
import 'dart:async';
import 'package:meta/meta.dart';
import 'package:bloc/bloc.dart';
import 'package:flutter_todos/blocs/blocs.dart';

class StatsBloc extends Bloc<StatsEvent, StatsState> {
  final TodosBloc todosBloc;
  StreamSubscription todosSubscription;

  StatsBloc({@required this.todosBloc}) {
    todosSubscription = todosBloc.state.listen((state) {
      if (state is TodosLoaded) {
        dispatch(UpdateStats(state.todos));
      }
    });
  }

  @override
  StatsState get initialState => StatsLoading();

  @override
  Stream<StatsState> mapEventToState(StatsEvent event) async* {
    if (event is UpdateStats) {
      int numActive =
          event.todos.where((todo) => !todo.complete).toList().length;
      int numCompleted =
          event.todos.where((todo) => todo.complete).toList().length;
      yield StatsLoaded(numActive, numCompleted);
    }
  }

  @override
  void dispose() {
    todosSubscription.cancel();
    super.dispose();
  }
}
```
StatsBloc은 TodosBloc의 상태가 변경될 때마다 active todos의 수와 completed todos의 수를 다시 계산합니다.

이제 StatsBloc의 구현이 끝났습니다. 남은 하나의 bloc은 TabBloc입니다.

## TabBloc
> TabBloc은 앱의 탭 상태를 관리하는 책임을 가집니다. TabBloc은 TabEvent를 입력받아 AppTab를 출력합니다.

### Model / State
우리는 TabState를 대표하는 AppTab 모델을 정의할 겁니다. AppTab은 enum 타입입니다.

models/app_tab.dart를 만듭시다.
```dart
enum AppTab { todos, stats }
```

### Event
TabBloc은 하나의 TabEvent를 다루는 책임을 가집니다.
- UpdateTab - bloc에게 tab이 갱신됐음을 알립니다.

blocs/tab/tab_event.dart를 만듭시다.
```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';
import 'package:flutter_todos/models/models.dart';

@immutable
abstract class TabEvent extends Equatable {
  TabEvent([List props = const []]) : super(props);
}

class UpdateTab extends TabEvent {
  final AppTab tab;

  UpdateTab(this.tab) : super([tab]);

  @override
  String toString() => 'UpdateTab { tab: $tab }';
}
```

### Bloc
TabBloc은 구현은 매우 단순합니다. 

blocs/tab/tab_bloc.dart를 만듭시다.
```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:flutter_todos/blocs/tab/tab.dart';
import 'package:flutter_todos/models/models.dart';

class TabBloc extends Bloc<TabEvent, AppTab> {
  @override
  AppTab get initialState => AppTab.todos;

  @override
  Stream<AppTab> mapEventToState(TabEvent event) async* {
    if (event is UpdateTab) {
      yield event.tab;
    }
  }
}
```
TabBloc이 하는 일은 첫 상태를 설정하고 UpdateTab 이벤트를 AppTab으로 yielding 하는 것 뿐입니다.

### Barrel File
blocs/tab/tab.dart를 만듭시다.
```dart
export './tab_bloc.dart';
export './tab_event.dart';
```

## Bloc Delegate
프리젠테이션 레이어로 이동하기 전에 모든 상태 변경과 에러를 다룰 수 이게 해주는 BlocDelegate를 구현할 겁니다. 이것은 개발 로그나 분석에 아주 유용합니다.

blocs/simple_bloc_delegate.dart를 만듭시다.
```dart
import 'package:bloc/bloc.dart';

class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onTransition(Transition transition) {
    print(transition);
  }

  @override
  void onError(Object error, StackTrace stacktrace) {
    print(error);
  }
}
```
모든 상태 변경(transition)과 에러들을 콘솔에 출력합니다. 구글 애널리틱스나 sentry, crashlytics 등에 BlocDelegate을 훅업할 수 있습니다.

## Bloc Barrel
우리가 구현한 bloc들을 가지는 barrel file을 만듭시다. blocs/blocs.dart를 만들고 모든 bloc을 export 합니다.
```dart
export './filtered_todos/filtered_todos.dart';
export './stats/stats.dart';
export './tab/tab.dart';
export './todos/todos.dart';
export './simple_bloc_delegate.dart';
```

다음으로 우리는 주요 화면들에 초점을 맞출 겁니다.

## 추가 준비물
localization.dart
```dart
import 'dart:async';

import 'package:flutter/material.dart';

class FlutterBlocLocalizations {
  static FlutterBlocLocalizations of(BuildContext context) {
    return Localizations.of<FlutterBlocLocalizations>(
      context,
      FlutterBlocLocalizations,
    );
  }

  String get appTitle => "Flutter Todos";
}

class FlutterBlocLocalizationsDelegate
    extends LocalizationsDelegate<FlutterBlocLocalizations> {
  @override
  Future<FlutterBlocLocalizations> load(Locale locale) =>
      Future(() => FlutterBlocLocalizations());

  @override
  bool shouldReload(FlutterBlocLocalizationsDelegate old) => false;

  @override
  bool isSupported(Locale locale) =>
      locale.languageCode.toLowerCase().contains("en");
}
```

## Screens
### Home Screen
> HomeScreen은 Scaffold를 만드는 책임을 가집니다. AppBar와 BottomNavigationBar, Stats / FilteredTodos 위젯들을 관리합니다.

새 화면 위젯들을 넣을 screens라는 새 디렉토리를 만들고 그 안에 screens/home_screen.dart를 만들 겁니다.

HomeScreen은 TabBloc, FilteredTodosBloc, StatsBloc을 만들고 dispose할 것이기 때문에 StatefulWidget로 만들 겁니다.
```dart
import 'package:flutter/material.dart';
import 'package:todos_app_core/todos_app_core.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_todos/blocs/blocs.dart';
import 'package:flutter_todos/widgets/widgets.dart';
import 'package:flutter_todos/localization.dart';
import 'package:flutter_todos/models/models.dart';

class HomeScreen extends StatefulWidget {
  final void Function() onInit;

  HomeScreen({@required this.onInit}) : super(key: ArchSampleKeys.homeScreen);

  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  final TabBloc _tabBloc = TabBloc();
  FilteredTodosBloc _filteredTodosBloc;
  StatsBloc _statsBloc;

  @override
  void initState() {
    widget.onInit();
    _filteredTodosBloc = FilteredTodosBloc(
      todosBloc: BlocProvider.of<TodosBloc>(context),
    );
    _statsBloc = StatsBloc(
      todosBloc: BlocProvider.of<TodosBloc>(context),
    );
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return BlocBuilder(
      bloc: _tabBloc,
      builder: (BuildContext context, AppTab activeTab) {
        return BlocProviderTree(
          blocProviders: [
            BlocProvider<TabBloc>(bloc: _tabBloc),
            BlocProvider<FilteredTodosBloc>(bloc: _filteredTodosBloc),
            BlocProvider<StatsBloc>(bloc: _statsBloc),
          ],
          child: Scaffold(
            appBar: AppBar(
              title: Text(FlutterBlocLocalizations.of(context).appTitle),
              actions: [
                FilterButton(visible: activeTab == AppTab.todos),
                ExtraActions(),
              ],
            ),
            body: activeTab == AppTab.todos ? FilteredTodos() : Stats(),
            floatingActionButton: FloatingActionButton(
              key: ArchSampleKeys.addTodoFab,
              onPressed: () {
                Navigator.pushNamed(context, ArchSampleRoutes.addTodo);
              },
              child: Icon(Icons.add),
              tooltip: ArchSampleLocalizations.of(context).addTodo,
            ),
            bottomNavigationBar: TabSelector(
              activeTab: activeTab,
              onTabSelected: (tab) => _tabBloc.dispatch(UpdateTab(tab)),
            ),
          ),
        );
      },
    );
  }

  @override
  void dispose() {
    _statsBloc.dispose();
    _filteredTodosBloc.dispose();
    _tabBloc.dispose();
    super.dispose();
  }
}
```
HomeScreen은 상태의 일부로써 TabBloc, FilteredTodosBloc, StatsBloc을 만듭니다. HomeScreen은 TodosApp의 루트에서 사용 가능하게 만들어진 TodosBloc에 접근하기 위해 BlocProvider.of<TodosBloc>(context)를 사용합니다.

HomeScreen은 TodosBloc 상태의 변경에 대해 반응해야 하기 때문에 TodosState를 기반으로 위젯을 빌드하기 위해 BlocBuilder를 사용합니다.

HomeScreen은 BlocProviderTree 위젯을 사용해서 서브트리에 있는 위젯들이 TabBloc, FilteredTodosBloc, StatsBloc에 접근할 수 있도록 합니다.
```dart
BlocProviderTree(
  blocProviders: [
    BlocProvider<TabBloc>(bloc: _tabBloc),
    BlocProvider<FilteredTodosBloc>(bloc: _filteredTodosBloc),
    BlocProvider<StatsBloc>(bloc: _statsBloc),
  ],
  child: Scaffold(...),
);
```
위 코드와 아래 코드는 같습니다.
```dart
BlocProvider<TabBloc>(
  bloc: _tabBloc,
  child: BlocProvider<FilteredTodosBloc>(
    bloc: _filteredTodosBloc,
    child: BlocProvider<StatsBloc>(
      bloc: _statsBloc,
      child: Scaffold(...),
    ),
  ),
);
```
위에서 본 것처럼 BlocProviderTree는 중첩 레벨을 감소시키고 가독성을 높혀줍니다.

다음으로 DetailsScreen을 구현할 겁니다.

### Details Screen
> DetailsScreen은 선택된 투의 상세정보를 보여주고 사용자가 투두를 수정하거나 삭제할 수 있게 해줍니다.

screens/details_screen.dart를 만듭시다.
```dart
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:todos_app_core/todos_app_core.dart';
import 'package:flutter_todos/blocs/todos/todos.dart';
import 'package:flutter_todos/screens/screens.dart';
import 'package:flutter_todos/flutter_todos_keys.dart';

class DetailsScreen extends StatelessWidget {
  final String id;

  DetailsScreen({Key key, @required this.id})
      : super(key: key ?? ArchSampleKeys.todoDetailsScreen);

  @override
  Widget build(BuildContext context) {
    final todosBloc = BlocProvider.of<TodosBloc>(context);
    return BlocBuilder(
      bloc: todosBloc,
      builder: (BuildContext context, TodosState state) {
        final todo = (state as TodosLoaded)
            .todos
            .firstWhere((todo) => todo.id == id, orElse: () => null);
        final localizations = ArchSampleLocalizations.of(context);
        return Scaffold(
          appBar: AppBar(
            title: Text(localizations.todoDetails),
            actions: [
              IconButton(
                tooltip: localizations.deleteTodo,
                key: ArchSampleKeys.deleteTodoButton,
                icon: Icon(Icons.delete),
                onPressed: () {
                  todosBloc.dispatch(DeleteTodo(todo));
                  Navigator.pop(context, todo);
                },
              )
            ],
          ),
          body: todo == null
              ? Container(key: FlutterTodosKeys.emptyDetailsContainer)
              : Padding(
                  padding: EdgeInsets.all(16.0),
                  child: ListView(
                    children: [
                      Row(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: [
                          Padding(
                            padding: EdgeInsets.only(right: 8.0),
                            child: Checkbox(
                                key: FlutterTodosKeys.detailsScreenCheckBox,
                                value: todo.complete,
                                onChanged: (_) {
                                  todosBloc.dispatch(
                                    UpdateTodo(
                                      todo.copyWith(complete: !todo.complete),
                                    ),
                                  );
                                }),
                          ),
                          Expanded(
                            child: Column(
                              crossAxisAlignment: CrossAxisAlignment.start,
                              children: [
                                Hero(
                                  tag: '${todo.id}__heroTag',
                                  child: Container(
                                    width: MediaQuery.of(context).size.width,
                                    padding: EdgeInsets.only(
                                      top: 8.0,
                                      bottom: 16.0,
                                    ),
                                    child: Text(
                                      todo.task,
                                      key: ArchSampleKeys.detailsTodoItemTask,
                                      style:
                                          Theme.of(context).textTheme.headline,
                                    ),
                                  ),
                                ),
                                Text(
                                  todo.note,
                                  key: ArchSampleKeys.detailsTodoItemNote,
                                  style: Theme.of(context).textTheme.subhead,
                                ),
                              ],
                            ),
                          ),
                        ],
                      ),
                    ],
                  ),
                ),
          floatingActionButton: FloatingActionButton(
            key: ArchSampleKeys.editTodoFab,
            tooltip: localizations.editTodo,
            child: Icon(Icons.edit),
            onPressed: todo == null
                ? null
                : () {
                    Navigator.of(context).push(
                      MaterialPageRoute(
                        builder: (context) {
                          return AddEditScreen(
                            key: ArchSampleKeys.editTodoScreen,
                            onSave: (task, note) {
                              todosBloc.dispatch(
                                UpdateTodo(
                                  todo.copyWith(task: task, note: note),
                                ),
                              );
                            },
                            isEditing: true,
                            todo: todo,
                          );
                        },
                      ),
                    );
                  },
          ),
        );
      },
    );
  }
}
```
> DetailsScreen은 TodosBloc으로부터 투두의 id를 전달받아 id에 해당하는 투두의 상세정보가 바뀔 때마다 갱신합니다.

DetailsScreen에는 DeleteTodo 이벤트를 디스패치하는 IconButton과 UpdateTodo 이벤트를 디스패치하는 CheckBox 위젯이 있습니다. FloatingActionButton은 isEditing을 true로 설정하고 AddEditScreen으로 navigating 시킵니다. 다음으로 AddEditScreen을 봅시다.

### Add/Edit Screen
> AddEditScreen 위젯은 사용자가 새 투두를 만들 수 있게 해줍니다. 그리고 생성자로 넘어온 isEditing이 true일 땐 존재하는 투두를 수정할 수 있게 해줍니다.

screens/add_edit_screen.dart를 만듭시다.
```dart
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:todos_app_core/todos_app_core.dart';
import 'package:flutter_todos/models/models.dart';

typedef OnSaveCallback = Function(String task, String note);

class AddEditScreen extends StatefulWidget {
  final bool isEditing;
  final OnSaveCallback onSave;
  final Todo todo;

  AddEditScreen({
    Key key,
    @required this.onSave,
    @required this.isEditing,
    this.todo,
  }) : super(key: key ?? ArchSampleKeys.addTodoScreen);

  @override
  _AddEditScreenState createState() => _AddEditScreenState();
}

class _AddEditScreenState extends State<AddEditScreen> {
  static final GlobalKey<FormState> _formKey = GlobalKey<FormState>();

  String _task;
  String _note;

  bool get isEditing => widget.isEditing;

  @override
  Widget build(BuildContext context) {
    final localizations = ArchSampleLocalizations.of(context);
    final textTheme = Theme.of(context).textTheme;

    return Scaffold(
      appBar: AppBar(
        title: Text(
          isEditing ? localizations.editTodo : localizations.addTodo,
        ),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: ListView(
            children: [
              TextFormField(
                initialValue: isEditing ? widget.todo.task : '',
                key: ArchSampleKeys.taskField,
                autofocus: !isEditing,
                style: textTheme.headline,
                decoration: InputDecoration(
                  hintText: localizations.newTodoHint,
                ),
                validator: (val) {
                  return val.trim().isEmpty
                      ? localizations.emptyTodoError
                      : null;
                },
                onSaved: (value) => _task = value,
              ),
              TextFormField(
                initialValue: isEditing ? widget.todo.note : '',
                key: ArchSampleKeys.noteField,
                maxLines: 10,
                style: textTheme.subhead,
                decoration: InputDecoration(
                  hintText: localizations.notesHint,
                ),
                onSaved: (value) => _note = value,
              )
            ],
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        key:
            isEditing ? ArchSampleKeys.saveTodoFab : ArchSampleKeys.saveNewTodo,
        tooltip: isEditing ? localizations.saveChanges : localizations.addTodo,
        child: Icon(isEditing ? Icons.check : Icons.add),
        onPressed: () {
          if (_formKey.currentState.validate()) {
            _formKey.currentState.save();
            widget.onSave(_task, _note);
            Navigator.pop(context);
          }
        },
      ),
    );
  }
}
```
AddEditScreen은 새로운 투두가 만들어졌거나 기존의 투두가 수정됐을 때 부모 위젯에게 알리기 위해 onSave 콜백을 사용합니다.

### Screens Barrel
screens/screens.dart를 만듭시다.
```dart
export './add_edit_screen.dart';
export './details_screen.dart';
export './home_screen.dart';
```

다음으로 widgets의 모든 것을 구현합시다.

## Widgets
### Filter Button
> FilterButton 위젯은 필터 옵션 목록을 사용자에게 제공하는 책임을 가집니다. 필터가 선택됐을 때 FilteredTodosBloc에게 알림을 보냅니다.

widgets 디렉토리를 만들고 그 안에 FilterButton을 구현할 widgets/filter_button.dart를 만듭시다.
```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:todos_app_core/todos_app_core.dart';
import 'package:flutter_todos/blocs/filtered_todos/filtered_todos.dart';
import 'package:flutter_todos/models/models.dart';

class FilterButton extends StatelessWidget {
  final bool visible;

  FilterButton({this.visible, Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final defaultStyle = Theme.of(context).textTheme.body1;
    final activeStyle = Theme.of(context)
        .textTheme
        .body1
        .copyWith(color: Theme.of(context).accentColor);
    final FilteredTodosBloc filteredTodosBloc =
        BlocProvider.of<FilteredTodosBloc>(context);
    return BlocBuilder(
        bloc: filteredTodosBloc,
        builder: (BuildContext context, FilteredTodosState state) {
          final button = _Button(
            onSelected: (filter) {
              filteredTodosBloc.dispatch(UpdateFilter(filter));
            },
            activeFilter: state is FilteredTodosLoaded
                ? state.activeFilter
                : VisibilityFilter.all,
            activeStyle: activeStyle,
            defaultStyle: defaultStyle,
          );
          return AnimatedOpacity(
            opacity: visible ? 1.0 : 0.0,
            duration: Duration(milliseconds: 150),
            child: visible ? button : IgnorePointer(child: button),
          );
        });
  }
}

class _Button extends StatelessWidget {
  const _Button({
    Key key,
    @required this.onSelected,
    @required this.activeFilter,
    @required this.activeStyle,
    @required this.defaultStyle,
  }) : super(key: key);

  final PopupMenuItemSelected<VisibilityFilter> onSelected;
  final VisibilityFilter activeFilter;
  final TextStyle activeStyle;
  final TextStyle defaultStyle;

  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<VisibilityFilter>(
      key: ArchSampleKeys.filterButton,
      tooltip: ArchSampleLocalizations.of(context).filterTodos,
      onSelected: onSelected,
      itemBuilder: (BuildContext context) => <PopupMenuItem<VisibilityFilter>>[
            PopupMenuItem<VisibilityFilter>(
              key: ArchSampleKeys.allFilter,
              value: VisibilityFilter.all,
              child: Text(
                ArchSampleLocalizations.of(context).showAll,
                style: activeFilter == VisibilityFilter.all
                    ? activeStyle
                    : defaultStyle,
              ),
            ),
            PopupMenuItem<VisibilityFilter>(
              key: ArchSampleKeys.activeFilter,
              value: VisibilityFilter.active,
              child: Text(
                ArchSampleLocalizations.of(context).showActive,
                style: activeFilter == VisibilityFilter.active
                    ? activeStyle
                    : defaultStyle,
              ),
            ),
            PopupMenuItem<VisibilityFilter>(
              key: ArchSampleKeys.completedFilter,
              value: VisibilityFilter.completed,
              child: Text(
                ArchSampleLocalizations.of(context).showCompleted,
                style: activeFilter == VisibilityFilter.completed
                    ? activeStyle
                    : defaultStyle,
              ),
            ),
          ],
      icon: Icon(Icons.filter_list),
    );
  }
}
```

> 다음 내용부터는 이전 내용들에 의해 이해가 수월해지기 때문에 생략합니다. 

### Extra Actinos
### Tab Selector
### Filtered Todos
### Todo Item
### Delete Todod SnackBar
### Loading Indicator
### Stats

## Putting it all together
