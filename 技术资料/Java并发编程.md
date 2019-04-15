## 多线程与并发

首先一直很疑问的一点：volatile和synchronized

## Java锁的分类及优化

- java锁的分类

  ```
  其实对于 Java 锁的分类没有严格意义的规则，
  我们常说的分类一般都是依据锁的特性、锁的设计、锁的状态等进行归纳整理的
  1.公平锁、非公平锁：
  公平锁是指多个线程按照锁的顺序来获取锁，非公平锁就是没有顺序完全随机，不能先来先得，没有优先级
  所以非公平锁会造成优先级反转或者饥饿现象
  比如：sychronized 内置锁，对象锁就是非公平锁，ReentrantLock(使用CAS和AQS实现)通过构造函数可以决定是公平锁还是非公平锁，默认构造是公平锁，非公平锁的吞吐量性能比公平锁好。
  2.可重入锁：
  又名递归锁，指在同一个线程在外层方法获取锁的时候在进入内层方法的时候会自动获取锁，就是获得锁的方法，是层层调用的，其中会call其他方法。这个没什么好说的，应该可重入
  所以synchronized和ReentrantLock都是可重入锁，可重入锁可以在一定程度上避免死锁。
  3.独享锁，共享锁：独享锁是指该锁一次只能被一个线程持有，共享锁指该锁可以被多个线程持有；
  synchronized和ReentrantLock都是独享锁，ReadWtireLock的读锁是共享锁，写锁是独占锁
  ReentrantLock的独享锁和共享锁也是通过AQS来实现的
  4.互斥锁，读写锁：其实就是独享锁、共享锁的具体说法，互斥锁的实质就是ReentrantLock，读写锁实质就是ReadWriteLock
  5.乐观锁，悲观锁：这个分类不是具体锁的分类，而是看待并发同步的角度；
  ```

- 对Java并发的理解：

  在多线程编程中，线程安全问题是一个最为关键的问题，其核心概念就在于正确性，即当多个线程访问某一共享、可变数据时，始终都不会导致数据结构的破坏和其他Exception。

  ```
  所有的并发模式在解决这个问题的时候（原来的生产者消费者模型不知道有什么关系，还有防止死锁的知识）
  采用的方案都是序列化访问临界资源
  在 Java 中，提供了两种方式来实现同步互斥访问：synchronized 和 Lock。本文针对 synchronized 内置锁 详细讨论了其在 Java 并发 中的应用，包括它的具体使用场景（同步方法、同步代码块、实例对象锁 
  ```

- 线程安全问题

  ```
  在单线程中不会出现线程安全问题，而在多线程编程中，有可能会出现同时访问同一个 共享、可变资源 的情况，这种资源可以是：一个变量、一个对象、一个文件等。特别注意两点，
  共享：不同线程都可访问，且可以同时访问
  可变：该资源在生命周期内可以被修改
  由于每个线程执行的过程是不可控的，所以需要采用同步机制来协同对对象可变状态的访问。
  
  就算已经实现了同步操作，但是开多个线程还是有可能出脏读的问题
  
  不过，当多个线程执行一个方法时，该方法内部的局部变量并不是临界资源，因为这些局部变量是在每个线程的私有栈中，因此不具有共享性，不会导致线程安全问题。
  ```

- 如何解决线程安全问题

  ```
  什么叫序列化访问临界资源：
  即在同一时刻，只能有一个线程访问临界资源，也称作 同步互斥访问。
  ```

  

- synchronized关键字（内置锁，也是对象锁）

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

- synchronized修饰方法的时候要注意 就是对于synchronized 修饰方法的时候要注意

  ```
  1.当一个线程正在访问一个对象的synchronized方法，那么其他线程不能访问该对象的其他synchronized方法，这个原因很简单，因为调用synchronized方法 还是要获取对象锁，当一个线程获取了该对象的锁之后，其他线程无法获取该对象的锁，所以无法访问该对象的其他synchronized方法。
  2.当一个线程正在访问一个对象的 synchronized 方法，那么其他线程能访问该对象的非 synchronized 方法。这个原因很简单，访问非 synchronized 方法不需要获得该对象的锁，假如一个方法没用 synchronized 关键字修饰，说明它不会使用到临界资源，那么其他线程是可以访问这个方法的
  3.如果一个线程 A 需要访问对象 object1 的 synchronized 方法 fun1，另外一个线程 B 需要访问对象 object2 的 synchronized 方法 fun1，即使 object1 和 object2 是同一类型），也不会产生线程安全问题，因为他们访问的是不同的对象，所以不存在互斥问题。
  访问不同对象，即时同一个类的对象，也不会出现问题，因为不是同一个对象锁
  ```

- synchronized代码块 

  ```
  从上面代码可以看出，synchronized代码块 比 synchronized方法 的粒度更细一些，使用起来也灵活得多。因为也许一个方法中只有一部分代码只需要同步，
  如果此时对整个方法用synchronized进行同步，会影响程序执行效率。而使用synchronized代码块就可以避免这个问题，synchronized代码块可以实现只对需要同步的地方进行同步。
  ```

- class对象锁

  ```
  synchronized修饰的方法在执行的时候就会获取该对象的锁，但是对于静态方法，没有实例对象，就要获取该class的锁
  特别地，每个类也会有一个锁，静态的 synchronized方法 就是以Class对象作为锁。另外，它可以用来控制对 static 数据成员 （static 数据成员不专属于任何一个对象，是类成员） 的并发访问。
  
  同时 类锁和对象锁是不互斥的，正如不同对象的对象锁之间不互斥
  ```

- 可重入锁 线程

  ```
  一般地，当某个线程请求一个由其他线程持有的锁时，发出请求的线程就会阻塞。然而，由于 Java 的内置锁是可重入的，因此如果某个线程试图获得一个已经由它自己持有的锁时，那么这个请求就会成功。
  可重入锁最大的作用是避免死锁。
  ```

- synchronized底层实现原理

  ```
  Java中每个对象都以用来实现一个同步锁
  线程在进入同步代码块之前会自动获取锁，并且在退出同步代码块时自动释放锁，无论是通过正常路径退出，还是通过代码块中抛出异常退出。获得内置锁的唯一途径就是进入由这个锁保护的同步方法或代码块。
  synchronized是JVM层面的，从JVM规范里可以看到synchronized在JVM里的实现原理，JVM基于进入和退出Monitor对象来实现方法同步和代码块同步，但两者的实现细节不一样
  
  原理，synchronized的原理 是monitorenter和monitorexit
  monitorenter 指令 是在 编译 后 插入 到 同步 代码 块 的 开始 位置， 而 monitorexit 是 插入 到 方法 结束 处 和 异常 处， JVM 要 保证 每个 monitorenter 必须 有 对应 的 monitorexit 与之 配对。 任何 对象 都有 一个 monitor 与之 关联， 当 且 一个 monitor 被 持有 后， 它将 处于 锁定 状态。 线程 执行 到 monitorenter 指令时， 将会 尝试 获取 对象 所 对应 的 monitor 的 所有权， 即 尝试 获得 对象 的 锁。
  
  ```

  

- Lock对象

  而Lock是一个Java接口(API如下图所示)，是基于JDK层面实现的，通过这个接口可以实现同步访问，

  它提供了比synchronized关键字更灵活、更广泛、粒度更细的锁操作，底层是由AQS实现的。二者之间的差异总结如下：

![0B2EDC9B-82A9-4E0B-A4C9-F31B727733DD](/Users/Haoyu/Desktop/0B2EDC9B-82A9-4E0B-A4C9-F31B727733DD.png)

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






## 并发编程的实现问题

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

    Runnable（run）和Callable（call）都是对任务的抽象，但是Callable可以返回任务执行的结果或者抛出异常

  - 任务执行状态future

    Future是对任务执行状态和结果的抽象，核心实现类是futureTask（所以它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值）

  ```
  四种常用的线程池：
  1.FixedThreadPool
  用于创建使用固定线程数的ThreadPool，corePoolSize = maximumPoolSize = n(固定的含义)，阻塞队列为LinkedBlockingQueue。
  public static ExecutorService newFixedThreadPool(int nThreads) {
          return new ThreadPoolExecutor(nThreads, nThreads,
                                        0L, TimeUnit.MILLISECONDS,
                                        new LinkedBlockingQueue<Runnable>());
      }
      
      
  2.SingleThreadExecutor
  用于创建一个单线程的线程池，corePoolSize = maximumPoolSize=1 阻塞队列为LinkedBlockingQueue
  3.CachedThreadPool
  4.ScheduledThreadPoolExecutor
  用于创建一个大小无限的线程池，此线程池支持定时以及周期性执行任务的需求
  所以阻塞队列为DelayedWorkQueue();
  public ScheduledThreadPoolExecutor(int corePoolSize) {
          super(corePoolSize, Integer.MAX_VALUE, 0, TimeUnit.NANOSECONDS,
                new DelayedWorkQueue());
      }
  
  
  ```

- 线程池的饱和策略

- 线程池调优

- CAS：CAS自旋volatile变量，是一种很经典的用法

  ```
  CAS有三个操作数，CAS在java中是通过unsafe类的compareAndSwap (JNI, Java Native Interface) 方法实现的，该方法包含四个参数：第一个参数是要修改的对象，第二个参数是对象中要修改的变量的偏移量，第三个参数是修改之前的值，第四个参数是预想修改之后的值
  内存值V，旧的预期值A，新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。
  但是这样仍旧存在问题：ABA问题，循环时间长开销大问题，和只能保证一个共享变量的原子操作（原子的意思就是，要么做，要么不做，没有做一半）
  各种乐观锁的实现中通常都会用版本戳version来对记录或对象标记，避免并发操作带来的问题，
  ```

- AQS队列同步器

  ```
  队列同步器(AbstractQueuedSynchronizer)是用来构建锁和其他同步组件的基础框架，
  技术是 CAS自旋Volatile变量：
  它使用了一个Volatile成员变量表示同步状态，通过CAS修改该变量的值，修改成功的线程表示获取到该锁；若没有修改成功，或者发现状态state已经是加锁状态，则通过一个Waiter对象封装线程，添加到等待队列中，并挂起等待被唤醒。
  同步器是实现锁的关键，子类通过继承同步器并实现它的抽象方法来管理同步状态，利用同步器实现锁的语义。特别地，锁是面向锁使用者的，它定义了使用者与锁交互的接口，隐藏了实现细节；同步器面向的是锁的实现者，它简化了锁的实现方式，屏蔽了同步状态管理、线程排队、等待与唤醒等底层操作。
  
  锁和同步器很好地隔离了锁的使用者与锁的实现者所需关注的领域。
  ```

  

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

  

