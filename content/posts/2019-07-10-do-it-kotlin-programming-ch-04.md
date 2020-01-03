---
title: Do it! 코틀린 프로그래밍 04 프로그램의 흐름 제어 
date: 2019-07-10 10:27:28
tags: [kotlin]
---

# 04 프로그램의 흐름 제어 

### 01 조건문

---

**조건문도 표현식**

    val max = if (a > b) a else 0

**조건문에서 in연산자와 범위연산자 사용하기**

    if(num in 1..100) // 범위에는 100도 포함

**when문**

자바의 switch문과 다르게 표현식도 허용!

    when (num) {
        in 1..100 -> print("1..100")
    		200, 300 -> print("200, 300")
    		is Float -> print("Float")
        else -> print("else")
    }

### 02 반복문

---

**범위 반복문**

    for(n in 1..10 step 2) { // 상행
        println(n)
    }
    
    for(n in 10 downTo 1 step 2){ // 하행
        println(n)
    }

### 03 흐름의 중단과 반환

---

**익명함수와 람다식에서 return사용하기**

- 익명 함수에서 return은 함수에서 쓰는 방식대로 쓰면 된다.
- 람다식에서는 return@라벨 형태로 써야 한다. 람다식을 호출하는 함수를 라벨로 사용할 수 있는데 이걸 **암묵적 라벨**이라고 한다.

**break문과 continue문에서 라벨 사용하기**

    first@ for (i in 1..10) {
        for (j in 1..10) {
            if (i == 5 && j == 5) {
                break@first
            }
            if (i == 2 && j == 2) {
                continue@first
            }
        }
    }