---
title: "[译]Java中的CountDownLatch和CyclicBarrier"
tags:
  - java
  - 学习
  - 工作
id: 3220
categories:
  - 我的翻译
date: 2013-09-15 08:03:09
---

本文译自官方文档，有细微改动，Java多线程的时候，看了好多文档，还是官方说的最清楚。结合自己的理解，译之。

## CountDownLatch

字面意思就是倒计数闩，后面会讲到，这里的同步允许一个或多个线程等待，，知道其他线程进行的一系列操作完成。而CountDownLatch通过一个参数count（数目）来构造，而await（）则阻塞当前线程，直到countDown()将count减为了0，然后，所有的阻塞线程被释放，也就是那些调用了await方法的线程立即返回，注意，这是一次性的，也就是说count不能被自动重置，如果你想这么做，CyclicBarrier是可以的。

CountDownLatch用处很多，当用count=1来构造的时候，这就相当于一个开关，所有调用了await方法的线程都在等待，直到有一个线程调用了countDown()，CountDownLatch通过count=N构造的话，就可以使一个线程等待其他N个线程完成操作，或者一个操作被做N次。

简单的demo：

一组worker（工人）线程使用两个CountDownLatch

第一个是开始信号，用来阻止工人提前操作，直到(driver)传送带准备好了才允许开始

第二个是完成信号，他使传送带等待直到所有的worker都完成
<pre class="lang:default decode:true"> class Driver { // ...
   void main() throws InterruptedException {
     CountDownLatch startSignal = new CountDownLatch(1);
     CountDownLatch doneSignal = new CountDownLatch(N);

     for (int i = 0; i &lt; N; ++i) // 创建并启动线程
       new Thread(new Worker(startSignal, doneSignal)).start();

     doSomethingElse();            // 传送带做点准备工作
     startSignal.countDown();      // 减为0，工人可以开始了
     doSomethingElse();
     doneSignal.await();           // 等待直到所有的工人完成任务
   }
 }

 class Worker implements Runnable {
   private final CountDownLatch startSignal;
   private final CountDownLatch doneSignal;
   Worker(CountDownLatch startSignal, CountDownLatch doneSignal) {
      this.startSignal = startSignal;
      this.doneSignal = doneSignal;
   }
   public void run() {
      try {
        startSignal.await();//工人们等待开关打开
        doWork();              //做事
        doneSignal.countDown(); //做完了就给countdownlatch 减去1
      } catch (InterruptedException ex) {} // return;
   }

   void doWork() { ... }
 }</pre>
另一个典型的例子就是把问题分成N部分，通过线程执行每一部分，具体的话是将线程入队到一个Executor对象里。然后调用execute方法。当执行完毕一部分，就并给latch 减去1，当减到0的时候调用await的方法就可以继续运行了，当需要重复计数的话，用CyclicBarrier代替
<pre>class Driver2 { // ...
   void main() throws InterruptedException {
     CountDownLatch doneSignal = new CountDownLatch(N);
     Executor e = ...

     for (int i = 0; i &lt; N; ++i) // 创建并开始线程
       e.execute(new WorkerRunnable(doneSignal, i));

     doneSignal.await();           // 等待所有的线程完成
   }
 }

 class WorkerRunnable implements Runnable {
   private final CountDownLatch doneSignal;
   private final int i;
   WorkerRunnable(CountDownLatch doneSignal, int i) {
      this.doneSignal = doneSignal;
      this.i = i;
   }
   public void run() {
      try {
        doWork(i);
        doneSignal.countDown();
      } catch (InterruptedException ex) {} // 返回;
   }

   void doWork() { ... }
 }</pre>
&nbsp;

## CyclicBarrier

字面意思回环栅栏。这里的同步是允许一系列的线程互相等待直到大家都来到一个barrier前面，当程序中有互相等待的情况的时候，非常有用，叫做回环是因为当所有等待线程都被释放以后，cyclicBarrier可以被重用

同时，CyclicBarrier支持一个可选的Runnable参数，当所有的线程都来到barrier前面的时候，程序开始执行runable线程动作，当需要更新所有线程共享的某个状态的时候，该动作非常有用。例子是一个并行分解的例子
<pre>class Solver {
   final int N;
   final float[][] data;
   final CyclicBarrier barrier;

   class Worker implements Runnable {
     int myRow;
     Worker(int row) { myRow = row; }
     public void run() {
       while (!done()) {
         processRow(myRow);

         try {
           barrier.await(); 
         } catch (InterruptedException ex) { 
           return; 
         } catch (BrokenBarrierException ex) { 
           return; 
         }
       }
     }
   }

   public Solver(float[][] matrix) {
     data = matrix;
     N = matrix.length;
     barrier = new CyclicBarrier(N, 
                                 new Runnable() {
                                   public void run() { 
                                     mergeRows(...); 
                                   }
                                 });
     for (int i = 0; i &lt; N; ++i) 
       new Thread(new Worker(i)).start();

     waitUntilDone();
   }
 }</pre>
这里每一个worker线程处理矩阵的一行，然后在barrier前面等待，直到所有的行都处理完成，当所有的都完成以后，这里
<pre> barrier = new CyclicBarrier(N, 
                                 new Runnable() {
                                   public void run() { 
                                     mergeRows(...); 
                                   }
                                 });</pre>
的Runnable参数执行来合并这些行，一旦有了正确的结果waitUntilDown（）就返回true，然后所有的线程终止。

如果barrier执行的这个动作没有对那些等待的线程做出回应，以使的这些线程做一些别的操作，我们可以这么写，将
<pre>barrier.await()</pre>
改为
<pre> if (barrier.await() == 0) {
     // 记录本次迭代的位置
   }</pre>
如果一个线程由于中断，失败或者超时过早的离开了barrier，那么所有的线程都会通过BrokenBarrierException引发异常。