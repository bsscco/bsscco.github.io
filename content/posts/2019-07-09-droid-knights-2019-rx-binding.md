---
title: 드로이드나이츠 2019 RxBinding 발표영상 리뷰
date: 2019-07-09
tags: [rx]
---

# RxBinding 소개

### 참고

[[DroidKnights 2019 - Track 1] 하동현 - 지금까지 이런 간단한 Logic 처리는 없었다](https://www.youtube.com/watch?v=uyUK65AUQOI&list=PLu8dnNjU2FmuGAGjExmVqsEnPuL-8alT2&index=2)

### RxBinding 정의

뷰 이벤트를 Observable로 받아서 사용할 수 있는 라이브러리 by 제이크와튼

### 언제 쓰나요?

1. 뷰 이벤트 스트림을 조합해서 한 번에 처리하고 싶을 때
2. 뷰 이벤트 스트림에 다양한 조건을 깔끔하게 추가하고 싶을 때

### 코드 비교

**RxBinding 사용 전**
[https://gist.github.com/bsscco/65e70856ab1ab725efaee8aec22294bc](https://gist.github.com/bsscco/65e70856ab1ab725efaee8aec22294bc)
```kotlin
class MainActivity : AppCompatActivity() {

    private var isButtonClickProcessing = false

    @SuppressLint("CheckResult")
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val nicknameInputField = findViewById<EditText>(R.id.nicknameInputField)
        val passwordInputField = findViewById<EditText>(R.id.passwordInputField)
        val okButton = findViewById<Button>(R.id.okButton)

        nicknameInputField.addTextChangedListener(object : TextWatcher {
            override fun afterTextChanged(p0: Editable?) {
            }

            override fun beforeTextChanged(p0: CharSequence?, p1: Int, p2: Int, p3: Int) {
            }

            override fun onTextChanged(text: CharSequence?, p1: Int, p2: Int, p3: Int) {
                text?.let { // 입력 내용에 따라 버튼색 변하게 하기
                    if (text.length >= 4 && passwordInputField.length() >= 4) {
                        okButton.setBackgroundColor(Color.BLUE)
                    } else {
                        okButton.setBackgroundColor(Color.GRAY)
                    }
                }
            }
        })

        passwordInputField.addTextChangedListener(object : TextWatcher {
            override fun afterTextChanged(p0: Editable?) {
            }

            override fun beforeTextChanged(p0: CharSequence?, p1: Int, p2: Int, p3: Int) {
            }

            override fun onTextChanged(text: CharSequence?, p1: Int, p2: Int, p3: Int) {
                text?.let { // 입력 내용에 따라 버튼색 변하게 하기
                    if (text.length >= 4 && nicknameInputField.length() >= 4) {
                        okButton.setBackgroundColor(Color.BLUE)
                    } else {
                        okButton.setBackgroundColor(Color.GRAY)
                    }
                }
            }
        })

        okButton.setOnClickListener {
            // 버튼 클릭에 쿨타임 걸기
            if(isButtonClickProcessing) return@setOnClickListener
            isButtonClickProcessing = true
            Observable.timer(1, TimeUnit.SECONDS).subscribe { isButtonClickProcessing = false }

            when {
                nicknameInputField.length() < 4 -> Toast.makeText(this, "닉네임 4글자 이상!", Toast.LENGTH_SHORT).show()
                passwordInputField.length() < 4 -> Toast.makeText(this, "패스워드 4글자 이상!", Toast.LENGTH_SHORT).show()
                else -> {
                    nicknameInputField.setText("")
                    passwordInputField.setText("")
                    Toast.makeText(this, "OK!", Toast.LENGTH_SHORT).show()
                }
            }
        }
    }
}
```

**RxBinding 사용 후**
[https://gist.github.com/bsscco/4954a5f497624b458a12e1eefaad6dc6](https://gist.github.com/bsscco/4954a5f497624b458a12e1eefaad6dc6)

```kotlin
class MainActivity : AppCompatActivity() {

    private val compositeDisposable = CompositeDisposable()

    @SuppressLint("CheckResult")
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val nicknameInputField = findViewById<EditText>(R.id.nicknameInputField)
        val passwordInputField = findViewById<EditText>(R.id.passwordInputField)
        val okButton = findViewById<Button>(R.id.okButton)
       
        compositeDisposable.add(Observable.merge(nicknameInputField.textChanges(), passwordInputField.textChanges())
            .subscribe { text -> // 입력 내용에 따라 버튼색 변하게 하기
                if (nicknameInputField.length() >= 4 && passwordInputField.length() >= 4) {
                    okButton.setBackgroundColor(Color.BLUE)
                } else {
                    okButton.setBackgroundColor(Color.GRAY)
                }
            })

        compositeDisposable.add(okButton.clicks()
            .throttleFirst(1, TimeUnit.SECONDS) // 버튼 클릭에 쿨타임 걸기
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe {
                when {
                    nicknameInputField.length() < 4 -> Toast.makeText(this, "닉네임 4글자 이상!", Toast.LENGTH_SHORT).show()
                    passwordInputField.length() < 4 -> Toast.makeText(this, "패스워드 4글자 이상!", Toast.LENGTH_SHORT).show()
                    else -> {
                        nicknameInputField.setText("")
                        passwordInputField.setText("")
                        Toast.makeText(this, "OK!", Toast.LENGTH_SHORT).show()
                    }
                }
            }
        )
    }

    override fun onDestroy() {
        compositeDisposable.clear()
        super.onDestroy()
    }
}
```

### 단점

disposable 처리가 필요하다.

### 마치면서

입력필드가 많고 입력값에 대해 복잡한 로직을 적용해야 할 때 편리할 것 같다.