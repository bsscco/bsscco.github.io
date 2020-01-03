---
title: Flutter로 개발하기(01) - 위젯 소개
date: 2019-03-09 06:49:58
tags: [flutter]
---

# Introduction to widgets
https://flutter.dev/docs/development/ui/widgets-intro

플러터 위젯은 React로부터 영감을 받아 반응형 스타일로 제작됐습니다. 

플러터 위젯의 중심 아이디어는 위젯으로만 UI를 제작하는 것입니다. 위젯은 자신에게 주어진 상태에 따라 어떻게 그릴 것인가를 기술하는 객체입니다. 위젯의 상태가 바뀌면 위젯은 다시 빌드되는데, 이때 프레임워크는 이전 상태와 다음 상태를 비교해서 render tree가 최소한만 변경되도록 합니다.

## Hello World
플러터 앱은 runApp() 함수를 호출하는 것에서부터 시작됩니다.
```dart
import 'package:flutter/meterial.dart';

void main() {
  runApp(
    Center(
      child: Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```
![runApp.png](/2019-03-10/run-app.png)

`runApp()` 함수는 주어진 위젯으로 위젯트리의 루트를 만듭니다. 위 예제에서 위젯트리는 Center와 Text로 구성됩니다. 

위젯의 주요 기능은 build() 함수를 구현하는 것입니다.

우리는 일반적으로 상태가 없는 StatelessWidget 또는 상태를 가지는 StatefulWidget 클래스를 상속해서 새로운 위젯을 만들 것입니다.

## 기본 위젯
Flutter는 강력한 기본 위젯들을 제공합니다. 
- Text: 스타일을 적용할 수 있는 텍스트를 제공합니다.
- Row, Column: 수평적인 배치와 수직적인 배치를 할 수 있게 해줍니다. 이 위젯들은 웹의 flexbox layout model에 기초해서 설계됐습니다.
- Stack: 위젯들을 순서에 따라 쌓일 수 있게 해줍니다. Stack 위젯 안에서 Positioned 위젯을 사용하면 자식 위젯들의 상대적인 위치를 지정할 수 있습니다. Stack 위젯은 웹의 absolute positioning layout model을 기반으로 합니다.
- Container: 사각형의 보이는 요소를 만들어줍니다. BoxDecoration을 사용해서 테두리, 배경색, 그림자 등을 설정할 수 있습니다. Container는 그밖에 margin, padding, 자신의 크기에 적용할 제약조건도 가질 수 있습니다. 그리고 x, y, z 세축에서 모양을 변형시킬 수도 있습니다.

아래는 위젯들을 조합해 사용한 간단한 예입니다.
```dart
import 'package:flutter/material.dart';

class MyAppBar extends StatelessWidget {
  MyAppBar({this.title});

  // StatelessWidget의 필드는 항상 final로 선언합니다.
  final Widget title;

  @override
  Widget build(BuildContext context) {
    return Container(
      height: 156.0, // 플러터에서는 논리적인 픽셀단위를 사용합니다.
      padding: const EdgeInsets.symmetric(horizontal: 8.0),
      decoration: BoxDecoration(color: Colors.blue[500]),
      // Row는 수평적인 선형 배치입니다.
      child: Row(
        children: <Widget>[
          IconButton(
            icon: Icon(Icons.menu),
            tooltip: 'Navigation menu',
            onPressed: null, // onPressed에 null값을 설정하면 Button이 disable상태로 설정됩니다.
          ),
          // Expanded는 자식 위젯이 여백을 채우게 합니다.
          Expanded(
            child: title,
          ),
          IconButton(
            icon: Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
    );
  }
}

class MyScaffold extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Material(
      child: Column(
        children: <Widget>[
          MyAppBar(
            title: Text(
              'Example title',
              style: Theme.of(context).primaryTextTheme.title,
            ),
          ),
          Expanded(
            child: Center(
              child: Text('Hello, world!'),
            ),
          ),
        ],
      ),
    );
  }
}

void main() {
  runApp(MaterialApp(
    title: 'My app', // used by the OS task switcher
    home: MyScaffold(),
  ));
}
```
![combine-widgets.png](/2019-03-10/combine-widgets.png)

pubspec.yaml 파일에서 uses-material-design: true 으로 설정했는지 확인하세요. 이 설정은 미리 정의된 Material Icons를 사용할 수 있게 해줍니다.
```yaml
name: my_app
flutter:
  uses-material-design: true
```

## 머터리얼 컴포넌트 사용하기
플러터는 머터리얼 디자인을 위한 여러 위젯을 제공합니다. 머터리얼 앱은 MaterialApp 위젯을 루트로 하는 것에서 시작됩니다.
```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Flutter Tutorial',
    home: TutorialHome(),
  ));
}

class TutorialHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Scaffold는 주요 머터리얼 컴포넌트 사용을 위한 위젯입니다.
    return Scaffold(
      appBar: AppBar(
        leading: IconButton(
          icon: Icon(Icons.menu),
          tooltip: 'Navigation menu',
          onPressed: null,
        ),
        title: Text('Example title'),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
      // body는 화면에 보여지는 영역입니다.
      body: Center(
        child: Text('Hello, world!'),
      ),
      floatingActionButton: FloatingActionButton(
        tooltip: 'Add', // 접근성 기능에서 사용됩니다.
        child: Icon(Icons.add),
        onPressed: null,
      ),
    );
  }
}
```
![material-widgets.png](/2019-03-10/material-widgets.png)

위 예제에서는 MyAppBar와 MyScaffold를 material.dart의 AppBar와 Scaffold로 바꾸었습니다. 그 결과 앱바 하단 그림자가 자동으로 설정되는 등 더 머터리얼 스러워졌습니다. 

더 많은 정보: [Material components](https://flutter.dev/docs/development/ui/widgets/material)

## 제스처 다루기
대부분의 앱은 사용자입력 폼을 포함합니다. 상호작용하는 앱을 제작하는 첫 번째 스탭은 제스처 입력을 감지하는 것입니다. 어떻게 하는지 아래를 봅시다.
```dart
class MyButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        print('MyButton was tapped!');
      },
      child: Container(
        height: 36.0,
        padding: const EdgeInsets.all(8.0),
        margin: const EdgeInsets.symmetric(horizontal: 8.0),
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(5.0),
          color: Colors.lightGreen[500],
        ),
        child: Center(
          child: Text('Engage'),
        ),
      ),
    );
  }
}
```
 
GestureDetector 위젯은 사용자에게 보이지는 않지만 사용자의 제스처를 감지합니다. 사용자가 Container 위에서 tap 했을 때 GestureDetector는 자신의 onTab을 호출합니다. GestureDetector는 drag, scale 등도 감지할 수 있습니다. 
 
많은 위젯들이 GestureDetector를 사용합니다. IconButton, RaisedButton, FloatingActionButton 등은 사용자가 tab 했을 때 자신의 onPressed를 호출합니다.
 
더 많은 정보: [Gestures in Flutter](https://flutter.dev/docs/development/ui/advanced/gestures)

## 입력에 반응해 위젯 변경하기
지금까지는 StatelessWidget만 사용해봤습니다. StatelessWidget에서는 부모 위젯으로부터 인자를 내려받아 final 멤버로 저장합니다. 이 인자들은 build() 메소드에서 UI를 생성할 때 사용됩니다.

더 복잡한 UI를 빌드하기 위해서 어플리케이션은 상태(state)를 사용합니다. 플러터는 이 아이디어를 구현하기 위해 StatefulWidget을 사용합니다. StatefulWidget은 상태 객체를 생성할 수 있는 특별한 위젯입니다. RaisedButton을 사용하는 다음 예제을 보겠습니다.
```dart
class Counter extends StatefulWidget {
  // 이 위젯 클래스는 아래의 State를 위해 함께 구성돼어야 하는 클래스입니다. 
  // 이 클래스는 부모로부터 제공된 값들과 State의 build() 메소드를 위해 사용될 값들을 담습니다. 
  // 위젯 클래스의 모든 필드는 final로 선언되어야 합니다.

  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      // setState() 메소드는 Flutter 프레임워크에게 State가 변경됐다는 것을 말해줍니다.
      // 아래 build() 메소드가 다시 실행되고, 그로 인해 UI에 갱신된 값들이 반영됩니다.
      // 만약 setState() 메소드를 호출하지 않고 _counter를 변경한다면, build() 메소드는 호출되지 않을 것입니다. 그렇게 되면 UI도 바뀌지 않습니다.
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    // 이 메소드는 위의 setState() 메소드가 호출될 때마다 다시 실행됩니다. 
    // Flutter 프레임워크는 build() 메소드를 빠르게 다시 실행시킬 수 있도록 최적화 돼있습니다. 그래서 갱신이 필요할 때면 언제든 리빌드를 해도 괜찮습니다.
    return Row(
      children: <Widget>[
        RaisedButton(
          onPressed: _increment,
          child: Text('Increment'),
        ),
        Text('Count: $_counter'),
      ],
    );
  }
}
```
왜 StatefulWidget과 State가 두 객체로 분리되는지 궁금할 것입니다. 플러터에서 두 유형의 객체들은 다른 라이프사이클을 가집니다. Widget 객체는 현재 상태의 프리젠테이션을 만들기 위해 사용되는 일시적인 객체입니다. 반대로, State 객체는 build() 메소드의 여러 호출에서도 그 정보를 유지합니다. 

위 예제는 사용자 입력을 받아들이고, 그 결과를 build() 메소드에서 직접 사용합니다. 더 복잡한 어플리케이션에서는 위젯들이 각자 다른 동작을 책임질 것입니다. 예를 들어, 한 위젯은 정보를 모으는 목적으로 사용자 입력을 받는 복잡한 UI를 보여주고 , 또 다른 위젯은 전체적인 프리젠테이션의 변경을 위해 그 정보를 사용할 것입니다. 

플러터에서 변경에 대한 알림들은 콜백 방법을 통해 위젯 계층의 위 방향으로 흘러갑니다. 반면에, 현재 상태의 프레젠테이션은 위젯 계층의 아래에 있는 StatelessWidget을 향해 아래 방향으로 흘러갑니다. 이 흐름을 발생시키는 공통의 부모가 State입니다. 다음 예제는 이것이 어떻게 동작하는지 보여줍니다.
```dart
// 현재 상태를 프레젠테이션 하는 stateless 위젯
class CounterDisplay extends StatelessWidget {
  CounterDisplay({this.count});

  final int count;

  @override
  Widget build(BuildContext context) {
    return Text('Count: $count');
  }
}

// 사용자 입력을 받는 stateless 위젯
class CounterIncrementor extends StatelessWidget {
  CounterIncrementor({this.onPressed});

  final VoidCallback onPressed;

  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: onPressed,
      child: Text('Increment'),
    );
  }
}

// State를 위해 필요한 stateful 위젯
class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}

// 사용자 입력으로 모인 정보를 사용해 변경 알림의 흐름을 만들어내는 State
class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      ++_counter;
    });
  }

  // CounterIncrementor는 사용자 입력을 받아 정보를 모읍니다. 
  // 사용자 입력이 있을 때 _CounterState로부터 받은 콜백을 호출합니다. 정보는 이 콜백을 통해 위젯 계층 위로 흘러갑니다. 
  // _CounterState는 정보를 사용해 상태(_counter)를 변경하고, 이 상태 변경은 CounterDisplay가 있는 위젯 계층 아래로 흘러갑니다.   
  // CounterDisplay는 바뀐 상태를 UI에 반영합니다.
  @override
  Widget build(BuildContext context) {
    return Row(children: <Widget>[
      CounterIncrementor(onPressed: _increment), 
      CounterDisplay(count: _counter),  
    ]);
  }
}
```
카운터를 보여주는 것(CounterDisplay)과 카운터를 변경(CounterIncrementor)하는 것의 관심사의 분리를 명심하세요.   

더 많은 정보: 
- [StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)
- [State.setState](https://docs.flutter.io/flutter/widgets/State/setState.html)

## 위 내용들을 모두 사용한 예제
다음 예제는 위에서 소개한 것들을 함께 가져온 더 완전한 예제입니다. 가상의 쇼핑 어플리케이션은 세일 중인 다양한 제품을 보여줍니다. 이 앱은 장바구니 담기 기능도 가집니다. 프레젠테이션 클래스인 ShoppingListItem을 정의하는 것으로 시작합시다.
```dart
class Product {
  const Product({this.name});
  final String name;
}

typedef void CartChangedCallback(Product product, bool inCart);

class ShoppingListItem extends StatelessWidget {
  ShoppingListItem({Product product, this.inCart, this.onCartChanged})
      : product = product,
        super(key: ObjectKey(product));

  final Product product;
  final bool inCart;
  final CartChangedCallback onCartChanged;

  Color _getColor(BuildContext context) {
    // 여기서 사용되는 테마는 BuildContext에 의존합니다. tree의 다른 부분이 다른 테마를 가질 수 있기 때문입니다.
    // BuildContext는 build가 실행되는 위치를 가리킵니다.

    return inCart ? Colors.black54 : Theme.of(context).primaryColor;
  }

  TextStyle _getTextStyle(BuildContext context) {
    if (!inCart) return null;

    return TextStyle(
      color: Colors.black54,
      decoration: TextDecoration.lineThrough,
    );
  }

  @override
  Widget build(BuildContext context) {
    return ListTile(
      onTap: () {
        onCartChanged(product, !inCart);
      },
      leading: CircleAvatar(
        backgroundColor: _getColor(context),
        child: Text(product.name[0]),
      ),
      title: Text(product.name, style: _getTextStyle(context)),
    );
  }
}
```
ShoppingListItem 위젯은 stateless 위젯의 일반적인 패턴을 따릅니다. 이 위젯은 생성자에서 받은 값을 final 멤버 변수로 저장합니다. 이 값은 build() 함수에서 사용됩니다. 예를 들어, inCart boolean은 두 visual appearances 사이에서 토글됩니다. 하나는 현재 테마로부터 primary color를 사용하고, 다른 하나는 grey를 사용합니다.

사용자가 리스트 아이템을 tap 했을 때 이 위젯은 자신의 inCart 값을 직접 변경하지 않습니다. 대신에 부모 위젯으로부터 전달받았던 onCartChanged() 함수를 호출합니다. 이 패턴은 상태를 위젯 계층의 더 높은 곳에 저장하게 하고, 더 긴 시간 동안 상태가 유지되도록 합니다.  

부모 위젯이 onCartChanged 콜백을 받았을 때 부모 위젯은 상태를 갱신합니다. 갱신이 트리거가 되어 그 위젯을 리빌드 하게 하고, 새로운 inCart값을 포함하는 새로운 ShoppingListItem 객체가 생성되게 됩니다. 비록 부모 위젯이 리빌드 과정에서 새로운 ShoppingListItem 객체를 생성한다고 해도 프레임워크는 새롭게 빌드 되어야 할 위젯들을 비교하고 RenderObject 위에서 필요한 부분만 적용하기 때문에 이 명령에 들어가는 비용은 적습니다.      

다음은 변경 가능한 상태를 저장하는 부모 위젯의 예제입니다.
```dart
class ShoppingList extends StatefulWidget {
  ShoppingList({Key key, this.products}) : super(key: key);

  final List<Product> products;

  // 프레임워크는 위젯이 tree에 위치가 주어지고 나타나는 처음 한 번 createState() 함수를 호출합니다.
  // 만약 부모 위젯이 같은 위젯 유형(같은 key)을 사용하고 rebuild 한다면, 프레임워크는 State 객체를 생성하는 대신에 재사용할 것입니다.  

  @override
  _ShoppingListState createState() => _ShoppingListState();
}

class _ShoppingListState extends State<ShoppingList> {
  Set<Product> _shoppingCart = Set<Product>();

  void _handleCartChanged(Product product, bool inCart) {
    setState(() {
      // 사용자가 카트 안의 무언가를 변경할 때 rebuild를 실행시키기 위해 setState() 메소드 안에서 _shoppingCart의 변경하는 동작이 필요합니다.
      // 그러면 프레임워크는 build를 호출할 것이고, 앱의 visual appearance는 갱신될 것입니다.

      if (inCart)
        _shoppingCart.add(product);
      else
        _shoppingCart.remove(product);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Shopping List'),
      ),
      body: ListView(
        padding: EdgeInsets.symmetric(vertical: 8.0),
        children: widget.products.map((Product product) {
          return ShoppingListItem(
            product: product,
            inCart: _shoppingCart.contains(product),
            onCartChanged: _handleCartChanged,
          );
        }).toList(),
      ),
    );
  }
}

void main() {
  runApp(MaterialApp(
    title: 'Shopping App',
    home: ShoppingList(
      products: <Product>[
        Product(name: 'Eggs'),
        Product(name: 'Flour'),
        Product(name: 'Chocolate chips'),
      ],
    ),
  ));
}
```
![shopping-list.png](/2019-03-10/shopping-list.png)

ShoppingList 클래스는 StatefulWidget을 상속합니다. 이것은 변경 가능한 상태를 저장한다는 것을 의미합니다. ShoppingList 위젯이 처음 tree에 삽입될 때 프레임워크는 최신의 _ShoppingListState 객체를 생성하기 위해 createState() 함수를 호출합니다. (State의 서브클래스들은 private으로 구현되도록 하는 underscore(_)로 이름이 시작되도록 이름지어야 한다는 것을 명심하세요.)  

ShoppingList 위젯의 부모가 리빌드 될 때 ShoppingList 객체도 새로 만들 것입니다. ShoppingList의 재생성과 달리 프레임워크는 _ShoppingListState를 재사용할 것입니다.  

_ShoppingListState는 현재의 ShoppingList의 속성에 접근하기 위해서 `widget` 속성을 사용합니다. 만약 부모 위젯이 리빌드 되고 새로운 ShoppingList를 생성된다면, widget 속성도 바뀔 것입니다. `widget` 속성이 바뀌었을 때 알림을 받고 싶다면, didUpdateWidget() 함수를 override 할 수 있습니다.  

onCartChanged() 콜백이 핸들링될 때 _ShoppingListState는 _shoppingCart로부터 제품을 삭제하거나 추가하는 등 자신의 상태를 변경합니다. 이 상태가 바뀌었다는 신호를 프레임워크에게 보내기 위해 setState() 함수를 호출합니다. setState() 호출은 위젯이 바뀌어야 할 부분을 추려내고, 앱이 화면을 갱신하는 다음 시간에 리빌드 되도록 스케줄링 합니다. 만약 setState()를 호출하는 것을 잊어버린다면, 프레임워크는 위젯의 변경될 부분을 알지 못할 것이고 위젯의 build() 함수도 호출되지 않을 것입니다. 그것은 UI가 변경된 상태를 반영하지 않을 것을 의미합니다.

이 방법으로 상태를 관리함으로써 자식 위젯들을 생성하고 갱신하는 코드를 분리해서 작성할 필요가 없습니다. 대신에 간단히 build() 함수를 구현하기만 하면 됩니다.

## 위젯의 생명주기
StatefulWidget에서 createState() 함수를 호출한 후에 프레임워크는 새로운 상태 객체를 tree에 삽입합니다. 그리고 상태 객체의 initState()를 호출합니다. State의 서브클래스는 initState()를 override할 수 있습니다. 예를 들어, 애니메이션을 구성하거나 플랫폼 서비스를 구독하기 위해 initState()를 override 할 수 있습니다. initState()의 구현부는 super.initState()의 호출로 시작되어야 합니다.

상태 객체가 더 이상 필요하지 않을 때 프레임워크는 상태 객체의 dispose()를 호출합니다. 작업은 clean up 하기 위해 dispose() 함수를 override 할 수 있습니다. 예를 들어, 타이머를 취소하거나 플랫폼 서비스로부터 구독을 해지하기 위해 dispose()를 override 할 수 있습니다. 일반적으로 구현부의 끝에 super.dispose()를 삽입합니다.

더 많은 정보: [State](https://docs.flutter.io/flutter/widgets/State-class.html)

## Keys
위젯이 rebuild 될 때 프레임워크가 위젯을 매칭시키는 것을 제어하기 위해 key를 사용할 수 있습니다. 기본적으로 프레임워크는 runtimeType과 표시 순서로 현재와 이전 빌드의 위젯을 매칭합니다. key를 사용하면, 프레임워크는 현재와 이전 빌드의 위젯들이 동일한 runtimeType과 동일한 key를 가질 것을 요구합니다. 

key는 같은 유형의 많은 위젯 인스턴스들을 build 하는 위젯에서 가장 유용합니다. 예를 들어, ShoppingList 위젯은 표시 영역들을 채우기에 충분히 많은 ShoppingListItem 인스턴스들을 build 한다고 가정해봅시다. 

key가 없다면, 현재 build의 첫 번째 항목은 이전 빌드의 첫 번째 항목과 항상 동기화 됩니다. (역자 주:  ListView를 스크롤하면서 ListView Top에 걸렸던 항목 A는 위로 올라가고, A 아래에 있던 항목 B가 ListView Top에 걸리게 된다고 가정했을 때 A, B 두 항목에 key가 없으면 프레임워크는 항목 A, B 둘 다 같은 runtimeType, 같은 표시 순서이기 때문에 같은 위젯으로 인식하고 같은 상태를 공유하게 만들어버리는 문제가 발생합니다.)    

각 항목에 의미를 지니는 key를 할당함으로써 infinite 리스트는 똑똑해집니다. 각 항목 위젯에 유지되는 상태가 뷰포트 기반이 아니라, 동일한 의미 순서에 맞게 제대로 연결되는 것입니다.  

더 많은 정보: [Key API](https://docs.flutter.io/flutter/foundation/Key-class.html)

## 전역 Keys
자식 위젯들을 구별하기 위해 global key를 사용할 수 있습니다. global key는 모든 위젯 계층을 통틀어 유니크 해야 합니다. global key는 주어진 위젯과 연결된 상태를 찾는 데 사용될 수 있습니다.

더 많은 정보: [GlobalKey API](https://docs.flutter.io/flutter/widgets/GlobalKey-class.html)