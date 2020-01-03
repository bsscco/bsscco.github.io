---
title: Flutter로 개발하기(02) - 레이아웃 위젯 소개
date: 2019-03-11 07:49:13
tags: [flutter]
---

# Layouts in Flutter
https://flutter.dev/docs/development/ui/layout

## 위젯 배치하기
### 1. 레이아웃 위젯을 선택합니다.
Column, Row, Center 등

### 2. visible 위젯을 생성합니다.
```dart
Text('Hello World'),

Image.asset(
  'images/lake.jpg',
  fit: BoxFit.cover,
),

Icon(
  Icons.star,
  color: Colors.red[500],
),
```

### 3. visible 위젯을 레이아웃 위젯에 추가합니다.
```dart
Center(
  child: Text('Hello World'),
),
```

### 4. 레이아웃 위젯을 페이지에 추가합니다.
```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter layout demo',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Flutter layout demo'),
        ),
        body: Center(
          child: Text('Hello World'),
        ),
      ),
    );
  }
}
```

## 여러 위젯을 수직, 수평으로 배치하기
### 위젯 Aligning
#### Row
![aligning-row.png](https://flutter.dev/ui/layout/row-diagram-ad51795e19e3e1d412815b287c9caa694ad357892e3ab8b3ef1da0c4c6e011db.png)
```dart
Row(
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  children: [
    Image.asset('images/pic1.jpg'),
    Image.asset('images/pic2.jpg'),
    Image.asset('images/pic3.jpg'),
  ],
);
```
![aligning-row.png](https://flutter.dev/ui/layout/row-spaceevenly-visual-bae00ee7fc0b8a0fbe46d74b113f9f0e4e4942e55609cf263cefca81f986d871.png)

#### Column
![aligning-column.png](https://flutter.dev/ui/layout/column-diagram-4e2ce8e33c32a09d090280fb7b2925baaf58f6de7876a551c207ab904e2fafc6.png)
```dart
Column(
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  children: [
    Image.asset('images/pic1.jpg'),
    Image.asset('images/pic2.jpg'),
    Image.asset('images/pic3.jpg'),
  ],
);
```
![aligning-column.png](https://flutter.dev/ui/layout/column-visual-40850f31d83815ccf693076198ae200ed46bc633e185de8484081cde83e9f1d3.png)

### 위젯 Sizing
```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.center,
  children: [
    Expanded(
      child: Image.asset('images/pic1.jpg'),
    ),
    Expanded(
      child: Image.asset('images/pic2.jpg'),
    ),
    Expanded(
      child: Image.asset('images/pic3.jpg'),
    ),
  ],
);
```
![sizing-row-1.png](https://flutter.dev/ui/layout/row-expanded-2-visual-7003ec3a27b38a2cc925b97abfd933fe6d2fce62494400e0e3c9790b715cd0d1.png)

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.center,
  children: [
    Expanded(
      child: Image.asset('images/pic1.jpg'),
    ),
    Expanded(
      flex: 2,
      child: Image.asset('images/pic2.jpg'),
    ),
    Expanded(
      child: Image.asset('images/pic3.jpg'),
    ),
  ],
);
```
![sizing-row-2.png](https://flutter.dev/ui/layout/row-expanded-visual-fa9a01df7b96ba5cb33162b91369658fea86554139953e3cc0357de83281133d.png)

### 위젯 Packing
```dart
Row(
  mainAxisSize: MainAxisSize.min,
  children: [
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.black),
    Icon(Icons.star, color: Colors.black),
  ],
)
```
![packing-row.png](https://flutter.dev/ui/layout/packed-5583166ea8f018a2c3c829cb075eb63b465cf859e74ae1d7792d2c50813e32aa.png)

### Row와 Column을 다양하게 중첩하기
원하는 만큼 레이아웃들을 조합하고 커스터마이징 할 수 있습니다.

## 공통 레이아웃 위젯
### 기본 위젯
- Container: 자식 위젯에 패딩, 마진, 테두리, 배경색, 그 외 데코레이들을 위젯에 추가합니다.
- GridView: 자식 위젯들을 스크롤 가능한 그리드 형태로 배치합니다.
- ListView: 자식 위젯들을 스크롤 가능한 리스트 형태로 배치합니다.
- Stack: 자식 위젯들을 중첩시킵니다. 

### 머터리얼 위젯
- Card: 자식 위젯들을 라운드 모서리와 그림자를 가지는 박스 안에 담습니다. 
- ListTile: 행 안에 최대 3줄의 텍스트, 타이틀, 아이콘을 넣습니다.  

### Container
패딩, 마진, 테두리, 테두리, 배경색을 추가하기 위해 컨테이너로 감쌉니다.

![container.png](https://flutter.dev/ui/layout/margin-padding-border-9616dd0d7af45b95e6fcface25cd933b6b4a0fda51c1ab1bb9287bc8ed92c356.png)

#### Container 배경색
```dart
Widget _buildImageColumn() => Container(
      decoration: BoxDecoration(
        color: Colors.black26,
      ),
      child: Column(
        children: [
          _buildImageRow(1),
          _buildImageRow(3),
        ],
      ),
    );
```
![container-bg.png](https://flutter.dev/ui/layout/container-c31fce287ed0d6c2b0de32af625b2351bd8a08c236a2e60578f940c7b4aceece.png)

#### Container 테두리
```dart
Widget _buildDecoratedImage(int imageIndex) => Expanded(
      child: Container(
        decoration: BoxDecoration(
          border: Border.all(width: 10, color: Colors.black38),
          borderRadius: const BorderRadius.all(const Radius.circular(8)),
        ),
        margin: const EdgeInsets.all(4),
        child: Image.asset('images/pic$imageIndex.jpg'),
      ),
    );

Widget _buildImageRow(int imageIndex) => Row(
      children: [
        _buildDecoratedImage(imageIndex),
        _buildDecoratedImage(imageIndex + 1),
      ],
    );
```

### GridView
```dart
Widget _buildGrid() => GridView.extent(
    maxCrossAxisExtent: 150,
    padding: const EdgeInsets.all(4),
    mainAxisSpacing: 4,
    crossAxisSpacing: 4,
    children: _buildGridTileList(30));

List<Container> _buildGridTileList(int count) => List.generate(
    count, (i) => Container(child: Image.asset('images/pic$i.jpg')));
```
![grid.png](https://flutter.dev/ui/layout/gridview-extent-b36976ff50ecc7d55518fa41c10ac3d6de2c0e8a19d550e607461b46e8722395.png)


### ListView
```dart
Widget _buildList() => ListView(
      children: [
        _tile('CineArts at the Empire', '85 W Portal Ave', Icons.theaters),
        _tile('The Castro Theater', '429 Castro St', Icons.theaters),
        _tile('Alamo Drafthouse Cinema', '2550 Mission St', Icons.theaters),
        _tile('Roxie Theater', '3117 16th St', Icons.theaters),
        _tile('United Artists Stonestown Twin', '501 Buckingham Way',
            Icons.theaters),
        _tile('AMC Metreon 16', '135 4th St #3000', Icons.theaters),
        Divider(),
        _tile('Kescaped_code#39;s Kitchen', '757 Monterey Blvd', Icons.restaurant),
        _tile('Emmyescaped_code#39;s Restaurant', '1923 Ocean Ave', Icons.restaurant),
        _tile(
            'Chaiya Thai Restaurant', '272 Claremont Blvd', Icons.restaurant),
        _tile('La Ciccia', '291 30th St', Icons.restaurant),
      ],
    );

ListTile _tile(String title, String subtitle, IconData icon) => ListTile(
      title: Text(title,
          style: TextStyle(
            fontWeight: FontWeight.w500,
            fontSize: 20,
          )),
      subtitle: Text(subtitle),
      leading: Icon(
        icon,
        color: Colors.blue[500],
      ),
    );
```
![list.png](https://flutter.dev/ui/layout/listview-34918a738b5d18af88d16a8773e563a3b97f2ffd2710304c5543f8541cbd1345.png)

### Stack
```dart
Widget _buildStack() => Stack(
    alignment: const Alignment(0.6, 0.6),
    children: [
      CircleAvatar(
        backgroundImage: AssetImage('images/pic.jpg'),
        radius: 100,
      ),
      Container(
        decoration: BoxDecoration(
          color: Colors.black45,
        ),
        child: Text(
          'Mia B',
          style: TextStyle(
            fontSize: 20,
            fontWeight: FontWeight.bold,
            color: Colors.white,
          ),
        ),
      ),
    ],
  );
```
![stack.png](https://flutter.dev/ui/layout/stack-8b743fe7d0f5fe7e58e3a257572f4b48b1552194f7e3f3ffa15a87edaf99a749.png)

### Card
```dart
Widget _buildCard() => SizedBox(
    height: 210,
    child: Card(
      child: Column(
        children: [
          ListTile(
            title: Text('1625 Main Street',
                style: TextStyle(fontWeight: FontWeight.w500)),
            subtitle: Text('My City, CA 99984'),
            leading: Icon(
              Icons.restaurant_menu,
              color: Colors.blue[500],
            ),
          ),
          Divider(),
          ListTile(
            title: Text('(408) 555-1212',
                style: TextStyle(fontWeight: FontWeight.w500)),
            leading: Icon(
              Icons.contact_phone,
              color: Colors.blue[500],
            ),
          ),
          ListTile(
            title: Text('costa@example.com'),
            leading: Icon(
              Icons.contact_mail,
              color: Colors.blue[500],
            ),
          ),
        ],
      ),
    ),
  );
```
![card.png](https://flutter.dev/ui/layout/card-3db18421d33c96f34f8d19889a5f28c61287063a62c9770f311fd56aeff0b364.png)

### ListTile
Card나 ListView 안에서 자주 사용되지만 어느 곳에나 사용할 수 있습니다.

![list-tile.png](https://flutter.dev/ui/layout/card-3db18421d33c96f34f8d19889a5f28c61287063a62c9770f311fd56aeff0b364.png)