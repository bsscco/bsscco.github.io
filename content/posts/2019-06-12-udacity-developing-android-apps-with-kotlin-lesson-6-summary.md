---
title: Udacity Developing Android Apps with Kotlin 레슨6 App Architecture (Persistence) 요약
date: 2019-06-12 00:00:01
tags: [android, kotlin]
---

# Lesson 6: App Architecture (Persistence)

## 1. Wake Up, Aleks!

## 2. Introduction

- Room Database
- DAO
- Application Architecture with Room
- Coroutines

# Room

---

## 3. SQLite Primer

SQLite 문법에 대한 간단 설명

## 4. Designing Entities

![](/2019-06-12/Untitled-28e707d7-0703-4e42-b08c-c832f51b8a08.png)

Room DB layer 구조

## 5. Exercise: Creating the SleepNight Entity

![](/2019-06-12/Untitled-378777d8-8daf-488b-95e7-7b5cc17a0182.png)

data class를 사용해 Entity 클래스 만들기

## 6. Data Access Object (DAO)

![](/2019-06-12/Untitled-b4c160a9-fb7b-4bf3-b70f-6d0355a727fb.png)

DAO 어노테이션의 종류

## 7. Exercise: DAO - SleepDatabaseDao

![](/2019-06-12/Untitled-915fbc8d-7ed7-4dd5-bf30-138b10dcea78.png)

DAO 클래스 만들기

## 8. Creating a Room Database

![](/2019-06-12/Untitled-98fd58a9-18f3-41fa-a743-0e54463f7a0c.png)

Room DB를 만드는 순서

## 9. Exercise: Creating a Room Database

    @Database(entities = [SleepNight::class], version = 1, exportSchema = false)
    abstract class SleepDatabase : RoomDatabase() {
    
    abstract val sleepDatabaseDao: SleepDatabaseDao
    
    companion object {
    
        @Volatile
        private var INSTANCE: SleepDatabase? = null
    
        fun getInstance(context: Context): SleepDatabase {
            synchronized(this) {
                var instance = INSTANCE 
    
                if (instance == null) {
                    instance = Room.databaseBuilder(
                            context.applicationContext,
                            SleepDatabase::class.java,
                            "sleep_history_database"
                    )
                            .fallbackToDestructiveMigration()
                            .build()
                    INSTANCE = instance
                }
                return instance
            }
         }
      }
    }

- exportScheme는 기본이 true이고, true일 때 스키마파일을 폴더에 저장시킨다고 한다.
- @Volatile의 의미 : [https://woovictory.github.io/2019/01/04/Android-What-is-Singleton-Pattern](https://woovictory.github.io/2019/01/04/Android-What-is-Singleton-Pattern/)
    - synchronized 블록으로 감쌌더라도 INSTANCE의 null값이 스레드 캐시에 저장돼있는 상태라면, 다른 스레드에서 먼저 INSTANCE에 DB인스턴스를 대입했더라도 스레드 캐시에 있는 null값을 사용할 확률이 있다. INSTANCE를 volatile로 선언해서 항상 메인 메모리에서 읽어오도록 하면 스레드 캐시에 저장했을 때 발생하는 문제를 예방할 수 있다.
- .fallbackToDestructiveMigration() 옵션은 DB 버전이 올라갔을 때 DB 데이터를 제거하고 DB를 새로 만든다.

## 10. Testing the Room Database

    @RunWith(AndroidJUnit4::class)
    class SleepDatabaseTest {ㅇ
    
        private lateinit var sleepDao: SleepDatabaseDao
        private lateinit var db: SleepDatabase
    
        @Before
        fun createDb() {
            val context = InstrumentationRegistry.getInstrumentation().targetContext
            // Using an in-memory database because the information stored here disappears when the
            // process is killed.
            db = Room.inMemoryDatabaseBuilder(context, SleepDatabase::class.java)
                    // Allowing main thread queries, just for testing.
                    .allowMainThreadQueries()
                    .build()
            sleepDao = db.sleepDatabaseDao
        }
    
        @After
        @Throws(IOException::class)
        fun closeDb() {
            db.close()
        }
    
        @Test
        @Throws(Exception::class)
        fun insertAndGetNight() {
            val night = SleepNight()
            sleepDao.insert(night)
            val tonight = sleepDao.getTonight()
            assertEquals(tonight?.sleepQuality, -1)
        }
    }

Room 유닛테스트는 쿼리 테스트용으로 좋을 것 같다.

## 11. Displaying Sleep Data

메인 액티비티 레이아웃과 두 프래그먼트 레이아웃을 살펴봄.

<merge> 태그 : <include>는 루트 레이아웃이 꼭 있어야 하는데, <mege>는 루트 레이아웃이 필요 없다. 다른 레이아웃에 삽입될 때 <merge> 태그는 사라지고 자식태그들만 붙여진다.

## 12. Adding A ViewModel

비지니스 로직은 ViewModel로 구현하고, DB 명령은 coroutines를 사용해 백그라운드 스레드로 실행할 것이라고 함.

## 13. Exercise: Adding a ViewModel

ViewModel과 ViewModelFactory를 구현하고 ViewModel을 레이아웃 파일에 연결함.

# Coroutines

---

## 14. Multithreading and Coroutines

![](/2019-06-12/Untitled-1f06d69e-6537-410e-a454-a1d3ecf8398a.png)

코루틴의 특징

![](/2019-06-12/Untitled-929eff06-cd7a-4c00-b491-e90d3936b29a.png)

코루틴을 쓰려면 필요한 것들

![](/2019-06-12/Untitled-eb3308c9-b2dc-4f12-a9b2-473e5861cbc9.png)

[https://twitter.com/akarnokd/status/979732723152687106](https://twitter.com/akarnokd/status/979732723152687106)

## 15. Exercise: Coroutines for Long-running Operations

    private var viewModelJob = Job() // 이 ViewModel의 모든 코루틴을 취소시킬 수 있는 객체

### Scope 개념 잘 모르겠음!

Room Dao로 반환한 LiveData는 Room DB 데이터가 바뀌면 자동으로 갱신되는 장점이 있다!

## 16. Googler Interview: Florina Muntenescu

## 17. Navigation and Sleep Quality

![](/2019-06-12/Untitled-8442a097-6d39-4cac-a072-3cdcf597f278.png)

## 18. Exercise: Recording Sleep Quality

    sleepTrackerViewModel.navigateToSleepQuality.observe(this, Observer { night ->
        night?.let {
            this.findNavController().navigate(
                    SleepTrackerFragmentDirections
                            .actionSleepTrackerFragmentToSleepQualityFragment(night.nightId))
            // Reset state to make sure we only navigate once, even if the device
            // has a configuration change.
            sleepTrackerViewModel.doneNavigating()
        }
    })

state가 아닌 event는 이런 식으로 View(Fragment)단에서 LiveData를 observe 해서 처리하는 패턴이 보임.

SleepQualityFragment에서 DB 데이터를 바꾸고 SleepTrackerFragment로 돌아왔을 때 SleepTrackerFragment에서 사용하는 LiveData도 자동으로 갱신된 것을 볼 수 있다. 심지어 라이프사이클을 잘 알기 때문에 자동으로 적절한 시점에 갱신 된다. 화면 간 상태 동기화 문제를 Bus 기법이나 다른 보일러플레이트 코드 없이도 간단하게 해결할 수 있는 장점이 있다. 단, 상태 동기화가 필요한 데이터의 DB화하는 또 다른 수고가 생긴다. 

## 19. Transformation Maps

## 20. Exercise: Button States and SnackBar

    val stopButtonVisible = Transformations.map(tonight) {
    	null != it
    }
    
    val clearButtonVisible = Transformations.map(nights) {
    	it?.isNotEmpty()
    }

LiveData 가공은 Transformations.map()! 다시 한 번 각인됨.

## 21. Where to go next?

![](/2019-06-12/Untitled-e01f203d-4bbc-4450-9043-96372b076921.png)

ViewModelFactory를 쓰는 이유 하나 더 생각 남 : DI를 쓰면 코드 수정을 최소화해서 테스트하기 편하기 때문 

## 마치면서

다른 화면 사이에서 Room~LiveData 자동 갱신 연계가 매우 인상적이었다. 정말 간결하고 쓰기 편하다고 생각한다.