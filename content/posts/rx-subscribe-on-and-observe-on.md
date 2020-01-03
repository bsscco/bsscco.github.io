---
title: RxJava SubscribeOn과 ObserveOn의 동작 차이
date: 2019-04-07 09:51:41
tags: [rx, java]
---

참고 : https://github.com/ReactiveX/RxAndroid/blob/2.x/README.md

## SubscribeOn
Observable의 create()부터 onNext(), onComplete(), onError()까지 모든 과정을 subscribeOn()으로 지정한 스레드에서 실행시킨다.

## ObserveOn
Observable의 메소드체인 호출 사이에서 observeOn()이 호출된 지점부터 onNext(), onComplete(), onError()까지의 과정을 observeOn()으로 지정한 스레드에서 실행시킨다.

```java
public class MainActivity extends Activity {
    private static final String TAG = "RxAndroidSamples";

    private final CompositeDisposable disposables = new CompositeDisposable();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main_activity);
        findViewById(R.id.button_run_scheduler).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                onRunSchedulerExampleButtonClicked();
            }
        });
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        disposables.clear();
    }

    void onRunSchedulerExampleButtonClicked() {
        disposables.add(sampleObservable()
                .observeOn(AndroidSchedulers.mainThread())
                .subscribeOn(Schedulers.io())
                // subscribeOn()에 의해 Observable의 create()와 subscribeWith()에 구현된 onNext(), onComplete(), onError() 모두 io스레드에서 실행돼야 한다.
                // 하지만 observeOn()에 의해 subscribeWith()에 구현된 onNext(), onComplete(), onError()는 main스레드에서 실행된다.  
                .subscribeWith(new DisposableObserver<String>() {
                    @Override
                    public void onComplete() {
                        Log.d(TAG, "onComplete() " + Thread.currentThread().getName());
                    }

                    @Override
                    public void onError(Throwable e) {
                        Log.e(TAG, "onError() " + Thread.currentThread().getName());
                    }

                    @Override
                    public void onNext(String string) {
                        Log.d(TAG, "onNext(" + string + ") " + Thread.currentThread().getName());
                    }
                }));
    }

    static Observable<String> sampleObservable() {
        return Observable.defer(new Callable<ObservableSource<? extends String>>() {
            @Override
            public ObservableSource<? extends String> call() throws Exception {
                Log.d(TAG, "create()  " + Thread.currentThread().getName());
                return Observable.just("one");
            }
        });
    }
}
```

