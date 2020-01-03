---
title: Flutter로 개발하기(04) - 위젯의 크기 제약조건
date: 2019-03-13 07:49:13
tags: [flutter]
---

# Dealing with box constraints
https://flutter.dev/docs/development/ui/layout/box-constraints

플러터에서 위젯은 자신의 렌더박스에 그려집니다. 렌더박스의 크기는 부모 위젯으로부터 받은 제약조건에 의해 결정됩니다. 제약조건은 최소, 최대 너비와 높이로 구성됩니다.

제약조건의 유형은 일반적으로 3가지로 분류됩니다.
- 가능한 한 큰 크기로 결정하기. Center, ListView 위젯이 그렇습니다.
- 자식 위젯과 같은 크기로 결정하기. Transform, Opacity 위젯이 그렇습니다.
- 특정한 값으로 크기를 결정하기. Image, Text 위젯이 그렇습니다.

Container 같은 위젯들은 생성자 인자에 따라 다른 제약조건 유형을 가집니다. Container는 기본적으로 가능한 한 큰 크기를 가지려고 하지만, 생성자 인자로 너비가 주어지면 그 값에 의해 크기가 결정됩니다.

## 한정되지 않은 제약조건
ListView 같은 스크롤 가능한 위젯들은 crossAxis 방향으로 크기를 확장하려는 제약조건을 가집니다.

## Flex
Flex box model을 기반으로 하는 Row, Column 같은 위젯들은 주어진 제약조건이 bounded인가, unbounded인가에 따라 다르게 동작합니다. 제약조건이 bounded일 때 자신의 mainAxis 방향으로 가능한 한 크게 확장합니다. unbounded일 때는 자식 위젯들의 크기에 맞춰집니다.