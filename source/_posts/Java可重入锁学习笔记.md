title: Java可重入锁学习笔记
date: 2015-11-20 20:51:25
tags: 
 - java 
 - 学习 
 - 并发
categories: 学习笔记
---
前几天被前辈问到这个可重入锁,结果忘掉了.于是抽空整个了解一下

## 目录 ##
1. 什么是可重入锁
2. 为什么要可重入
3. 如何实现可重入锁
4. 有不可重入锁吗
5. demo代码展示 
6. 参考文章

## 1 . 什么是可重入锁 ##

锁的概念就不用多解释了,当某个线程A已经持有了一个锁,当线程B尝试进入被这个锁保护的代码段的时候.就会被阻塞.而锁的操作粒度是"线程",而不是调用(至于为什么要这样,下面解释).同一个线程再次进入同步代码的时候.可以使用自己已经获取到的锁,这就是可重入锁
java里面内置锁(synchronize)和Lock(ReentrantLock)都是可重入的
## 2 . 为什么要可重入 ##

如果线程A继续再次获得这个锁呢?比如一个方法是synchronized,递归调用自己,那么第一次已经获得了锁,第二次调用的时候还能进入吗? 直观上当然需要能进入.这就要求必须是可重入的.可重入锁又叫做递归锁,再举个例子.

````
public class Widget {
        public synchronized void doSomething() {
            ...
        }
}
     
public class LoggingWidget extends Widget {
        public synchronized void doSomething() {
            System.out.println(toString() + ": calling doSomething");
            super.doSomething();//若内置锁是不可重入的，则发生死锁
        }
}
````
  这个例子是java并发编程实战中的例 子.synchronized 是父类Widget的内置锁,当执行子 类的方法的时候,先获取了一次Widget的锁,然后在执行super的时候,就要获取一次,如果不可重入,那么就跪了.

## 3 . 如何实现可重入锁 ##
 
为每个锁关联一个获取计数器和一个所有者线程,当计数值为0的时候,这个所就没有被任何线程只有.当线程请求一个未被持有的锁时,JVM将记下锁的持有者,并且将获取计数值置为1,如果同一个线程再次获取这个锁,技术值将递增,退出一次同步代码块,计算值递减,当计数值为0时,这个锁就被释放.
ReentrantLock里面有实现

## 4 . 有不可重入锁吗 ##

这个还真有.Linux下的pthread_mutex_t锁是默认是非递归的。可以通过设置PTHREAD_MUTEX_RECURSIVE属性，将pthread_mutex_t锁设置为递归锁。如果要自己实现不可重入锁,同可重入锁,这个计数器只能为1.或者0,再次进入的时候,发现已经是1了,就进行阻塞.jdk里面没有默认的实现类.

## 5 . demo代码展示 ##

5.1 内置锁的可重入
````
public class ReentrantTest {
    public void method1() {
        synchronized (ReentrantTest.class) {
            System.out.println("方法1获得ReentrantTest的内置锁运行了");
            method2();
        }
    }

    public void method2() {
        synchronized (ReentrantTest.class) {
            System.out.println("方法1里面调用的方法2重入内置锁,也正常运行了");
        }
    }

    public static void main(String[] args) {
        new ReentrantTest().method1();
    }
}
````

5.2 lock对象的可重入
```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockTest {
    private Lock lock = new ReentrantLock();

    public void method1() {
        lock.lock();
        try {
            System.out.println("方法1获得ReentrantLock锁运行了");
            method2();
        } finally {
            lock.unlock();
        }
    }

    public void method2() {
        lock.lock();
        try {
            System.out.println("方法1里面调用的方法2重入ReentrantLock锁,也正常运行了");
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        new ReentrantLockTest().method1();
    }
}
```

5.3 不同线程不可访问同一锁

````
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantTwoThreadTest {
    private static Lock lock = new ReentrantLock();

    private static class T1 extends Thread {
        @Override
        public void run() {
            System.out.println("线程1启动");
            lock.lock();
            try {
                Thread.sleep(10000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
            System.out.println("线程2终止");
        }
    }

    private static class T2 extends Thread {
        @Override
        public void run() {
            System.out.println("线程2启动");
            lock.lock();
            lock.unlock();
            System.out.println("线程2终止");
        }
    }


    public static void main(String[] args) {
        new T1().start();
		Thread.sleep(100);
        new T2().start();
    }
}
````

## 6. 参考文章 ##

1. [可重入锁测试](http://ikarishinjieva.github.io/tachikoma-blog/post/2013-03-13-reentrant-lock/)
2. [生产者消费者的一个更真实的例子](http://bininhere.github.io/blog/java/multiThread/ReentrantLock.html)
3. [浅谈Java中的锁](http://zhwbqd.github.io/2015/02/13/lock-in-java.html)
4. [java并发编程实战](https://book.douban.com/subject/10484692/)
