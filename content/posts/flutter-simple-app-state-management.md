---
title: Flutter로 개발하기(17) - 간단한 상태관리 앱
date: 2019-03-17 22:51:41
tags: [flutter]
---

# Simple app state management
원문: https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple

## 이번 예제
로그인화면, 카탈로그화면, 장바구니화면으로 구성된 앱입니다.

![example.gif](https://flutter.dev/development/data-and-backend/state-mgmt/model-shopper-screencast-e0ada0e83cd8e7fdcad84167b8f7ffd7eb5ef85b0cb8957f03c6f05bd16b1cea.gif)

![widget-tree.png](https://flutter.dev/development/data-and-backend/state-mgmt/simple-widget-tree-19cb2528c56ef04924de364b4d0e08b73f4bcf7231aad0d6bc0eb1919e543fb9.png)

우리는 최소한 6개의 위젯을 만들 겁니다. 

## 상태를 위로 올리기
플러터에서는 상태를 사용하는 위젯 위(부모 위젯)에 그 상태를 유지하는 게 좋습니다. 
```dart
// BAD: 이렇게 하지 마세요.
void myTapHandler() {
  var cartWidget = somehowGetMyCartWidget(); // 부모 위젯
  cartWidget.updateWith(item);
}
```
```dart
// BAD: 이렇게 하지 마세요.
Widget build(BuildContext context) {
  // 부모 위젯의 build() 메소드
  return SomeWidget();
}

void updateWith(Item item) {
  // UI를 직접 바꿉니다.
}
```
이러한 코드들이 잘 동작한다고 해도 바뀐 상태에 따라 UI를 직접 바꾸고 갱신하는 방법은 버그를 피하기 어렵습니다.

플러터에서는 상태가 바뀌면 위젯이 다시 만들어집니다. MyCart.updateWith(somethingNew) 대신에 MyCart(contents) 생성자를 사용하세요. 새로운 위젯의 생성은 build() 메소드 안에서만 할 수 있습니다. 그래서 위젯의 상태를 바꾸려면 그 상태를 부모 위젯에 두어야 합니다.

```dart
// GOOD
void myTapHandler(BuildContext context) {
  var cartModel = somehowGetMyCartModel(context); // 장바구니 상태
  cartModel.add(item);
}
``` 
```dart
// GOOD
Widget build(BuildContext context) {
  // 부모 위젯의 build() 메소드
  var cartModel = somehowGetMyCartModel(context); // 장바구니 상태
  return SomeWidget(
    // 장바구니의 현재 상태를 사용해서 UI를 생성합니다. 
    // ···
  );
}
```

장바구니 상태가 바뀌면 이전의 MyCart 위젯은 새로운 MyCart 위젯으로 대체됩니다. 
![state-change.png](https://flutter.dev/development/data-and-backend/state-mgmt/simple-widget-tree-with-cart-088b22c4ef4e4389a1cababaceaadcd36ba3de37613080942885263c36e29595.png)
이런 동작은 위젯이 왜 immutable인지 알 수 있게 해줍니다. 위젯은 바뀌지 않고 대체될 뿐입니다.

## 상태에 접근하기
사용자가 카탈로그 항목 중 하나를 클릭했을 때 그 항목은 장바구니에 추가됩니다. 어떻게 구현할까요? 

단순한 선택지는 MyListItem이 클릭됐을 때 호출할 수 있는 콜백을 제공하는 것입니다. 
```dart
@override
Widget build(BuildContext context) {
  // MyCatalog 위젯이 build() 메소드
  return SomeWidget(
    // 자식 위젯에 콜백 메소드를 인자로 넣습니다.
    MyListItem(myTapCallback),
  );
}

void myTapCallback(Item item) {
  print('user tapped on $item');
}
```
이 코드는 잘 동작하지만 앱 상태로 만들어야 할 땐 많은 콜백들을 만들어야 하며, 많은 곳들을 함께 수정해야 합니다.

다행히도 플러터에는 하위 계층에 있는 위젯들에게 데이터와 서비스를 제공할 수 있는 메커니즘이 있습니다. 이 메커니즘은 InheritedWidget, InheritedNotifier, InheritedModel에 의해 제공됩니다. 이 위젯들은 조금 low-level이기 때문에 우리는 이것들을 다루진 않고 이 위젯들을 간단하게 다룰 수 있는 패키지 scoped_model을 사용할 것입니다. 

scoped_model에는 3가지 컨셉이 있습니다.
- Model
- ScopedModel
- ScopedModelDescendant

## Model
scoped_model에서 Model은 앱 상태를 캡슐화합니다. 우리 쇼핑앱 예제에서 장바구니 상태를 관리하기 위해 Model 클래스를 상속할 것입니다.
```dart
class CartModel extends Model {
  /// private으로 선언된 장바구니의 상태
  final List<Item> _items = []; 

  /// 장바구니에서 목록뷰를 수정할 수 없게 합니다.
  UnmodifiableListView<Item> get items => UnmodifiableListView(_items);

  /// 모든 항목들의 현재 합산가격 (모든 항목의 가격이 1$라고 가정합니다.)
  int get totalPrice => _items.length;

  /// 장바구니에 항목을 추가합니다. 이것이 외부에서 장바구니를 수정할 수 있는 유일한 방법입니다.
  void add(Item item) {
    _items.add(item);
    // 이 호출은 Model에게 위젯들이 리빌드 되어야 한다고 말해줍니다.
    notifyListeners();
  }
}
```

Model은 어느 클래스도 의존하지 않기 때문에 테스트 하기 쉽습니다.
```dart
test('adding item increases total cost', () {
  final cart = CartModel();
  final startingPrice = cart.totalPrice;
  cart.addListener(() {
    expect(cart.totalPrice, greaterThan(startingPrice));
  });
  cart.add(Item('Dash'));
});
```

## ScopedModel(위젯)
ScopedModel은 Model 인스턴스를 하위 계층의 위젯들에게 제공합니다. 

우리는 이미 이것을 어디에 넣어야 할지 압니다. CartModel의 경우 MyCart와 MyCatalog의 공통 조상 위젯에 넣습니다.
```dart
void main() {
  final cart = CartModel();

  // You could optionally connect [cart] with some database here.

  runApp(
    ScopedModel<CartModel>(
      model: cart,
      child: MyApp(),
    ),
  );
}
```

만약 여러 Model을 사용하기 원한다면 중첩시키면 됩니다.
```dart
ScopedModel<SomeOtherModel>(
  model: myOtherModel,
  child: ScopedModel<CartModel>(
    model: cart,
    child: MyApp(),
  ),
)
```

## ScopedModelDescendant
이제 CartModel을 ScopedModelDescendant을 통해 위젯들에게 제공할 차례입니다.
```dart
return ScopedModelDescendant<CartModel>(
  builder: (context, child, cart) {
    return Text("Total price: ${cart.totalPrice}");
  },
);
```

ScopedModelDescendant은 빌더입니다. 빌더는 함수입니다. notifyListeners()가 호출되면 ScopedModelDescendant는 다시 호출될 것입니다.

작은 부분을 바꾸자고 거대한 UI를 리빌드 하고 싶진 않을 겁니다.
```dart
// 이렇게는 하지 마세요.
return ScopedModelDescendant<CartModel>(
  builder: (context, child, cart) {
    return HumongousWidget(
      // ...
      child: AnotherMonstrousWidget(
        // ...
        child: Text('Total price: ${cart.totalPrice}'),
      ),
    );
  },
);
```
```dart
// 대신에 이렇게 하세요.
return HumongousWidget(
  // ...
  child: AnotherMonstrousWidget(
    // ...
    child: ScopedModelDescendant<CartModel>(
      builder: (context, child, cart) {
        return Text('Total price: ${cart.totalPrice}');
      },
    ),
  ),
);

```

### ScopedModel.of
때로는 리빌드 없이 상태를 바꿀 필요가 있습니다. 예를 들면 장바구니화면이 아닌 곳에서 "장바구니 비우기" 버튼을 눌렀을 때 장바구니 상태를 바꾸려고 ScopedModelDescendant를 사용하는 것은 낭비입니다. 이럴 때 ScopedModel.of를 사용하면 notifyListeners()가 호출돼도 위젯이 리빌드 되지 않습니다. 
```dart
ScopedModel.of<CartModel>(context).add(item);
```

## 완전한 예제 코드
[링크](https://github.com/filiph/samples/tree/scoped-model-shopper/model_shopper)