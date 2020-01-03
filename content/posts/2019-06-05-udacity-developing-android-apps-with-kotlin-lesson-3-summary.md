---
title: Udacity Developing Android Apps with Kotlin 레슨3 App Navigation 요약
date: 2019-06-05 00:00:00
tags: [android, kotlin]
---

# Lesson 3: App Navigation

### Fragment

다양한 크기의 기기들에 대응할 수 있는 컴포넌트

Activity Back stack vs Fragment Back stack

Activity Back stack

백버튼을 누르면 현재 액티비티는 닫힌다.

Fragment Back stack 

Activity 안에서 네비게이션 되고, Fragment Manager로 추가/삭제 할 수 있다.

Navigation graph는 activity 안에서 multi fragment를 다루는 기술이다.

TitleFragment.kt

    val binding = DataBindingUtil.inflate<FragmentTitleBinding>(inflater, R.layout.fragment_title, container, false)
       return binding.root

## Navigation

### Navigation 원칙

1. Starting Place가 있어야 한다.
2. 언제나 Go Back 할 수 있어야 한다.
3. Up 버튼으로 뒤로 갈 수 있어야 한다.

    시스템 백버튼은 홈화면까지 되돌아갈 수 있지만 Up버튼은 Starting Place에서 없어진다.

여러 액티비티를 쓰면서 각 액티비티마다 Navigation을 따로 두는 게 일반적인 패턴인 듯. 이렇게 쓰면 네비게이션 그래프를 적당한 크기로 나눌 수 있음.

### Navigation 준비물

1.Navigation graph 리소스를 추가하기

2. NavHostFragment를 추가하기

    app:defaultNavHost="true" # system 백버튼을 가로챌 수 있게 해준다.
    app:navGraph="@navigation/navigation"

3. Fragment들을 Navigation graph에 추가하기

4. Navigation graph에서 Fragment들을 Action으로 연결하기

![](/2019-06-05/Untitled-65ebda4a-f0a3-43e3-8863-91e7b555565d.png)

5. onClickListener를 만들고 Navigation Controller를 찾아서 Action으로 네비게이팅 시키기

    // ktx 사용
    binding.playButton.setOnClickListener(Navigation.createNavigateOnClickListener(R.id.action_titleFragment_to_gameFragment))
    
    binding.playButton.setOnClickListener {
        Navigation.findNavController(it).navigate(R.id.action_titleFragment_to_gameFragment)
    }

### 조건부 Navigation

조건부 네비게이션은 코드에서 제어한다.

    if (answers[answerIndex] == currentQuestion.answers[0]) {
        questionIndex++
        // Advance to the next question
        if (questionIndex < numQuestions) {
            currentQuestion = questions[questionIndex]
            setQuestion()
            binding.invalidateAll()
        } else {
            view.findNavController().navigate(R.id.action_gameFragment_to_gameWonFragment)
        }
    } else {
        view.findNavController().navigate(R.id.action_gameFragment_to_gameOverFragment)
    }

### pop behavior

action에서 설정할 수 있다.

![](/2019-06-05/Untitled-e64c137a-aae3-4b9a-83b3-4f5c8bdf07f2.png)

inclusive를 체크하면 pop to에 지정된 프래그먼트까지 이동 후에 그 프래그먼트에서 pop 동작을 수행한다.

### support navigate up

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        @Suppress("UNUSED_VARIABLE")
        val binding = DataBindingUtil.setContentView<ActivityMainBinding>(this, R.layout.activity_main)
    
        val navigationController = this.findNavController(R.id.myNavHostFragment)
        NavigationUI.setupActionBarWithNavController(this, navigationController)
    }
    
    override fun onSupportNavigateUp(): Boolean {
        val navigationController = this.findNavController(R.id.myNavHostFragment)
        return navigationController.navigateUp()
    }
    
    // override 메소드 목록을 보여주는 단축키 : Mac Ctrl+O

![](/2019-06-05/Untitled-7aad67e6-48f3-4c09-ab0a-2579cf104d6c.png)

setupActionBarWithNavController()를 호출한 뒤로는 ActionBar에 up 버튼이 생김

### adding a menu

![](/2019-06-05/Untitled-be671b18-cdce-4e67-aa84-3a9164c8e529.png)

id를 aboutFragment로 설정

![](/2019-06-05/Untitled-6ed9d5bc-01c9-488d-879e-beb030502220.png)

menu item의 id도 aboutFragment로 설정

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                                  savedInstanceState: Bundle?): View? {
    	...
    	setHasOptionsMenu(true)
    	...
    }
    
    override fun onCreateOptionsMenu(menu: Menu?, inflater: MenuInflater?) {
        super.onCreateOptionsMenu(menu, inflater)
        inflater?.inflate(R.menu.overflow_menu, menu)
    }
    
    override fun onOptionsItemSelected(item: MenuItem?): Boolean {
        return NavigationUI.onNavDestinationSelected(item!!, view!!.findNavController()) || super.onOptionsItemSelected(item)
    }
    

![](/2019-06-05/Untitled-a097eac0-3922-432b-8b20-e89532a6ddfd.png)

menu에 네비게이션 프래그먼트를 연결할 땐 action을 사용하지 않아도 된다.

### safeArgs

    // in project gradle
    classpath "android.arch.navigation:navigation-safe-args-gradle-plugin:$version_navigation"
    
    // in app gradle
    apply plugin: 'androidx.navigation.fadeargs'
    
    // in GameFragment.kt
    view.findNavController().navigate(GameFragmentDirections.actionGameFragmentToGameWonFragment(numQuestions, questionIndex))

한 번 safeargs를 사용하도록 설정해두면 앞으로는 NavDirections을 어디서나 사용할 수 있다.

![](/2019-06-05/Untitled-18c460e3-91f7-4fe9-bf9b-5808fe5971f0.png)

arguments 설정

    // in GameWonFragment.kt
    val args = GameWonFragmentArgs.fromBundle(arguments!!)
    Toast.makeText(context, "NumCorrect: ${args.numCorrect}, NumQuestions: ${args.numQuestions}", Toast.LENGTH_LONG).show()

### implicit intent

    override fun onCreateOptionsMenu(menu: Menu?, inflater: MenuInflater?) {
            super.onCreateOptionsMenu(menu, inflater)
            inflater?.inflate(R.menu.winner_menu, menu)
            if(null == getShareIntent().resolveActivity(activity!!.packageManager)) {
                menu?.findItem(R.id.share)?.setVisible(false)
            }
        }
    
    override fun onOptionsItemSelected(item: MenuItem?): Boolean {
        when (item!!.itemId) {
            R.id.share -> startActivity(getShareIntent())
        }
        return super.onOptionsItemSelected(item)
    }
    
    // Creating our Share Intent
    private fun getShareIntent(): Intent {
        val args = GameWonFragmentArgs.fromBundle(arguments!!)
        return ShareCompat.IntentBuilder.from(activity)
                .setText(getString(R.string.share_success_text, args.numCorrect, args.numQuestions))
                .setType("text/plain")
                .intent
    }

## Navigation drawer

1. navdrawer_menu 리소스 생성
2. Activity layout xml에 DrawerLayout, NavigationView를 추가

    ![](/2019-06-05/Untitled-8edb3576-ffab-4b96-9d35-fd575d796cae.png)

3. MainActivity에서

        // NavigationUI.setupActionBarWithNavController(this, navController)
        NavigationUI.setupActionBarWithNavController(this, navController, drawerLayout)
        NavigationUI.setupWithNavController(binding.navView, navController)
        
        ...
        
        // return navController.navigateUp()
        return NavigationUI.navigateUp(navController, binding.drawerLayout)

4. 

        // prevent nav gesture if not on start destination
        navController.addOnDestinationChangedListener { nc: NavController, nd: NavDestination, args: Bundle? ->
            if (nd.id == nc.graph.startDestination) {
                binding.drawerLayout.setDrawerLockMode(DrawerLayout.LOCK_MODE_UNLOCKED)
            } else {
                binding.drawerLayout.setDrawerLockMode(DrawerLayout.LOCK_MODE_LOCKED_CLOSED)
            }
        }

### Navigation 시스템을 사용했을 때 장점

Fragment 매니저를 사용하지 않아도 되고 프래그먼트 네비게이션을 GUI로 만들 수 있어서 간결하고 편하다. 

pop 동작을 GUI에서 자유롭게 설정할 수 있는 게 직관적이고 쉽고 편하다!

ktx를 사용하면 코드가 더 간결해지는 것!

네이게이션 흐름을 한 눈에 볼 수 있는 것

safeArgs로 프래그먼트 간 데이터 통신에서 발생하는 에러를 정확하게 잡아줌

enter, exit, popEnter, popExit 애니메이션을 nav graph에서 설정할 수 있는 편리함 

## 궁금한 것

AppBar를 다양하게 커스텀 시키는 우리 앱에서 setupActionBarWithNavController 같은 기능을 쓸 수 있을까? 방법이 없을까?

**ToolBar를 사용하면 할 수 있을 듯!**

**setCustomActionBar 사용해도 됨!**

## 논의하고 싶은 것

강의를 보면 layout xml에서 view id의 이름을 camelCase로 짓는다.

binding을 쓰는 코드에서 접근 변수들이 view id의 이름 그대로 사용하는데, 코드에선 camelCase로 네이밍 하는 게 표준이니까 view id의 이름을 camelCase로 지은 듯

우리도 앞으로 짓는 view id의 이름을 camelCase로 지으면 좋겠다.

**그렇게 하기로 함!**