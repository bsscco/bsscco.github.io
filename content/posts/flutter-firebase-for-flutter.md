---
title: Flutter 코드랩 리뷰 - 플러터를 위한 파이어베이스
date: 2019-02-16 15:41:07
tags: [flutter, firebase]
---

Flutter를 위한 Firebase

https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html

- 프로젝트 소스 : https://github.com/bsscco/baby_name_votes
- 눈에 띄는 내용
  - named 생성자를 지원한다.
    - ```dart
      class Record {
        final String name;
        final int votes;
        Record.fromMap(Map<String, dynamic> map)
          : assert(map['name'] != null),
            assert(map['votes'] != null),
            name = map['name'],
            votes = map['votes'];
      }
      ```
  - StreamBuilder는 인자를 통해 받은 스트림이 변경되면 다시 빌드하는 기능을 한다.
    - ```dart
      Widget _buildBody(BuildContext context) {
        return StreamBuilder<QuerySnapshot>(
          stream: Firestore.instance.collection('baby').snapshots(),
          builder: (context, snapshot) {
            if (!snapshot.hasData) {
              return LinearProgressIndicator();
            }
            return _buildList(context, snapshot.data.documents);
          },
        );
      }
      ```
  - Firestore의 Transaction기능을 사용하면 Firestore로부터 순서가 보장되는 transaction을 가져와서 document를 갱신할 수 있다. 아마 이 transaction들은 수많은 기기에서 동시에 실행되는 상황을 대비해서 바로 실행되지 않고 Firestore 내부에 있는 큐에 담긴 다음에 순차적으로 실행되지 않을까 추측한다.


