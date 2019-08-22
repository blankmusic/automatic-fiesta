# automatic-fiesta
I am confused.
# 面向对象的基本特征
面向对象有四个基本特征，抽象 继承 封装 多态

抽象 将一类对象的共同特征总结出来构造类的过程，包括数据抽象和行为抽象。抽象只关注对象有哪些行为和属性 不关注这些行为的细节是什么。
继承 是从已有类继承信息创建新类的过程。
封装
多态性 允许不同子类型的对象对同一消息做出不同的响应。简单地说，就是就是用同样的对象引用调用同样的方法但是做了不同的事情。多态性分为，编译时的多态性和运行时的多态性。

# Java并发中的AQS
```text
基本概念
AQS（AbstractQueueSyncronizer）抽象队列同步器，是一个框架，其中
exclusive（独占）：
ReentrantLock、
share（分享）
semaphore、
countDowanLatch
都依赖这个同步器。

框架
这个框架维护了一个 volatile的state和一个CLH（线程队列缓冲FIFO）
state的访问方式有3种
getState()
setState()'
compareAndSetState()

不同定义的同步器争用资源共享的方式也是不同的。
自定义的同步器在实现的实收只需要实现共享资源state的获取和释放方式即可。至于具体的线程登台队列的维护，AQS已经在顶层实现好了。自定义同步器只要时间以下几种方法
isHeldExclusively():该线程是否正在独占资源，只有用到Condition才会实现它
tryAcquire(int):独占方式，尝试获取资源，成果返回true，失败false
tryRelease(int):独占，尝试释放资源，返回同上
tryAcquireShared(int):共享方式。尝试获取资源。负数表示失败，0表示成果，但没有剩余资源可用，正数表示成功，且有剩余资源
tryReleaseShared（int）:共享方式。尝试释放资源。如何释放以后允许唤醒后续的等待节点返回true，否则返回false

ReentrantLock的获取和释放锁的具体实现：
首先，state初始化为0，表示状态未锁定。A线程lock（）时，会调用tryAcquire（）独占该锁，并且state+1，此后其他线程再tryAcquire就会失败。直到A线程unlock（）到state=0（释放锁）为止。其他的线程才有机会获取该所。当然是方之间，A线程是可以重复的获取此锁的state会累加。这就是可重入锁的概念。。但是获取多少次就要释放多少次，保障state的零状态。

CountDowanLatch对锁的获取和释放：
首先任务分配N个子线程，state也初始化为N和线程数相同。N个线程是并行执行的，每个线程执行完都要countDown()一次，state会CAS减1.等到所有的子线程执行完state=0，会UNpark（）主调用线程，主调用线程从await（）返回后，执行后续动作。

一般来说，自定义同步器要么是独占方法，要么是共享方式，他们也只需实现tryAcquire-tryRelease、tryAcquireShared-tryReleaseShared中的一种即可。但AQS也支持自定义同步器同时实现独占和共享两种方式，如ReentrantReadWriteLock。


```
