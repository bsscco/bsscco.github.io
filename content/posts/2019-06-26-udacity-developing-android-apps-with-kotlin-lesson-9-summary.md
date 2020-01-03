---
title: Udacity Developing Android Apps with Kotlin 레슨9 Behind the scenes 요약
date: 2019-06-26 00:00:00
tags: [android, kotlin]
---

# Lesson 9: Behind the scenes

## 1. Offline Movie Night

## 2. Exercise: Introduction

offline caching에 대해 배울 거야!

## 3. What’s in a Cache

캐시란, 다음에 곧 쓸 데이터를 로컬에 저장해뒀다가 필요할 때 다시 쓰는 것

![](/2019-06-26/Untitled-65cb43d4-e27c-487d-8850-7ccc9c5d07af.png)

### 우리앱에서 레포지토리 패턴을 써야 하는 이유

1. 앱 실행 시에 다운로드받아 캐시해 사용할 만한 데이터들이 있다. 필터 목록 같은 데이터. 이 데이터를 따로 관리하는 클래스를 두는 것보다, 레포지토리 패턴을 써서 레포지토리 안에서 필요한 데이터만 캐시 시키면 레포지토리를 쓰는 곳에서는 캐시되는 데이터인지 실시간 데이터인지 알 필요 없어서 관심사의 분리가 된다.
2. 네트워크 서비스를 대신해서 테스트할 수 있는 mock을 교체하기 쉽다.

![](/2019-06-26/Untitled-140815fb-a316-44ff-858c-10fe2637d17d.png)

캐시 인벨리데이션에 대해 생각해야 한다.

![](/2019-06-26/Untitled-d536c9ae-e217-4091-a2e1-28d5db8558c7.png)

구조화된 데이터를 캐시하기에 적절한 Room DB

![](/2019-06-26/Untitled-93eb24f3-a377-4691-a37c-0fdf35865683.png)

작은 데이터를 캐시하기에 적절한 SharedPreferences

![](/2019-06-26/Untitled-db06a2fa-29fe-4b79-b5e9-5dfd7cc13157.png)

이미지나 데이터파일을 캐시하기에 적절한 파일

## 4. How to store data

![](/2019-06-26/Untitled-9ffa5e69-f555-4a04-a038-0a564f06794e.png)

네트워크 → Application Code → Database → Notify Application Code → UI

## 5. Decorating a Room

![](/2019-06-26/Untitled-52416e63-4cb0-40a9-ad08-1a4021f27c57.png)

VideoDao

![](/2019-06-26/Untitled-ab0b8c34-843c-4bda-97ea-284c0a596092.png)

Room 메소드 종류

## 6. Building a Room

![](/2019-06-26/Untitled-81ed769b-2ed2-4c47-af2c-ecaf9f963cae.png)

![](/2019-06-26/Untitled-9bfe0e20-a11f-4c74-8496-e5088614bb7f.png)

![](/2019-06-26/Untitled-6123917f-f798-41e9-9a48-5ba9f902baaf.png)

domain object(database) / DTO(network)

## 7. Exercise: Add a DatabaseVideo Entity

    fun List<DatabaseVideo>.asDomainModel(): List<Video> {
    // DO to DTO
        return map {
            Video (
               url = it.url,
               title = it.title,
               description = it.description,
               updated = it.updated,
               thumbnail = it.thumbnail)
        }
    }

    fun NetworkVideoContainer.asDatabaseModel(): Array<DatabaseVideo> {
    // DTO to DO
        return videos.map {
            DatabaseVideo (
               title = it.title,
               description = it.description,
               url = it.url,
               updated = it.updated,
               thumbnail = it.thumbnail)
        }.toTypedArray()
    }

## 8. Exercise: Add the VideoDao

## 9. Exercise: Refactor the VideoDao

    @Dao
    interface VideoDao {
        @Query("select * from databasevideo")
        fun getVideos(): LiveData<List<DatabaseVideo>>
    
        @Insert(onConflict = OnConflictStrategy.REPLACE)
        fun insertAll(vararg videos: DatabaseVideo)
    }

## 10. Exercise: Add the VideosDatabase

## 11. Room Review

## 12. Googler Interview: Sumir Kataria

work manager

## 13. Using a Room

repository pattern: separation of concerns

## 14. Exercise: Build a Repository

    class VideosRepository(private val database: VideosDatabase) {
    
       val videos: LiveData<List<Video>> = Transformations.map(database.videoDao.getVideos()) {
          it.asDomainModel()
       }
    
       suspend fun refreshVideos() {
           withContext(Dispatchers.IO) {
               val playlist = Network.devbytes.getPlaylist().await()
               database.videoDao.insertAll(*playlist.asDatabaseModel())
    					// *playlist : Array를 vararg로 사용할 수 있게 하는 연산자
           }
       }
    }

## 15. Exercise: Use the Repository

    class DevByteViewModel(application: Application) : AndroidViewModel(application) {
    
        private val viewModelJob = SupervisorJob()
        private val viewModelScope = CoroutineScope(viewModelJob + Dispatchers.Main)
    
        private val database = getDatabase(application)
        private val videosRepository = VideosRepository(database)
    
        init {
            viewModelScope.launch {
                videosRepository.refreshVideos()
            }
        }
    
        val playlist = videosRepository.videos
    
        override fun onCleared() {
            super.onCleared()
            viewModelJob.cancel()
        }
    }

## 16. Video: Offline Caching Review

## 17. WorkManager for the background

![](/2019-06-26/Untitled-82fafcc4-1f5e-4dc6-b662-297154e9d89a.png)

백그라운드 작업의 종류

![](/2019-06-26/Untitled-6b2e629d-2be4-4f5a-adf3-cc2ab512f80a.png)

WorkManager의 특징

![](/2019-06-26/Untitled-66277db8-9913-4b65-a794-f0d900d5a9f6.png)

WorkManager의 실행환경 제약들

![](/2019-06-26/Untitled-5a7d6a11-dd89-4ad2-badf-ce57ae11276f.png)

Pre-fetching 가이드

## 18. Exercise: Create a Worker

## 19. Exercise: Schedule Background Work

Appliaction.onCreate 에서 너무 많은 작업을 하면 앱 실행 시 렉이 길어진다! 그래서 이 프로젝트에선 아래와 같이 코딩했음.

    private fun delayedInit() {
        applicationScope.launch {
            setupRecurringWork()
        }
    }
    
    private fun setupRecurringWork() {
        val constraints = Constraints.Builder()
                .setRequiredNetworkType(NetworkType.UNMETERED)
                .setRequiresBatteryNotLow(true)
                .setRequiresCharging(true)
    	            .apply {
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                        setRequiresDeviceIdle(true)
                    }
                }.build()
    
        val repeatingRequest
                = PeriodicWorkRequestBuilder<RefreshDataWorker>(1, TimeUnit.DAYS)
                .setConstraints(constraints)
                .build()
    
        WorkManager.getInstance().enqueueUniquePeriodicWork(
                RefreshDataWorker.WORK_NAME,
                ExistingPeriodicWorkPolicy.KEEP,
                repeatingRequest)
    }

## 20. Wrapping up Caching

## 마치면서

Repository.getData → save in database → auto-notified to Repository.LiveData → ViewModel has Repository.LiveData → Fragment has observed ViewModel.LiveData

매우 깔끔한 캐싱 전략! 심지어

WorkManager를 사용한 프리패칭까지!