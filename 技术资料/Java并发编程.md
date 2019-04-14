## 多线程与并发

首先一直很疑问的一点：volatile和synchronized

- synchronized关键字

  ```
  是一个修饰符，可以用来修饰方法或者代码块，又叫同步块
  synchronized是一种同步锁，它修饰的对象有
  1.修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围就是调用这个代码块的对象。
  例子
  	synchronized(this){};
  	SyncThread syncThread = new SyncThread();
  	Thread thread1 = new Thread(syncThread,"1");
  	Thread thread2 = new Thread(syncThread,"2");
  2.修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法，作用的对象是调用这个方法的对象。
  
  	上面代码中countAdd是一个synchronized的，printCount是非synchronized的。从上面的结果中可以看出一个线程访问一个对象的synchronized代码块时，别的线程可以访问该对象的非synchronized代码块而不受阻塞。
  	在AccountOperator 类中的run方法里，我们用synchronized 给account对象加了锁。这时，当一个线程访问account对象时，其他试图访问account对象的线程将会阻塞，直到该线程访问account对象结束。也就是说谁拿到那个锁谁就可以运行它所控制的那段代码。
  	当有一个明确的对象作为锁时，就可以用类似下面这样的方式写程序。
  	public void method3(SomeObject obj)
  	{
     		synchronized(obj){
     		  	 //obj 锁定的对象
            	 // todo
     		}
      }
       
  	当没有明确的对象作为锁，只是想让一段代码同步时，可以创建一个特殊的对象来充当锁：
  	
  3.修饰一个静态的方法，其作用范围是整个静态方法，作用的对象是这个类的所有对象（因为是类中的静态方法）
  4.修饰一个类，其作用范围是synchronized后面括号括起来的部分，作用的对象是这个类的所有对象。
  ```

- Lock对象
- Java锁的分类



- 如何停止一个线程 （仍需要细看 需要自己实验）

  ```
  1. 使用volatile变量终止正常运行的线程 + 抛异常法/Return法
  2. 组合使用interrupt方法与interruptted/isinterrupted方法终止正在运行的线程 + 抛异常法/Return法
  3. 使用interrupt方法终止 正在阻塞中的 线程 阻塞就是  wait();
  ```

- 何为线程安全的类 ✅

  ```
  在线程安全性的定义中，最核心的概念就是 正确性。当多个线程访问某个类时，
  不管运行时环境采用何种调度方式或者这些线程将如何交替执行，//无论
  并且在主调代码中不需要任何额外的同步或协同，//不需要任何同步或者协同
  这个类都能表现出正确的行为，那么这个类就是线程安全的。
  ```

- 线程通信方法，wait(); notify();notifyAll();定义在Object类中 ✅

  ```
  Wait-notify机制是在获取对象锁的前提下不同线程间的通信机制。在Java中，任意对象都可以当作锁来使用，由于锁对象的任意性，所以这些通信方法需要被定义在Object类里。
  ```

- 并发三准则 ✅

  ```
  1.异常不会导致死锁现象：当线程出现异常且没有捕获处理时，JVM会自动释放当前线程占用的锁，因此不会由于异常导致出现死锁现象，同时还会释放CPU；
  2.锁的是对象而非其引用
  3.有Wait必须有Notify
  ```

- 如何确保线程安全？

  ```
  在Java中有很多方法来保证线程安全 如
  1.通过加锁（Lock/Synchronized）保证对临界资源的互斥访问
  2.使用volatile关键字，轻量级同步机制，但不保证原子性？？
  3.使用不变类和线程安全类（原子类，并发容器（比如HashTable），同步容器）等
  ```

- Volatile关键字在Java中的作用 什么叫指令重排序？

  ```
  volatile的特殊规则保证了新值能立即同步到主内存，以及每次使用前立即从主内存刷新，保证了内存的可见性
  也禁止指令重排序，此外，synchronized关键字也可以保证内存可见性
  
  指令重排序问题在并发环境下会导致线程安全问题，volatile关键字通过禁止指令重排序来避免这一问题。而对于Synchronized关键字，其所控制范围内的程序在执行时独占的，指令重排序问题不会对其产生任何影响，因此无论如何，其都可以保证最终的正确性。
  ```

- 弱引用与强引用？

- Java中的死锁（DeadLock）其实和操作系统中的死锁区别不大

  ```
  死锁是指两个以上的线程永远阻塞的情况，这种情况产生至少需要两个以上的线程和两个以上的资源
  多个线程多个资源，占用不释放，循环请求，循环等待就构成了死锁
  
  分析死锁，我们需要查看Java应用程序的线程转储。我们需要找出那些状态为BLOCKED的线程和他们等待的资源。每个资源都有一个唯一的id，用这个id我们可以找出哪些线程已经拥有了它的对象锁。下面列举了一些JDK自带的死锁检测工具：
  Jconsole
  Jstack
  VisualVM
  ```

  

- 什么是线程池，如何创建一个线程池

  ```
  一个线程池管理了一组工作线程，同时它还包括一个用于放置等待执行的任务的队列。
  线程池可以避免线程的频繁创建与销毁，降低资源消耗，提高系统反应速度。
  java.util.concurrent.Executors 提供了几个Java.Util.concurrent.Executor接口的实现，用于创建线程池，其主要涉及四个角色
  线程池：Executor
  工作线程Worker线程，Worker的run()方法执行Job的run()方法
  任务Job：Runable 和 Callable
  阻塞队列：BlockingQueue，用过这个容器接口了
  ```

  - 线程池Executor

    Executor及其实现类是用户级的线程调度器，也是对任务执行机制的抽象，其将任务的提交与任务的执行分离开来，核心实现类包括ThreadPoolExecutor(用来执行被提交的任务)和ScheduledThreadPoolExecutor  (可以在给定的延迟后执行任务或者周期性执行任务)。

    Executor的实现继承链条为：(父接口)Executor -> (子接口)ExecutorService -> (实现类)[ ThreadPoolExecutor + ScheduledThreadPoolExecutor ]。

  - 任务Runnable/Callable

    Runnable（run）和Callable（call）都是对任务的抽象，但是Callable

- 单例模式与多线程

  ```
  - 饿汉模式线程安全
  - 懒汉模式是非线程安全的
  - 多线程环境下如何创建线程安全的单例
  
  为什么有单例模式，整个系统只需要一个全局对象，有利于我们协调系统整体的行为
  单例模式就是为确保一个类只有一个实例，并为整个系统提供一个全局访问点的方法
  
  - 单线程下两种经典的实现模式
  饿汉模式 私有构造，静态取用方法，私有静态实例 bean也是如此，bean就是遵循统一类的模版
  懒汉模式 需时加载
  
  - 从速度和反应时间来说，饿汉式要好一些；从资源利用效率上来说，懒汉式又好一些
  
  - 单例模式的优点
  - 使用单例模式的时候，必须使用单例类提供的公有工厂方法得到单例对象，而不应该用反射机制
  
  ```

  

