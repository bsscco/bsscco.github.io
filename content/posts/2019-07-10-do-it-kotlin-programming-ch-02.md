---
title: Do it! 코틀린 프로그래밍 02 변수와 자료형, 연산자 요약
date: 2019-07-10 08:26:28
tags: [kotlin]
---

# 02 변수와 자료형, 연산자

### 01 코틀린 패키지

---

**import~ as 연산자: 클래스에 이름 짓기**

**파일 안에 여러 클래스가 있으면 파일명에 .kt가 붙어서 보여진다. 1:1 매치되는 경우에는 .kt가 안 보인다.**

**여러 클래스가 같은 이름을 가질 때 conflict를 예방하기 위해 파일 안에 패키지를 명시하는 게 좋다.**

### 02 변수와 자료형

---

**코틀린 자료형은 모두 참조형(클래스)이다.**

Int, Float 같은 기본 자료형은 코틀린 컴파일러에서 원시 자료형으로 변환된다.

**코드에서 부동소수점 표기법**

    // val exp0 = 가수E지수
    val exp1 = 3.14E-2 // 0.0314
    val exp2 = 3.14E2 // 314

**프로그래밍에서 왜 2의 보수를 사용할까?**

컴퓨터 연산기에는 덧셈기능 밖에 없는데 덧셈으로 뺄셈기능도 구현하기 위해 2의 보수를 사용함.

**String pool을 활용!**

힙영역 String pool에 문자열 리터럴을 보관해두고 재활용한다. 당연히 런타임은 해당되지 않음.

**String interpolation을 지원!**

    print("$a ${b.hashCode()}")

**typealias**

    typealias Username = String
    val user: Username = "Bsscco"

### 03 자료형 검사하고 변환하기

---

**safe call과 elvis 연산자**

    print("Name length: {name?.length ?: -1}")

**코틀린에서는 묵시적 형변환이 안 된다!**

    val decimal = 1.0
    val int = 1
    val sum1Error = decimal + int // TypeMismatch 에러!
    val sum2Ok = decimal.toInt() + int // 문제 없음.
    val sum3Ok = 1.0 + 1 // 표현식에서는 묵시적으로 범위가 큰 자료형으로 형변환 돼서 계산됨.

**자료 비교하기**

동일 연산자 == : 두 변수의 값이 같은지

동등 연산자 === : 두 변수의 참조가 같은지

    val prim1:Int = 128 // 컴파일러에서 원시타입으로 변환됨.
    val prim2 = a // prim2도 원시타입이 된다.
    val ref1:Int? = a // prim1의 값을 가지는 Integer형으로 변환됨.
    val ref2:Int? = 128 // 컴파일러에서 Integer형으로 변환됨.
    println(prim1 == prim2) // 동등비교 true
    println(prim1 === prim2) // 동일비교 true
    println(prim1 == ref1) // 동등비교 true
    println(prim1 === ref1) // 동등비교 false
    println(ref1 == ref2) // 동등비교 true
    println(ref1 === ref2) // 동일비교 false

**주의**: -128~127 사이의 값을 대입하는 경우엔 캐시를 활용하므로 참조형(val ref: Int?)으로 선언했어도 원시형이라 생각하면 된다.

**스마트 캐스트**

    fun printName(o: Any) {
    	if(o is Person) {
    		println(o.name)
    	}
    }

**숫자형 Number, 모든 자료형의 부모 Any**

**safe 타입캐스트 연산자**

as?

### 04 코틀린 연산자

---

**비트 연산 디버깅하기**

Debug>Variables창에서 View as > Binary 선택