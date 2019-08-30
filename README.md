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
单例是为了节约资源，对于频繁使用的对象，避免重复创建，减少GC压力。

创建单例模式实例只创建一次，所以构造函数要是private的，是其他类不能创建实例。<br>
为了访问类中的变量，构造一个public static的getInstance（）方法来获得类中的唯一私有静态变量。<br>
由于整个系统只有一个单例对象，则需要线程同步。

创建单例模式的方法
线程安全：懒汉式，饿汉式，双重校验锁
懒汉式，实例只有在使用的时候才被加载，不使用的时候不加载，使用synchronized保证线程安全

```java
public class Singleton{
private static Singleton singleton;
private Singleton(){};
public static `synchronized` Singleton getInstance(){
if(singleton==null) singleton=new Singleton();
return singleton;
}
}
```
饿汉式加锁带来的开销大，线程不安全问题主要是由于instance被实例化多次，采用直接实例化instance
的方式就不会昌盛线程不安全的问题，但是直接实例化的方式也丢失了延迟实例化带来的节约资源的好处
```java
public calss{
private static Singleton instance=new Singleton();
      private Singleton(){}
      public static ` ` Singleton getInstance(){

             return instance;
}
```
双重校验锁先判断instance是否已经被实例化，如果没有实例化就对实例化语句进行加锁，同时instance在<br>
使用的时候才实例化。延迟加载节约资源。在synchronized的锁外面再加一层判断，就可以在对象创建后不再<br>
进入synchronized同步块，减小了锁的粒度也提高了性能。
```java
public class A{
private  `valotile`  `static` A a;
private A(){}
public static A getA(){
if(a==null){
synchronized(A.classs){
 if(a==null) a=new A();
}
}
return a;
}
}
```
1 如果如下只使用一个if语句,当A和B线程同时访问的时候，实例不存在，虽然有了
synchronized但两个线程还是会先后创建实例，使得实例不唯一。<br>
```java
if(a==null){
synchronized(A.class){
 a=new A();
}
}
```
2 instance 使用volatile是为了防止指令重排，a=new A(),分三步执行，为a 分配内存，2 初始化，3 将a指向内存空间。<br>
会使上述顺序错乱，在多线程的状态下无法得到正常执行结果。<br>
3 使用Synchronized（A.class）锁住A类，synchronized（this）所著的是当前市里对象，每个线程都以自身的对象作为锁对象，<br>
而要实现线程同步必须使得锁唯一。

静态内部类实现

延迟初始化，JVM保证线程安全。
```java
public class A{
priavtte A(){};
private static class SingletonHolder{
private static final A=new A();
}
public static A getA(){
return singletonHolder.INSTANCE;}
}
```
枚举类

通过序列化和反序列化实现单例模式，防止反射攻击。  setAccessible()方法将私有构造设为public即为反射攻击。<br>
在其他的单利模式中使用transient关键字解决反射攻击
```java
public enum A{
INSTANCE;
private String objname;
public String getObjName() return objName;
public void setObjname(String objName) this.objname=objname;
}
```


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
Spring中的Bean是单例模式的，Spring实现线程安全是结合ThreadLocal来实现并发，<br>
但不论是单例还是ThreadLocal都被无法避免内存写的情况。内存泄露是长生命周期的对象引<br>
用了短生命周期的对象，就会导致到生命周期的对象无法被回收。  <br> 
ThreadLocal是以<key,value>的形式存储线程，key是弱引用，在下次GC之前就会被回收，<br>
这可能导致value无法被回收，而产生内存泄露。


