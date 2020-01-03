---
title: Udacity Developing Android Apps with Kotlin 레슨5 App Architecture (UI Layer) 요약
date: 2019-06-12 00:00:00
tags: [android, kotlin]
---

# Lesson 5: App Architecture (UI Layer) 

## 1. Architecting on the fly

## 2. Lesson Introduction

- Application Architecture
- ViewModel
- LiveData
- Data Binding

## 3. Exercise: Tour of the App

ktx를 쓰고 싶은 곳에서만 ktx가 구현된 파일을 import 할 수 있구나

## 4. Where the App Falls Short

## 5. What is Architecture

![](/2019-06-12/Untitled-6587f21b-70db-4d8d-9ef0-8a9674593f1e.png)

안드로이드 MVVM 패턴

- M : Repository + Data provider
- V : UI Controller
- VM : ViewModel(ViewModel 클래스의 기능은 Presenter의 기능과 다르지 않다)

UI Controller는 데이터를 표현하고, 사용자입력을 잡아내는 역할을 한다.

ViewModel은 UI 데이터를 담는다.

## 6. Our App Architecture

## 7. ViewModel

![](/2019-06-12/Untitled-0053bc83-7649-4d3f-8f64-672478d3efd0.png)

액티비티가 진짜 파괴될 때까지 유지된다.

### 그럼 메모리 관리를 위해 시스템이 프로세스를 죽일 때는 어떤 상황이 벌어지나?

다른 앱을 사용하고 있을 때 시스템에 의해 우리앱의 프로세스가 죽을 때가 있다. Recents 화면에서 우리앱을 눌누르면 이전에 보던 화면(Activity)와 함께 새로운 Backstack이 이전의 Backstack이랑 똑같게 만들어지는데, 프로세스가 죽으면서 메모리가 다 해제됐었기 때문에 동시에 ViewModel도 새로 만들어진다.

![](/2019-06-12/Untitled-5d235cc3-735c-4855-a869-d5591981ae15.png)

기기가 회전되면 Fragment가 다시 만들어지고, Fragment 안에 있던 데이터는 사라짐.

![](/2019-06-12/Untitled-096630b9-b2ce-40aa-b25d-4da9ab25fb21.png)

기기가 회전돼도 ViewModel은 다시 만들어지지 않기 때문에 ViewModel 안에 있던 데이터는 보존됨.

## 8. Exercise: Create the GameViewModel

## 9. What Belongs in the GameViewModel?

![](/2019-06-12/Untitled-ac87505f-78d8-4a90-a8d6-352add227741.png)

1. 액티비티로부터 UI 데이터와 UI 데이터를 조작하는 메소드를 ViewModel로 옮긴다.
2. 액티비티엔 Binding 객체와 UI 데이터를 표현하는 메소드만 남긴다.

## 10. Exercise: Populate the GameViewModel

## 11. The Benefits of a Good Architecture

![](/2019-06-12/Untitled-efe9d611-29f9-42a1-b3c4-5cada907e687.png)

## 12. The power and Limits of ViewModel

메모리 관리를 위해 프로세스가 종료됐을 때를 대비하는 내용은 Lesson 6에서 자세히...

## 13. LiveData

![](/2019-06-12/Untitled-ca4f6702-a572-4152-8b14-1e34c150a122.png)

UI Controller에서만 ViewModel을 조작할 수 있는 Unidirectional은 ViewModel에서 UI 데이터가 변경됐을 때 UI Controller가 UI 데이터를 바로 갱신할 수 없게 만든다.

![](/2019-06-12/Untitled-98c0b7d0-b157-4a4a-b761-bf538417fe66.png)

![](/2019-06-12/Untitled-0ffceaee-53d7-4e18-aabd-788cbeb007fe.png)

UI 데이터에 LiveData로 wrapping 시키면, ViewModel에서 UI Data가 변경됐을 때 UI Controller가 바로 알아채고 UI 표현을 갱신할 수 있다.

## 14. Exercise: Add LiveData to GameViewModel

    fun onSkip() {
        score.value = (score.value)?.minus(1) // value에 대입 시켜줘야 observe 이벤트가 발생함.
        nextWord()
    }
    
    ...
    
    viewModel.score.observe(this, Observer { newScore ->
        binding.scoreText.text = newScore.toString()
    })

UI 갱신 코드가 여러 군데 있다가 한 곳으로 결집돼서 보기 좋아지고 에러 처리가 쉬워짐.

## 15. Lifecycle Awareness

![](/2019-06-12/Untitled-fe22b1bd-0951-4169-ae29-6c4f1c68ca12.png)

- off-screen 상태에서 LiveData의 값이 바뀌어도 observe 이벤트가 발생하지 않고, on-screen으로 돌아왔을 때 자동으로 현재 값을 담고 있는 observe 이벤트가 발생되기 때문에 상태 관리에 최적이다.
- UI Controller가 파괴되면 observe 커넥션은 자동으로 끊겨서 라이프사이클 문제에 대해 신경쓰지 않아도 된다.

## 16. Exercise: Add LiveData Encapsulation to GameViewModel

    private var _score = MutableLiveData<Int>()
    val score: LiveData<Int> // backing property를 써서 UI Controller에서는 데이터를 조작할 수 없게 만든다.
        get() = _score 

## 17. Event vs. State

이벤트는 한 번 발생하면 더 보존될 필요가 없는 것

ViewModel에서 이벤트 멤버변수는 event라는 prefix를 붙여서 이름짓는 듯

## 18. Exercise: Add End Game Event

    viewModel.eventGameFinish.observe(this, Observer { isFinished ->
        if (isFinished) {
    				Toast.makeToast(this.activity, "Game has finished", Toast.LENGTH_SHORT).show()
            viewModel.onGameFinishComplete() // off-screen -> on-screen에 의해 토스트가 다시 호출되는 경우를 막기 위한 조치
        }
    })

## 19. Google Interview: Yigit Boyar

## 20. Adding a Timer

## 21. Exercise: Create the GameViewModel

    DateUtils.formatElapsedTime(newTime) // 요 Util 좋네

## 22. Exercise: Add a ViewModelFactory

## 23. Exercise: Add ViewModel to Data Binding

![](/2019-06-12/Untitled-1a7a3637-e738-4f3f-b701-2d6fd73715dd.png)

before Data binding

![](/2019-06-12/Untitled-18593374-65d9-4dc4-ab33-6050682bb5c2.png)

after Data binding

## 24. Exercise: Add LiveData Data Binding

    binding.setLifecycleOwner(this) // LiveData를 xml layout에서 사용하기 위해

    android:text="@{@string/quote_format(gameViewModel.word)}"

xml layout에서 데이터를 가공하려면 strings 리소스를 사용해야 하는데 많이 불편한 듯. 더 나은 방법이 없을까? 바로 다음 cencept. 25에서 해결방법을 소개한다.

## 25. Exercise: LiveData Map Transformation

![](/2019-06-12/Untitled-9cd657b0-0507-4c44-a758-a2f2a61f052d.png)

![](/2019-06-12/Untitled-9b9f1e13-3d4d-4e34-8e58-7e688d76d339.png)

    val currentTimeString = Transformations.map(currentTime) { time ->
        DateUtils.formatElapsedTime(time)
    }

    android:text="@{gameViewModel.currentTimeString}"

## 26. Optional Exercise: Adding the Buzzer

## 27. Recap of Architecture and Lifecycles

## 마치면서

우리앱에선 모바일 위주라서 세로화면이 강제다. 화면전환에 대한 이슈해결을 위해 ViewModel을 사용하는 건 의미가 없다. 만약 태블릿도 대응하려면 ViewModel을 꼭 쓰면서 UI 디자인도 다시 고려해 할 것 같다. 앞에 이유 말고 ViewModel과 LiveData가 액티비티의 라이프사이클을 잘 알고 있기 때문에 사용하면 라이프사이클 문제에 대해 신경쓰지 않아도 되는 장점(필요할 때만 subscription에 이벤트가 발생, 자동 release 등)이 있어서 자주 쓰고 싶다. 또 ViewModel, LiveData, Data Binding을 함께 조합하면 UI Controller가 단순해지는 마법을 쓸 수 있어서 이런 구성으로도 쓰고 싶다.