# automatic-fiesta
I am confused.
# 面向对象的基本特征
面向对象有四个基本特征，抽象 继承 封装 多态

抽象 将一类对象的共同特征总结出来构造类的过程，包括数据抽象和行为抽象。抽象只关注对象有哪些行为和属性 不关注这些行为的细节是什么。
继承 是从已有类继承信息创建新类的过程。
封装
多态性 允许不同子类型的对象对同一消息做出不同的响应。简单地说，就是就是用同样的对象引用调用同样的方法但是做了不同的事情。
多态性分为，编译时的多态性和运行时的多态性。

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
自定义的同步器在实现的实收只需要实现共享资源state的获取和释放方式即可。至于具体的线程登台队列的维护，
AQS已经在顶层实现好了。自定义同步器只要时间以下几种方法
isHeldExclusively():该线程是否正在独占资源，只有用到Condition才会实现它
tryAcquire(int):独占方式，尝试获取资源，成果返回true，失败false
tryRelease(int):独占，尝试释放资源，返回同上
tryAcquireShared(int):共享方式。尝试获取资源。负数表示失败，0表示成果，但没有剩余资源可用，正数表示成功，且有剩余资源
tryReleaseShared（int）:共享方式。尝试释放资源。如何释放以后允许唤醒后续的等待节点返回true，否则返回false

ReentrantLock的获取和释放锁的具体实现：
首先，state初始化为0，表示状态未锁定。A线程lock（）时，会调用tryAcquire（）独占该锁，并且state+1
此后其他线程再tryAcquire就会失败。直到A线程unlock（）到state=0（释放锁）为止。其他的线程才有机会获取该所。
当然是方之间，A线程是可以重复的获取此锁的state会累加。这就是可重入锁的概念。。
但是获取多少次就要释放多少次，保障state的零状态。

CountDowanLatch对锁的获取和释放：
首先任务分配N个子线程，state也初始化为N和线程数相同。N个线程是并行执行的，每个线程执行完都要countDown()一次，
state会CAS减1.等到所有的子线程执行完state=0，会UNpark（）主调用线程，主调用线程从await（）返回后，执行后续动作。

一般来说，自定义同步器要么是独占方法，要么是共享方式，他们也只需实现tryAcquire-tryRelease、
tryAcquireShared-tryReleaseShared中的一种即可。
但AQS也支持自定义同步器同时实现独占和共享两种方式，如ReentrantReadWriteLock。


```
https://www.cnblogs.com/skywang12345/p/3324788.html
# comparator和comparable
```text
Comparable 简介

Comparable 是排序接口。

若一个类实现了Comparable接口，就意味着“该类支持排序”。  即然实现Comparable接口的类支持排序，
假设现在存在“实现Comparable接口的类的对象的List列表
(或数组)”，则该List列表(或数组)可以通过 Collections.sort（或 Arrays.sort）进行排序。

此外，“实现Comparable接口的类的对象”可以用作“有序映射(如TreeMap)”中的键或“有序集合(TreeSet)”中的元素，
而不需要指定比较器。

 

Comparable 定义

Comparable 接口仅仅只包括一个函数，它的定义如下：

package java.lang;
import java.util.*;

public interface Comparable<T> {
    public int compareTo(T o);
}
说明：
假设我们通过 x.compareTo(y) 来“比较x和y的大小”。若返回“负数”，意味着“x比y小”；返回“零”，
意味着“x等于y”；返回“正数”，意味着“x大于y”。

 

 

Comparator 简介

Comparator 是比较器接口。

我们若需要控制某个类的次序，而该类本身不支持排序(即没有实现Comparable接口)；那么，
我们可以建立一个“该类的比较器”来进行排序。这个“比较器”只需要实现Comparator接口即可。
也就是说，我们可以通过“实现Comparator类来新建一个比较器”，然后通过该比较器对类进行排序。

 

Comparator 定义

Comparator 接口仅仅只包括两个个函数，它的定义如下：

复制代码
package java.util;

public interface Comparator<T> {

    int compare(T o1, T o2);

    boolean equals(Object obj);
}
复制代码
说明：
(01) 若一个类要实现Comparator接口：它一定要实现compareTo(T o1, T o2) 函数，
但可以不实现 equals(Object obj) 函数。

        为什么可以不实现 equals(Object obj) 函数呢？ 因为任何类，默认都是已经实现了
	equals(Object obj)的。 Java中的一切类都是继承于java.lang.Object，
	在Object.java中实现了equals(Object obj)函数；所以，其它所有的类也相当于都实现了该函数。

(02) int compare(T o1, T o2) 是“比较o1和o2的大小”。返回“负数”，意味着“o1比o2小”；
返回“零”，意味着“o1等于o2”；返回“正数”，意味着“o1大于o2”。

 

 

Comparator 和 Comparable 比较

Comparable是排序接口；若一个类实现了Comparable接口，就意味着“该类支持排序”。
而Comparator是比较器；我们若需要控制某个类的次序，可以建立一个“该类的比较器”来进行排序。

我们不难发现：Comparable相当于“内部比较器”，而Comparator相当于“外部比较器”。

 
```
# 单例模式
```text
保证一个类只有一个实例，并提供一个访问它的全局访问点
1.构造方法私有化
2.双重检查实现线程安全
3.尽量延迟加载
4.定义静态的单例对象以及getInstance方法
```
```java
//java.lang.Runtime使用的单例模式（源码中为饿汉式,jdk中都是这样，但我们开发尽量用懒汉式）
//可以用 volatile 实现一个双重检查锁的单例模式：
public class Singleton{
	private static volatile Singleton singleton ;
	private Singleton(){}
	public static Singleton getInstance(){
		if(singleton == null){
			synchronize(Singleton.class){
			  if(singleton == null){
			    singleton = new Singleton();
			  }
			}
		}
		return singleton ;
	}
	
}
```
