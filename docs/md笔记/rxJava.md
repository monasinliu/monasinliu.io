# RxJava-2

## 观察者基本实现

​	四个基本概念：Observer(观察者)，Observable(被观察者)，subscribe(订阅)，事件

### 创建 Observer 观察者

​	决定事件发生时有怎么样的行为

​	Observer 接口实现方式

```java
Observer<String> observer = new Observer<String>() {
    @Override
    public void onSubscribe(Disposable d) {
      //事件订阅成功回调，返回Disposable请求实例，可以通过d.dispose()取消请求
        Log.e(TAG, "onSubscribe == 订阅");
    }
    @Override
    public void onNext(String s) {
      //响应事件的方法，发送事件时，观察者会回调onNext()方法，接收事件数据
        Log.d(tag, "Item: " + s);
    }
    @Override
    public void onCompleted() {
      //事件队列完结，当不再有新onNext()发出时，需要触发onComplete()方法来作为标志，其他事件不会再继续发出
        Log.d(tag, "Completed!");
    }
    @Override
    public void onError(Throwable e) {
      //事件队列异常，在处理事件出现异常的时候回触发这个方法，其他事件不会再继续发出
        Log.d(tag, "Error!");
    }
};
```

​	在一个正确的事件运行队列中，onError()和onComplete()有且仅有一个出现，还是在事件的最后出现，即onError()和onComplete()是互斥的，当一个出现了，另一个就不再回出现。（什么样是错误的，怎么会造成错误的事件运行队列）

​	实现了Observer的抽象类：Subscriber（rx1.x）

​	在Rx2.x中， `Observer` 中不支持 `Subscriber` ，Subscriber成为单独的接口并提供方法。与Observer<T> 提供的方法相同。

```java
class TestSubscriber implements Subscriber {
    @Override
    public void onSubscribe(Subscription s) {
    }
    @Override
    public void onNext(Object o) {
    }
    @Override
    public void onError(Throwable t) {
    }
    @Override
    public void onComplete() {
    }
}
```

### 创建 Observable 被观察者

​	通过 `create()` 方法创建，实现了 `subscribe` 方法

```java
//创建被观察者Observable
Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
    @Override
    public void subscribe(ObservableEmitter<String> e) throws Exception {
        e.onNext("RxJava：e.onNext== 第一次");
        e.onNext("RxJava：e.onNext== 第二次");
        e.onNext("RxJava：e.onNext== 第三次");
        e.onComplete();
    }
});
```

### Subscribe 订阅

​	连接被观察者和观察者

```java
//订阅(观察者观察被观察者)
observable.subscribe(observer);
```

​	Observable只生产事件，真正发送事件的是在它订阅的时候，即subscribe()被调用的时候。

​	被观察者Obaservable的subscribe的多个重载的方法：

```java
//观察者不对被观察者发送的事件做出响应(但是被观察者还可以继续发送事件)
public final Disposable subscribe()
 
//观察者对被观察者发送的任何事件都做出响应
public final void subscribe(Observer<? super T> observer)
 
//表示观察者只对被观察者发送的Next事件做出响应
public final Disposable subscribe(Consumer<? super T> onNext)
 
//表示观察者只对被观察者发送的Next & Error事件做出响应
public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError)
 
//表示观察者只对被观察者发送的Next & Error & Complete事件做出响应
public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError,
                                  Action onComplete)
 
//表示观察者只对被观察者发送的Next & Error & Complete & onSubscribe事件做出响应
public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError,
                                  Action onComplete, Consumer<? super Disposable> onSubscribe)
```



## Scheduler

Scheduler调度器 线程控制器

### 提供的线程

* `Schedulers.immediate()` ：在当前线程运行（默认）

* `Schedulers.newThread()` : 总是启用新线程，并在新线程执行操作

* `Schedulers.io()` : I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的  `Scheduler` 。`io()` 的内部用了一个无数量上限的线程池实现，可以重用空闲的线程。 比`newThread()` 更有效率。

* `Schedulers.computation()`: 计算所使用的 `Scheduler`。这个计算指的是 CPU 密集型计算，即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 `Scheduler` 使用的固定的线程池，大小为 CPU 核数。不要把 I/O 操作放在 `computation()` 中，否则 I/O 操作的等待时间会浪费 CPU。

*  `AndroidSchedulers.mainThread()` 指定操作在Android 主线程运行

### 线程控制

  结合 `subscribeOn()` 和 `observeOn()` 两个方法来对线程进行控制 

 `subscribeOn()` ： 指定Observable被观察者subscribe()时所发生的线程，即指定发生事件的线程

 `observeOn()` : 指定Observer观察者接收&响应事件的线程，即订阅者接收事件的线程

 `Tips` :多次指定发射事件的线程只有第一次指定有效，即多次调用subscribeOn()只有第一次有效

## 操作符

`create()` 方法是 RxJava **最基本**的创造事件序列的方法。

`empty()`：快速创建被观察者对象，**仅发送onComplete()事件**，直接通知完成

```java
Observable.empty()
           .subscribe(new Observer<Object>() {
        @Override
        public void onSubscribe(Disposable d) {
            Log.e(TAG, "empty：onSubscribe == 订阅");
        }
        @Override
        public void onNext(Object o) {
            Log.e(TAG, "empty：onNext ==" + o.toString());
        }
 
        @Override
        public void onError(Throwable e) {
            Log.e(TAG, "empty：onError == " + e.getMessage());
        }
 
        @Override
        public void onComplete() {
            Log.e(TAG, "empty：onComplete == ");
        }
    });
```



log：

![img](https://img-blog.csdnimg.cn/20191017160732285.png)



`error()`：快速创建被观察者对象，仅发送onError()事件，直接通知异常。

`never()`：快速创建被观察者对象，不发送任何事件。

`just(T...)`: 将传入的参数依次发送出来 **最多只能发送10个事件。**

`fromArray(T... items)` ：快速创建被观察者对象，可发送多个任意子类型的数据。传入数组，`Observable` 创建后发送数组内所有数据。

`from(T[])` / `from(Iterable<? extends T>)` : 将传入的数组或 `Iterable` 拆分成具体对象后，依次发送出来。

`just(T...)` 和 `from(T[])` 与`create()` 是等价的

`map()` 

![image-20210922163746486](/Users/ruiyu/Documents/android_project/md笔记/image-20210922163746486.png)

例子：

```java
Observable.just("images/logo.png") // 输入类型 String
    .map(new Func1<String, Bitmap>() {
        @Override
        public Bitmap call(String filePath) { // 参数类型 String
            return getBitmapFromPath(filePath); // 返回类型 Bitmap
        }
    })
    .subscribe(new Action1<Bitmap>() {
        @Override
        public void call(Bitmap bitmap) { // 参数类型 Bitmap
            showBitmap(bitmap);
        }
    });
```

