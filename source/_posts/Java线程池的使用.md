---
title: Java线程池的使用
date: 2023-02-09 16:37:24
categories: "工程"
tags: ["Java", "多线程"]
---

Java语言虽然内置了多线程支持，启动一个新线程非常方便，但是，创建线程需要操作系统资源（线程资源，栈空间等），频繁创建和销毁大量线程需要消耗大量时间。<!-- more -->
如果可以复用一组线程，那么我们就可以把很多小任务让一组线程来执行，而不是一个任务对应一个新线程。这种能接收大量小任务并进行分发处理的就是线程池。

## 基本使用

![线程池示意图](Java线程池的使用/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

简单地说，线程池内部维护了若干个线程，没有任务的时候，这些线程都处于等待状态。如果有新任务，就分配一个空闲线程执行。如果所有线程都处于忙碌状态，新任务要么放入队列等待，要么增加一个新线程进行处理。
Java标准库提供了`ExecutorService`接口表示线程池，它的典型用法如下：

```java
// 创建固定大小的线程池:
ExecutorService es = Executors.newFixedThreadPool(3);
// 提交任务:
es.submit(task1);
es.submit(task2);
es.submit(task3);
es.submit(task4);
es.submit(task5);
```

因为`ExecutorService`只是接口，Java标准库提供的几个常用实现类有：

* FixedThreadPool：线程数固定的线程池。
* CachedThreadPool：线程数根据任务动态调整的线程池。
* SingleThreadExecutor：仅单线程执行的线程池。
* ScheduledThreadPool：可定时、反复执行的线程池。

创建这些线程池的方法都被封装到`Executors`这个类中。我们以`FixedThreadPool`为例，看看线程池的执行逻辑：

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
        // 创建一个固定大小的线程池:
        ExecutorService es = Executors.newFixedThreadPool(4);
        for (int i = 0; i < 6; i++) {
            int name = i;
            es.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println("start task " + name);
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                    }
                    System.out.println("end task " + name);
                }
            });
        }
        // 关闭线程池:
        es.shutdown();
    }
}
```

我们观察执行结果，一次性放入6个任务，由于线程池只有固定的4个线程，因此，前4个任务会同时执行，等到有线程空闲后，才会执行后面的两个任务。

线程池在程序结束的时候要关闭。使用`shutdown()`方法关闭线程池的时候，它会等待正在执行的任务先完成，然后再关闭。`shutdownNow()`会立刻停止正在执行的任务，`awaitTermination()`则会等待指定的时间让线程池关闭。

如果我们把线程池改为`CachedThreadPool`，由于这个线程池的实现会根据任务数量动态调整线程池的大小，所以6个任务可一次性全部同时执行。

如果我们想把线程池的大小限制在4～10个之间动态调整怎么办？我们查看`Executors.newCachedThreadPool()`方法的源码：

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                    60L, TimeUnit.SECONDS,
                                    new SynchronousQueue<Runnable>());
}
```

因此，想创建指定动态范围的线程池，可以这么写：

```java
int min = 4;
int max = 10;
ExecutorService es = new ThreadPoolExecutor(min, max,
        60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());
```

还有一种任务，需要定期反复执行，例如，每秒刷新证券价格。这种任务本身固定，需要反复执行的，可以使用`ScheduledThreadPool`。放入`ScheduledThreadPool`的任务可以定期反复执行。
创建一个`ScheduledThreadPool`仍然是通过`Executors`类：

```java
ScheduledExecutorService ses = Executors.newScheduledThreadPool(4);
```

我们可以提交一次性任务，它会在指定延迟后只执行一次：

```java
// 1秒后执行一次性任务:
ses.schedule(new Task("one-time"), 1, TimeUnit.SECONDS);

// 2秒后开始执行定时任务，每3秒执行:
ses.scheduleAtFixedRate(new Task("fixed-rate"), 2, 3, TimeUnit.SECONDS);

// 2秒后开始执行定时任务，以3秒为间隔执行:
ses.scheduleWithFixedDelay(new Task("fixed-delay"), 2, 3, TimeUnit.SECONDS);
```

注意FixedRate和FixedDelay的区别。FixedRate是指任务总是以固定时间间隔触发，不管任务执行多长时间：
![FixedRate示意图](Java线程池的使用/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E4%BD%BF%E7%94%A81.png)

而FixedDelay是指，上一次任务执行完毕后，等待固定的时间间隔，再执行下一次任务：
![FixedDelay示意图](Java线程池的使用/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E4%BD%BF%E7%94%A82.png)

因此，使用ScheduledThreadPool时，我们要根据需要选择执行一次、FixedRate执行还是FixedDelay执行。

还可以思考下面的问题：

* 在FixedRate模式下，假设每秒触发，如果某次任务执行时间超过1秒，后续任务会不会并发执行？
不会，只不过会在当前任务结束后立即执行。除非这个 Job 方法用 @Async 注解了，使得任务不在 TaskScheduler 线程池中执行，而是每次创建新线程来执行。
* 如果任务抛出了异常，后续任务是否继续执行？
抛出异常的话后续任务不继续执行，但可以利用try-catch避免抛出异常。

Java标准库还提供了一个`java.util.Timer`类，这个类也可以定期执行任务，但是，一个`Timer`会对应一个`Thread`，所以，一个`Timer`只能定期执行一个任务，多个定时任务必须启动多个`Timer`，而一个`ScheduledThreadPool`就可以调度多个定时任务，所以，我们完全可以用`ScheduledThreadPool`取代旧的`Timer`。

## 阿里规范

### 推荐用法

阿里巴巴开发手册并发编程这块有一条：线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式手动创建。
![3](Java线程池的使用/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E4%BD%BF%E7%94%A83.png)

推荐的用法：

```java
// 1
ThreadFactory namedThreadFactory = new ThreadFactoryBuilder()
                .setNameFormat("demo-pool-%d").build();
ExecutorService pool = new ThreadPoolExecutor(5, 200, 0L, TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<>(1024), namedThreadFactory, new ThreadPoolExecutor.AbortPolicy());

// 2
ScheduledExecutorService executorService = new ScheduledThreadPoolExecutor(1,
                new BasicThreadFactory.Builder().namingPattern("example-schedule-pool-%d").daemon(true).build());
```

### ThreadPoolExecutor参数

具体参数对应的逻辑关系可以往下看，这里只粗略的介绍一下。

* `corePoolSize`：核心线程池大小
* `maximumPoolSize`：最大线程池大小
* `keepAliveTime`：线程池中超过corePoolSize数目的空闲线程最大存活时间
当设置allowCoreThreadTimeOut(true)时，线程池中corePoolSize线程空闲时间达到keepAliveTime也将关闭
* `unit`：时间单位
* `workQueue`：保存任务的阻塞队列
* `threadFactory`：创建线程的工厂
* `handler`：当提交任务数超过maxmumPoolSize+workQueue之和时，任务会交给RejectedExecutionHandler来处理

### 线程池执行任务逻辑和线程池参数的关系

![执行逻辑](Java线程池的使用/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E4%BD%BF%E7%94%A84.png)

执行逻辑说明：

* 判断核心线程数是否已满，核心线程数大小和corePoolSize参数有关，未满则创建线程执行任务
* 若核心线程池已满，判断队列是否满，队列是否满和workQueue参数有关，若未满则加入队列中
* 若队列已满，判断线程池是否已满，线程池是否已满和maximumPoolSize参数有关，若未满创建线程执行任务
* 若线程池已满，则采用拒绝策略处理无法执执行的任务，拒绝策略和handler参数有关

**具体的例子：**

线程池刚创建时，里面没有一个线程。任务队列是作为参数传进来的。

1. 当调用 execute() 方法添加一个任务时，线程池会做如下判断：
    * 如果正在运行的线程数量小于 corePoolSize，那么马上创建线程运行这个任务；
    * 如果正在运行的线程数量大于或等于 corePoolSize，那么将这个任务放入队列。
    * 如果这时候队列满了，而且正在运行的线程数量小于 maximumPoolSize，那么还是要创建线程运行这个任务；
    * 如果队列满了，而且正在运行的线程数量大于或等于 maximumPoolSize，那么线程池会抛出异常，告诉调用者“我不能再接受任务了”。

2. 当一个线程完成任务时，它会从队列中取下一个任务来执行。
3. 当一个线程无事可做，超过一定的时间（keepAliveTime）时，线程池会判断，如果当前运行的线程数大于 corePoolSize，那么这个线程就被停掉。所以线程池的所有任务完成后，它最终会收缩到 corePoolSize 的大小。

这样的过程说明，并不是先加入任务就一定会先执行。假设队列大小为 10，corePoolSize 为 3，maximumPoolSize 为 6，那么当加入 20 个任务时，执行的顺序就是这样的：首先执行任务 1、2、3，然后任务 4~13 被放入队列。这时候队列满了，任务 14、15、16 会被马上执行，而任务 17~20 则会抛出异常。最终顺序是：1、2、3、14、15、16、4、5、6、7、8、9、10、11、12、13。

### 线程池的阻塞队列的选择

如果线程数超过了corePoolSize，则开始把线程先放到阻塞队列里，相当于生产者消费者的一个数据通道，有以下一些阻塞队列可供选择：

* `ArrayBlockingQueue`：ArrayBlockingQueue是一个有边界的阻塞队列，它的内部实现是一个数组。有边界的意思是它的容量是有限的，我们必须在其初始化的时候指定它的容量大小，容量大小一旦指定就不可改变。
* `DelayQueue`：DelayQueue阻塞的是其内部元素，DelayQueue中的元素必须实现 java.util.concurrent.Delayed接口，该接口只有一个方法就是long getDelay(TimeUnit unit)，返回值就是队列元素被释放前的保持时间，如果返回0或者一个负值，就意味着该元素已经到期需要被释放，此时DelayedQueue会通过其take()方法释放此对象，DelayQueue可应用于定时关闭连接、缓存对象，超时处理等各种场景。
* `LinkedBlockingQueue`：LinkedBlockingQueue阻塞队列大小的配置是可选的，如果我们初始化时指定一个大小，它就是有边界的，如果不指定，它就是无边界的。说是无边界，其实是采用了默认大小为Integer.MAX_VALUE的容量 。它的内部实现是一个链表。
* `PriorityBlockingQueue`：PriorityBlockingQueue是一个没有边界的队列，它的排序规则和 java.util.PriorityQueue一样。需要注意，PriorityBlockingQueue中允许插入null对象。所有插入PriorityBlockingQueue的对象必须实现 java.lang.Comparable接口，队列优先级的排序规则就是按照我们对这个接口的实现来定义的。
* `SynchronousQueue`：SynchronousQueue队列内部仅允许容纳一个元素。当一个线程插入一个元素后会被阻塞，除非这个元素被另一个线程消费。

使用的最多的应该是`LinkedBlockingQueue`，注意一般情况下要配置一下队列大小，设置成有界队列，否则JVM内存会被撑爆！

## 饱和策略的选择

饱和策略指的就是线程池已满情况下任务的处理策略，线程池已满的定义，是指运行`线程数==maximumPoolSize`，并且workQueue是有界队列并且已满（如果是无界队列当然永远不会满）。这时候再提交任务怎么办呢？线程池会将任务传递给最后一个参数RejectedExecutionHandler来处理，比如打印报错日志、抛出异常、存储到Mysql/redis用于后续处理等等。

默认有以下几种：

* ThreadPoolExecutor.AbortPolicy 直接抛出异常RejectedExecutionException
* ThreadPoolExecutor.CallerRunsPolicy 直接调用run方法并且阻塞执行
* ThreadPoolExecutor.DiscardPolicy 直接丢弃后来的任务
* ThreadPoolExecutor.DiscardOldestPolicy 丢弃在队列中队首的任务

## 优化线程池的配置

一般需要根据任务的类型来配置线程池大小：

* 如果是CPU密集型任务，就需要尽量压榨CPU，参考值可以设为 NCPU+1
* 如果是IO密集型任务，参考值可以设置为2*NCPU

其中NCPU的指的是CPU的核心数，可以使用`Runtime.getRuntime().availableProcessors()`来获取

当然，这只是一个参考值，具体的设置还需要根据实际情况进行调整，比如可以先将线程池大小设置为参考值，
再观察任务运行情况和系统负载、资源利用率来进行适当调整。
