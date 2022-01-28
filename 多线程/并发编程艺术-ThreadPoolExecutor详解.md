# ThreadPoolExecutor

```java

    /**
     * Creates a new {@code ThreadPoolExecutor} with the given initial
     * parameters and default thread factory and rejected execution handler.
     * It may be more convenient to use one of the {@link Executors} factory
     * methods instead of this general purpose constructor.
     *
     * @param corePoolSize the number of threads to keep in the pool, even
     *        if they are idle, unless {@code allowCoreThreadTimeOut} is set
     * @param maximumPoolSize the maximum number of threads to allow in the
     *        pool
     * @param keepAliveTime when the number of threads is greater than
     *        the core, this is the maximum time that excess idle threads
     *        will wait for new tasks before terminating.
     * @param unit the time unit for the {@code keepAliveTime} argument
     * @param workQueue the queue to use for holding tasks before they are
     *        executed.  This queue will hold only the {@code Runnable}
     *        tasks submitted by the {@code execute} method.
     * @throws IllegalArgumentException if one of the following holds:<br>
     *         {@code corePoolSize < 0}<br>
     *         {@code keepAliveTime < 0}<br>
     *         {@code maximumPoolSize <= 0}<br>
     *         {@code maximumPoolSize < corePoolSize}
     * @throws NullPointerException if {@code workQueue} is null
     */
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
             Executors.defaultThreadFactory(), defaultHandler);
    }
```

Executor框架最核心的类是ThreadPoolExecutor，它是线程池的实现类，主要由下列4个组 件构成。 ·

- corePool：核心线程池的大小。 
- maximumPool：最大线程池的大小。 
- BlockingQueue：用来暂时保存任务的工作队列。 
- RejectedExecutionHandler：饱和策略。当ThreadPoolExecutor已经关闭或ThreadPoolExecutor已经饱和时（达到了最大线程池大小且工作队列已满），execute()方法将要调用的Handler。



## FixedThreadPool

可重用固定线程数的线程池

```java

    /**
     * Creates a thread pool that reuses a fixed number of threads
     * operating off a shared unbounded queue.  At any point, at most
     * {@code nThreads} threads will be active processing tasks.
     * If additional tasks are submitted when all threads are active,
     * they will wait in the queue until a thread is available.
     * If any thread terminates due to a failure during execution
     * prior to shutdown, a new one will take its place if needed to
     * execute subsequent tasks.  The threads in the pool will exist
     * until it is explicitly {@link ExecutorService#shutdown shutdown}.
     *
     * @param nThreads the number of threads in the pool
     * @return the newly created thread pool
     * @throws IllegalArgumentException if {@code nThreads <= 0}
     */
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```

1）如果当前运行的线程数少于corePoolSize，则创建新线程来执行任务。 

2）在线程池完成预热之后（当前运行的线程数等于corePoolSize），将任务加入 LinkedBlockingQueue。 

3）线程执行完1中的任务后，会在循环中反复从LinkedBlockingQueue获取任务来执行。

FixedThreadPool使用**无界队列LinkedBlockingQueue**作为线程池的工作队列（队列的容量为 Integer.MAX_VALUE）

1）当线程池中的线程数达到corePoolSize后，新任务将在无界队列中等待，因此线程池中的线程数不会超过corePoolSize。 

2）由于1，**使用无界队列时maximumPoolSize将是一个无效参数**。 

3）由于1和2，**使用无界队列时keepAliveTime将是一个无效参数**。 

4）由于使用无界队列，运行中的FixedThreadPool（未执行方法`shutdown()`或 `shutdownNow()`）==不会拒绝任务==（不会调用`RejectedExecutionHandler.rejectedExecution`方法）。



## SingleThreadExecutor

```java
/**
 * Creates an Executor that uses a single worker thread operating
 * off an unbounded queue. (Note however that if this single
 * thread terminates due to a failure during execution prior to
 * shutdown, a new one will take its place if needed to execute
 * subsequent tasks.)  Tasks are guaranteed to execute
 * sequentially, and no more than one task will be active at any
 * given time. Unlike the otherwise equivalent
 * {@code newFixedThreadPool(1)} the returned executor is
 * guaranteed not to be reconfigurable to use additional threads.
 *
 * @return the newly created single-threaded Executor
 */
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

SingleThreadExecutor使用**无界队列LinkedBlockingQueue**作为线程池的工作队列（队列的容量为Integer.MAX_VALUE）。



## CachedThreadPool

CachedThreadPool是一个会根据需要创建新线程的线程池。

```java
/**
 * Creates a thread pool that creates new threads as needed, but
 * will reuse previously constructed threads when they are
 * available.  These pools will typically improve the performance
 * of programs that execute many short-lived asynchronous tasks.
 * Calls to {@code execute} will reuse previously constructed
 * threads if available. If no existing thread is available, a new
 * thread will be created and added to the pool. Threads that have
 * not been used for sixty seconds are terminated and removed from
 * the cache. Thus, a pool that remains idle for long enough will
 * not consume any resources. Note that pools with similar
 * properties but different details (for example, timeout parameters)
 * may be created using {@link ThreadPoolExecutor} constructors.
 *
 * @return the newly created thread pool
 */
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

这里把keepAliveTime设置为60L，意味着 CachedThreadPool中的空闲线程等待新任务的最长时间为60秒，空闲线程超过60秒后将会被 终止。

CachedThreadPool**使用没有容量的SynchronousQueue作为线程池的工作队列，但 CachedThreadPool的maximumPool是无界的。**

如果主线程提交任务的速度高于 maximumPool中线程处理任务的速度时，CachedThreadPool会不断创建新线程。极端情况下， CachedThreadPool会因为创建过多线程而耗尽CPU和内存资源。

1）首先执行`SynchronousQueue.offer（Runnable task）`。如果当前maximumPool中有空闲线程 正在执行`SynchronousQueue.poll（keepAliveTime，TimeUnit.NANOSECONDS）`，那么主线程执行 offer操作与空闲线程执行的poll操作配对成功，主线程把任务交给空闲线程执行，`execute()`方法执行完成；否则执行下面的步骤2）。 

2）当初始maximumPool为空，或者maximumPool中当前没有空闲线程时，将没有线程执行 `SynchronousQueue.poll（keepAliveTime，TimeUnit.NANOSECONDS）`。这种情况下，步骤1）将失 败。此时CachedThreadPool会创建一个新线程执行任务，`execute()`方法执行完成。 

3）在步骤2）中新创建的线程将任务执行完后，会执行 `SynchronousQueue.poll（keepAliveTime，TimeUnit.NANOSECONDS）`。这个poll操作会让空闲线 程最多在SynchronousQueue中等待60秒钟。如果60秒钟内主线程提交了一个新任务（主线程执行步骤1）），那么这个空闲线程将执行主线程提交的新任务；否则，这个空闲线程将终止。由于空闲60秒的空闲线程会被终止，因此长时间保持空闲的CachedThreadPool不会使用任何资源。





# ScheduledThreadPoolExecutor

它主要用来在给定的延迟之后运 行任务，或者定期执行任务。

DelayQueue是一个无界队列，所以ThreadPoolExecutor的maximumPoolSize在ScheduledThreadPoolExecutor中没有什么意义

ScheduledThreadPoolExecutor的执行主要分为两大部分。 

1）当调用ScheduledThreadPoolExecutor的scheduleAtFixedRate()方法或者scheduleWithFixedDelay()方法时，会向ScheduledThreadPoolExecutor的DelayQueue添加一个实现了 RunnableScheduledFutur接口的ScheduledFutureTask。

 2）线程池中的线程从DelayQueue中获取ScheduledFutureTask，然后执行任务。

![image-20220116135520204](F:\WorkSpace\Lian\notes\多线程\线程并发ThreadPoolExecutor详解.assets\image-20220116135520204.png)

1）线程1从DelayQueue中获取已到期的`ScheduledFutureTask（DelayQueue.take()）`。到期任务 是指ScheduledFutureTask的time大于等于当前时间。 

2）线程1执行这个ScheduledFutureTask。 

3）线程1修改ScheduledFutureTask的time变量为下次将要被执行的时间。 

4）线程1把这个修改time之后的ScheduledFutureTask放回DelayQueue中`（DelayQueue.add()）`。





# FutureTask

FutureTask除了实现Future接口外，还实现了Runnable接口。因此，FutureTask可以交给 Executor执行，也可以由调用线程直接执行`（FutureTask.run()）`。





