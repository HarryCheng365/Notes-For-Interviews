# 多线程与并发

首先一直很疑问的一点：volatile和synchronized

### Java中的并发

### 内存模型的相关概念

```
其实并发的问题最早在CPU设计的时候就出现了
对于多个线程，缓存内有共享变量，CPU的写操作也会导致不一致性出现，比如脏读
因此，当时对CPU的解决办法就是
1.加锁，像java一样加锁，访问的时候其他线程都阻塞或者循环获取，然后就是锁优化技术，锁粗化，自旋锁，锁升级等等。然后就是，通过在总线加锁的方式
2.缓存一致协议，最出名的比如Intel的MESI协议
```

在多线程编程中，线程安全问题是一个最为关键的问题，其核心概念就在于正确性，即当多个线程访问某一共享、可变数据时，始终都不会导致数据结构的破坏和其他Exception。

```
所有的并发模式在解决这个问题的时候（原来的生产者消费者模型不知道有什么关系，还有防止死锁的知识）
采用的方案都是序列化访问临界资源
在 Java 中，提供了两种方式来实现同步互斥访问：synchronized 和 Lock。本文针对 synchronized 内置锁 详细讨论了其在 Java 并发 中的应用，包括它的具体使用场景（同步方法、同步代码块、实例对象锁 
```

### 线程安全问题

```
在单线程中不会出现线程安全问题，而在多线程编程中，有可能会出现同时访问同一个 共享、可变资源 的情况，这种资源可以是：一个变量、一个对象、一个文件等。特别注意两点，
共享：不同线程都可访问，且可以同时访问
可变：该资源在生命周期内可以被修改
由于每个线程执行的过程是不可控的，所以需要采用同步机制来协同对对象可变状态的访问。

就算已经实现了同步操作，但是开多个线程还是有可能出脏读的问题

不过，当多个线程执行一个方法时，该方法内部的局部变量并不是临界资源，因为这些局部变量是在每个线程的私有栈中，因此不具有共享性，不会导致线程安全问题。
```

##### 线程不安全问题的由来

![](./assets/java%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.png)

**这样也就引出了线程操作中变量一致性的问题**：堆中某个对象的共享变量值为1，线程A和线程B在读取的时候都拷贝了一个副本，A线程在将本地副本中的1进行+1操作修改为2后，将其重新写会堆中，但此时B线程也进行了同样的操作，其写回主存中的变量值也为2，这就与预期的3发生了不一致情况。

//其实就是变量 共享，可变 两个问题，需要互斥同步访问

##### 如何确保线程安全？

```
在Java中有很多方法来保证线程安全 如
1.通过加锁（Lock/Synchronized）保证对临界资源的互斥访问
2.使用volatile关键字，轻量级同步机制，但不保证原子性？？
3.使用不变类和线程安全类（原子类，并发容器（比如HashTable），同步容器）等
```

##### 解决线程安全问题的思想

```
什么叫序列化访问临界资源：
即在同一时刻，只能有一个线程访问临界资源，也称作 同步互斥访问。
```

##### 如何停止一个线程

```
1. 使用volatile变量终止正常运行的线程 + 抛异常法/Return法
2. 组合使用interrupt方法与interruptted/isinterrupted方法终止正在运行的线程 + 抛异常法/Return法
3. 使用interrupt方法终止 正在阻塞中的 线程 阻塞就是  wait();
```

##### 何为线程安全的类  ✅

```
在线程安全性的定义中，最核心的概念就是 正确性。当多个线程访问某个类时，
不管运行时环境采用何种调度方式或者这些线程将如何交替执行，//无论
并且在主调代码中不需要任何额外的同步或协同，//不需要任何同步或者协同
这个类都能表现出正确的行为，那么这个类就是线程安全的。
```

##### 线程通信方法 ✅

```
wait(); notify();notifyAll();定义在Object类中 
Wait-notify机制是在获取对象锁的前提下不同线程间的通信机制。在Java中，任意对象都可以当作锁来使用，由于锁对象的任意性，所以这些通信方法需要被定义在Object类里。
```

### 并发三准则 ✅

```
1.异常不会导致死锁现象：当线程出现异常且没有捕获处理时，JVM会自动释放当前线程占用的锁，因此不会由于异常导致出现死锁现象，同时还会释放CPU；
2.锁的是对象而非其引用
3.有Wait必须有Notify
```

### 并发编程中的三个性质

```
1.原子性：即一个操作或者多个操作，要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行
2.可见性：多个线程访问同一个变量，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值
3.有序性：即程序执行的顺序按照代码的先后顺序执行

这个问题的产生是，编译器会对代码进行优化，一些数据之间没有相互依赖关系的语句可能会被重排序，但是在单线程环境下，重排序后的结果一定是和原执行语句一致，是不变的。
但是在多线程环境下这一点就很难被保证了，可能会影响结果的正确性。
```

##### 原子性

```
Java内存模型只能保证基本的读取和复制是原子性操作，如果要实现更大范围操作的原子性，就需要lock和synchronized来实现
```

##### 可见性

```
可见性，java提供了volatile关键字来保证可见性，当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到贮存，当有其他线程需要读取的时候，它会去内存中读取新的值，而普通共享变量被修改后，什么时候写入主存是不确定的，当其他线程去读取时，此时内存中可能还是原来的旧值，因此无法保证可见性。

而加了volatile就不一样了
首先它会在线程更改后，立刻将更改写入主存
另外它会使其他线程对同一变量的缓存无效，因此需要其他线程重新去主存获取这个变量。

CAS 为什么叫自旋volatile变量，因此自选就是一直循环尝试写，循环判断内存中的值和线程中的copy值是否一致。 所以说CAS是一种乐观并发策略。


```

##### 有序性

```
另外 java内存模型 具备一些先天的有序性，即不需要任何手段就能够得到保证的有序性，
这个也称为happens-beforce原则，如果两个操作的次序，无法从happens-beforce中推导出来，那么他们就不能保证它们的有序性。
```

##### 补充：Volatile关键字在Java中的作用 

```
什么叫指令重排序？
volatile的特殊规则保证了新值能立即同步到主内存，以及每次使用前立即从主内存刷新，保证了内存的可见性
也禁止指令重排序，此外，synchronized关键字也可以保证内存可见性

指令重排序问题在并发环境下会导致线程安全问题，volatile关键字通过禁止指令重排序来避免这一问题。而对于Synchronized关键字，其所控制范围内的程序在执行时独占的，指令重排序问题不会对其产生任何影响，因此无论如何，其都可以保证最终的正确性。
```



# Java实现线程的方式

### 继承Thread类

```java
public class MyThread extends Thread{
    public void run(){sout("通过继承thread类实现多线程");}
    
    public static void main(String args[]){
        MyThread thread1 = new MyThread();
        MyThread thread2 = new MyThread();

		thread1.start();
        thread2.start();
    }
}
```

### 实现Runnable接口

```java
public class MyRunnable implements Runnable{
    @Override
    public void run(){sout("通过实现Runnable接口实现多线程");}
    
    public static void main(String args[]){
        MyRunnable myRunnable1 = new MyRunnable();
        MyRunnable myRunnable2 = new MyRunnable();
        Thread thread1 = new Thread(myRunnable1);
        Thread thread2 = new Thread(myRunnable2);
		thread1.start();
		thread2.start();
    }
}
```

​	实现Runnable接口与继承Thread类本质上都是实现或重写了run()方法，再由Thread.start()方法开辟一个新线程。

### **带返回值的实现方式**

​	我们发现上面提到的不管是继承Thread类还是实现Runnable接口，发现有两个问题，第一个是无法抛出更多的异常，第二个是线程执行完毕之后并无法获得线程的返回值。那么下面的这种实现方式就可以完成我们的需求。这种方式的实现就是我们后面要详细介绍的Future模式，只是在jdk5的时候，官方给我们提供了可用的API，我们可以直接使用。但是使用这种方式创建线程比上面两种方式要复杂一些，步骤如下：

1. 创建一个类实现Callable接口，实现call方法。这个接口类似于Runnable接口，但比Runnable接口更加强大，增加了异常和返回值。
2. 创建一个FutureTask，指定Callable对象，做为线程任务。
3. 创建线程，指定线程任务。
4. 启动线程



​	**注意将上述过程与实现Runnable接口对比，两者十分相似！**

```java
public static void main(String args[]){
    Callable<Integer> callable = new Callable<>(){
    	@Override
        public Integer call(){
            System.out.println("thread start .. ");
			return 1;
        }
    };
    
    FutureTask<Integer> ft = new FutureTask<>(callable);//但是runnable接口不需要指定线程任务
    
    Thread t = new Thread(ft);
    t.start();
}
```

### 通过线程池实现

​	线程池的详细解析见线程池章节，此处只给出实例代码：

```java
public static void main(String args[]){
    ExecutorService pool = Executors.newFixedThreadPool(10);
    while(true){
        pool.execute(new Runnable(){
            @Override
            public void run(){
                System.out.println(Thread.currentThread().getName() + " is running ..");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
    }
}
```

```java
//输出
pool-1-thread-4 is running ..
pool-1-thread-1 is running ..
pool-1-thread-6 is running ..
pool-1-thread-2 is running ..
pool-1-thread-8 is running ..
pool-1-thread-3 is running ..
pool-1-thread-5 is running ..
pool-1-thread-9 is running ..
pool-1-thread-10 is running ..
pool-1-thread-7 is running ..
```

### **额外补充：通过线程池实现有返回值的线程**：

​	**注意是通过Future对象来接收 submit() 方法的返回值。**

```java
public static void main(String args[])  throws ExecutionException, InterruptedException {
    ExecutorService pool = Executors.newFixedThreadPool(10);
    
    Future<Integer> result1 = pool.submit(new Callable<Integer>() {
        @Override
        public Integer call() throws InterruptedException {
            System.out.println(Thread.currentThread().getName() + " is running ..");
            Thread.sleep(1000);
            System.out.println(Thread.currentThread().getName() + " wake up ..");
            return 1;
        }
    });

    Future<Integer> result2 = pool.submit(new Callable<Integer>() {
        @Override
        public Integer call() throws InterruptedException {
            System.out.println(Thread.currentThread().getName() + " is running ..");
            Thread.sleep(200);
            System.out.println(Thread.currentThread().getName() + " wake up ..");
            return 2;
        }
    });

    int resultValue1 = result1.get();
    System.out.println("线程 1 获得返回值："+resultValue1);
    int resultValue2 = result2.get();
    System.out.println("线程 2 获得返回值："+resultValue2);
}
```

```java
//输出
pool-1-thread-1 is running ..
pool-1-thread-2 is running ..
pool-1-thread-2 wake up ..
pool-1-thread-1 wake up ..
线程 1 获得返回值：1
线程 2 获得返回值：2
```

# Thread类解析

Thread类是java下实现多线程的核心类。

### start()与run()

- start()方法是Thread类中的方法，核心是其中调用了private native void start0()方法，该方法是本地方法，底层有jvm实现。最后由jvm创建新的线程并调用run()方法，实现真正的创建线程操作。其调用结构图如下：

  ![](./assets/start%20%E4%B8%8Erun%E5%8C%BA%E5%88%AB.png)

- run()方法是Runnable接口中的唯一方法，Thread类实现Runnable接口。使用该方法有两点注意：

  - 如果直接人为调用，则没有经过jvm创建新线程，与普通方法无异。
  - 如果通过start()方法来启动run()方法，需要将run()方法重写，否则最后jvm调用run()方法什么也不会发生。

start()与run()方法的代码测试结果如下：

```java
	private void startAndRun0(){
        System.out.println("current thread : "+Thread.currentThread().getName());
    }

    public void startAndRun(){
        System.out.println("main thread start : "+Thread.currentThread().getName());
        Thread thread = new Thread(){
            public void run(){
                startAndRun0();
            }
        };

        //以下是测试start()和run()
        System.out.println("run()");
        thread.run();

        System.out.println("start()");
        thread.start();
    }

	@Test
    public void startAndRunTest(){
        ThreadTest threadTest = new ThreadTest();
        threadTest.startAndRun();
    }
```

运行结果如下：

```java
main thread start : main
run()
current thread : main		//直接调用run()方法没有创建新的线程
start()
current thread : Thread-0	//调用start()方法创建了新的线程，调用start，重写了run方法，才会创建新线程，新线程会自动调用run()方法
```

### Thread和Runnable

​	Runnable为接口，其中只包含一个run()方法，Thread是实现Runable接口的类，run()方法支持多线程。

​	另外，在实际业务开发中，推荐多使用Runable接口，将业务封装在run()方法中，使得代码结构更清晰。示例代码如下：

```java
/**
 * 测试Thread与Runnable的关系
 */
public class MyRunnable implements Runnable {
    private String name;

    public MyRunnable(String name){this.name = name;}

    @Override
    public void run() {
        for(int i = 0; i < 10; i++)
            System.out.println("Thread start : "+this.name + ", i="+i);
    }//继承Runnable接口 重写run方法 调用start()

    public static void main(String[] args) {
        MyRunnable mr1 = new MyRunnable("thread1");
        MyRunnable mr2 = new MyRunnable("thread2");
        Thread thread1 = new Thread(mr1);
        Thread thread2 = new Thread(mr2);
        thread1.start();
        thread2.start();
    }
}

// output
Thread start : thread1, i=0
Thread start : thread2, i=0
Thread start : thread1, i=1
Thread start : thread2, i=1
...
```

```java
/**
 * 测试Thread与Runnable的关系
 */
public class MyThread extends Thread {
    private String name;

    public MyThread(String name){this.name = name;}

    @Override
    public void run() {
        for(int i = 0; i < 10; i++){
            System.out.println("Thread start : "+this.name + ", i="+i);
        }
    }

    public static void main(String[] args) {
        MyThread mt1 = new MyThread("Thread1");
        MyThread mt2 = new MyThread("Thread2");
        mt1.start();
        mt2.start();
    }
}

// output
Thread start : Thread2, i=0
Thread start : Thread2, i=1
Thread start : Thread2, i=2
Thread start : Thread1, i=0
...
```

### 如何给run()函数传参

​	三种方式：

##### 构造函数传参

​	见ThreadAndRunnable部分的代码

##### 成员变量传参 

直接用类中的成员变量做run的参数，run本身应该是无参方法

##### 回调函数传参

### 如何实现处理线程返回值

​	主要有三种实现方式：

##### 主线程等待法

​	即在主线程中设置等待条件，等待子线程执行完后再继续向下走，代码如下：

```java
public class SubThreadReturnValue implements Runnable {
    private int i;

    @Override
    public void run() {
        try {
            for(i=0;i<2;i++)
               Thread.currentThread().sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

     // 主线程等待法
    public static void getSubThreadReturnValue1() throws InterruptedException {
        SubThreadReturnValue s = new SubThreadReturnValue();
        Thread t = new Thread(s);
        t.start();
        //System.out.println(s.value); //0
        while(s.i!=2)
            Thread.currentThread().sleep(100);
        System.out.println(s.i);		//2
    }
}
```

​	该方法的缺点是每次需要手动判断子线程是否跑完，如果等待返回的变量较多，则代码会十分臃肿，并且循环多久不确定，无法做到精准的控制（子线程结束了可能主线程还在sleep）。

##### join()阻塞法

​	使用Thread类中的**join()**方法阻塞当前线程以等待子线程处理完毕。该方法的实现简单并且可以做到较为精准的控制（子线程一结束就可以执行主线程），但依旧无法做到十分精准的控制，如想让 i 在等于1的时候停止。

```java
    /**
     * 使用join()方法
     * @throws InterruptedException
     */
    public static void getSubThreadReturnValue2() throws InterruptedException {
        SubThreadReturnValue s = new SubThreadReturnValue();
        Thread t = new Thread(s);
        t.start();
        
        t.join();
        
        System.out.println(s.i);	//2
    }
```

##### 通过Callable接口

​	通过Callable接口实现：Callable接口中只有一个 "V call()" 方法，通过该接口有两种实现方式：FutureTask和线程池。

- FutureTask：具体要注意的细节见下面的代码

  ```java
  public class MyCallable implements Callable<String> {
      @Override
      public String call() throws InterruptedException {
          System.out.println("ready to work");
          Thread.currentThread().sleep(1000);
          String value = "mycallable test";
          System.out.println("task done");
          return value;
      }
  }
  
  public class FutureTaskTest {
      public static void main(String[] args) throws Exception {
          // FutureTask的构造函数可以接收Callable接口下实现类的实例
          FutureTask<String> futureTask = new FutureTask<>(new MyCallable());
          // FutureTask实现RunnableFuture 接口，RunnableFuture接口继承Runnable和Future
          // 所以此处Thread的构造函数可以接收 FutureTask 的实例
          new Thread(futureTask).start();
          // futureTask.isDone方法用来判断 Callable接口实现类中的call方法是否已经执行完成
          if(!futureTask.isDone())
              System.out.println("task has not finished, please wait");
          //futureTask.get()方法阻塞当前调用的线程，直到Callable实现类的call方法执行完毕
          System.out.println("task return : "+ futureTask.get());
      }
  }
  
  //output
  task has not finished, please wait
  ready to work
  task done
  task return : mycallable test
  ```

- 线程池

  线程池的在此体现的好处是可以统一管理，后续还会详细介绍线程池。

- ```java
  public class ThreadPoolTest{
      public static void main(String[] args){
          ExcutorService newCachedThreadPool = Excutor.newCachedThreadPool();
          Future<String> future = newCachedThreadPool.submit(new MyCallable());
          if(!future.isDone())
              System.out.println("task has not finished, please wait");
          try{
              System.out.println("task return : "+ future.get());
          } catch (Exception e){
              // ...
          } finally{
              newCachedThreadPool.shutdown();
          }
      }
  }
  ```

### 线程的状态

​	Thread类下有一个枚举类型state，其中定义了六种线程的状态：

##### 1. 新建(NEW)

​	已经创建的尚未启动的线程处于这种状态，即未调用start()方法。

##### 2 运行(RUNNABLE)

​	运行状态(RUNNABLE)包含运行态(Running)和就绪态(Ready)两种状态，处于Running状态的线程位于可运行线程之中，分配到CPU时间后便开始运行。处于Ready状态的线程位于线程池中，其在获得cpu使用权后就变成了Running状态的线程。

##### 3. 无限等待(WAITING)

​	处于该状态的线程不会被分配cpu时间，需要显式唤醒( Object.notify() / Object.notifyAll() )。以下情况下线程会处于无限期等待(Waiting)状态下：

- 没有设置timeout的Object.wait()方法
- 没有设置timeout的Thread.join()方法
- LockSupport.park()方法

##### 4. 限期等待( TIMED-WAITING )

​	在一定时间后会被系统自动唤醒，以下情况下线程处于限期等待状态：

- Thread.sleep()
- 设置timeout的Object.wait()方法
- 设置timeout的Thread.join()方法
- LockSupport.parkNanos()
- LockSupport.parkUntil()

##### 5. 阻塞(BLOCKED)

​	线程处于阻塞状态停止运行，等待获取排它锁。比如当某块代码有sychronized关键字修饰时，一个线程进入后，别的线程就无法进入，处于阻塞状态，直到已经进入的线程释放资源。

##### 6. 终结(TEMINATED)

​	线程已经结束状态，即run()方法已经运行完。处于终结状态的线程不可以再使用，否则会抛出IllegalThreadStateException异常。

##### 状态转换图

![]()

# Java锁的分类及优化

### Java锁的分类

```
其实对于 Java 锁的分类没有严格意义的规则，
我们常说的分类一般都是依据锁的特性、锁的设计、锁的状态等进行归纳整理的
```

##### 公平锁、非公平锁

```
1.公平锁、非公平锁：
公平锁是指多个线程按照锁的顺序来获取锁，非公平锁就是没有顺序完全随机，不能先来先得，没有优先级
所以非公平锁会造成优先级反转或者饥饿现象
比如：sychronized 内置锁，对象锁就是非公平锁，ReentrantLock(使用CAS和AQS实现)通过构造函数可以决定是公平锁还是非公平锁，默认构造是公平锁，非公平锁的吞吐量性能比公平锁好。
```

##### 可重入锁

```
2.可重入锁：
又名递归锁，指在同一个线程在外层方法获取锁的时候在进入内层方法的时候会自动获取锁，就是获得锁的方法，是层层调用的，其中会call其他方法。这个没什么好说的，应该可重入
所以synchronized和ReentrantLock都是可重入锁，可重入锁可以在一定程度上避免死锁。
```

##### 独享锁、共享锁

```
3.独享锁，共享锁：独享锁是指该锁一次只能被一个线程持有，共享锁指该锁可以被多个线程持有；
synchronized和ReentrantLock都是独享锁，ReadWtireLock的读锁是共享锁，写锁是独占锁
ReentrantLock的独享锁和共享锁也是通过AQS来实现的
```

##### **互斥锁、读写锁**

```
4.互斥锁，读写锁：其实就是独享锁、共享锁的具体说法，互斥锁的实质就是ReentrantLock，读写锁实质就是ReadWriteLock
```

##### 乐观锁、悲观锁(数据库中也有相关概念)

```
5.乐观锁，悲观锁：这个分类不是具体锁的分类，而是看待并发同步的角度；
乐观锁其实就是，不加锁没事，我重复写就好了
悲观锁就是，我不加锁一定有事，并发操作的时候 一定会发生脏读，所以就...

悲观锁认为对于同一个数据的并发操作，一定是会发生修改的，哪怕没有修改，也会认为修改。
因此对于同一个数据的并发操作，悲观锁采取加锁的形式。
悲观的认为，不加锁的并发操作一定会出问题。
乐观锁则认为对于同一个数据的并发操作，是不会发生修改的。
在更新数据的时候，会采用尝试更新，不断重新的方式更新数据。//就是多次尝试，和TLAB的方法一样 乐观锁
乐观的认为，不加锁的并发操作是没有事情的。

从上面的描述我们可以看出，悲观锁适合写操作非常多的场景，
乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。

悲观锁在Java中的使用，就是利用各种锁。
乐观锁在Java中的使用，是
无锁编程，常常采用的是CAS算法，典型的例子就是原子类，通过CAS自旋实现原子操作的更新。
```

##### 分段锁(ConcurrentHashMap)

```
6.分段锁，其实就是为了细化粒度
分段锁其实是一种锁的设计，并不是具体的一种锁，对于ConcurrentHashMap而言，其并发的实现就是通过分段锁的形式来实现高效的并发操作。

我们以ConcurrentHashMap来说一下分段锁的含义以及设计思想，ConcurrentHashMap中的分段锁称为Segment，它即类似于HashMap（JDK7与JDK8中HashMap的实现）的结构，即内部拥有一个Entry数组，数组中的每个元素又是一个链表；
同时又是一个ReentrantLock（Segment继承了ReentrantLock)。

1.当需要put元素的时候，并不是对整个hashmap进行加锁，这句是重点，所以可以实现并发添加提高效率，
仅仅对分段 对桶进行加锁，细化了锁的粒度。
2.而是先通过hashcode来知道他要放在那一个分段中，然后对这个分段进行加锁，所以当多线程put的时候，只要不是放在一个分段中，就实现了真正的并行的插入。
3.但是，在统计size的时候，可就是获取hashmap全局信息的时候，就需要获取所有的分段锁才能统计。
分段锁的设计目的是细化锁的粒度，当操作不需要更新整个数组的时候，就仅仅针对数组中的一项进行加锁操作。
```

#### 锁优化技术

##### 自旋锁 自适应自旋锁

```
锁优化技术的目的在于线程之间更高效的共享数据，解决竞争问题，更好提高程序执行效率。
1.自旋锁(上下文切换代价大)：互斥锁 -> 阻塞 –> 释放CPU，线程上下文切换代价较大 + 共享变量的锁定时间较短 == 让线程通过自旋等一会儿，
如果锁占用的时间很短，自旋等待的效果就很好；如果锁占用的时间较长，自旋等待反而会白白浪费CPU资源，会带来性能上的浪费。

自旋次数的默认值是10次，用户可以使用参数preBlockSpin来更改。但不同锁的等待时间可能不一致，统一设置PreBlockSpin参数较为困难，JDK1.6版本引入了自适应自旋锁，自适应的情况下自旋的时间不固定，主要由以下两个元素决定：
1.前一次在同一个锁上的自旋时间
2.锁的拥有者的状态

如果在同一个锁对象上，自旋等待刚刚成功获得过锁，并且持有锁的线程正在运行中，那么虚拟机就会认为这次自旋也很有可能再次成功，进而它将允许自选等待持续相对更长的时间。
```

比如偏向锁也是这个道理，根据锁的拥有者的状态，还有LRU也是，这是一种设计思想

##### 锁粗化

```
2.锁粗化(一个大锁优于若干小锁)：一系列连续操作对同一对象的反复频繁加锁/解锁会导致不必要的性能损耗，建议粗化锁 
一般而言，同步范围越小越好，这样便于其他线程尽快拿到锁，但仍然存在特例。
如果虚拟机侦测到有这样一串操作，将会把加锁同步的范围扩展到整个操作序列的外部。
```

##### 偏向锁

```
3.偏向锁(有锁但当前情形不存在竞争)：消除数据在无竞争情况下的同步原语，提高带有同步但无竞争的程序性能。
```

##### 锁消除

```java
4.锁消除(有锁但不存在竞争，锁多余)：JVM编译优化，将不存在数据竞争的锁消除
锁可以升级但不能降级，意味着偏向锁升级成轻量级锁后无法降为偏向锁，这种升级无法降级的策略目的就是为了提高获得锁和释放锁的效率。
public String concatString(String s1, String s2){
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2)
    return sb.toString();
}
以上代码中，StringBuffer.append()方法实际是一个同步方法(synchronized关键字修饰)，但虚拟机观察变量sb发现它的作用域永远都被限制在concatString()方法的内部，也就是说，sb的所有引用永远不会被非当前线程的线程访问，不存在线程安全问题。在及时编译之后，这段代码就会忽略掉所有的同步而直接执行了。
```



# Synchronized的锁转换

### Synchronized的四种状态

这四种锁是指锁的状态，并且是针对Synchronized。在Java 5通过引入锁升级的机制来实现高效Synchronized。java 1.6以后引入的

##### 无锁

即共享数据没有被加锁，此时的共享数据没有被任何一条线程占用

##### 偏向锁

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。

##### 轻量级锁

轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。（自旋锁的由来，这样虽然会占用系统资源

##### 重量级锁

重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。

自旋，自旋久了就变成了重量级锁了。
重量级锁会让其他申请的线程进入阻塞，性能降低。

### 自旋锁

```
自旋锁。就是一个线程获取锁后，会让其他尝试获取锁的线程，会一直尝试循环获取，而不是阻塞，这样会减少线程上下文切换的时间，但是会增加CPU的负担

自旋锁其实是相对于互斥锁的概念，互斥锁线程会进入Waiting状态和Runnable状态，涉及上下文切换，自旋锁的线程一直是runnable状态的，一直在循环检测锁的标记位，机制不可重复，但是自旋锁加锁全程消耗cpu，起始开销低于互斥锁，但是随着持锁时间增长，而开销增加



```

### 可中断锁

synchronized 是不可中断的，Lock 是可中断的，这里的可中断建立在阻塞等待中断，运行中是无法中断的。

阻塞了怎么中断，就很奇怪

# Synchronized的核心

synchronized锁的都是对象，不是代码。恰当合理地给一个对象上锁，是解决线程安全问题的关键。

有锁互斥，所以可以顺序执行，顺序执行其实就是串行执行

### Synchronized的实现原理

```
这三种锁的状态是通过对象监视器在对象头中的字段来表明的。//对象监视器？Monitor 因此ReentrantLock不可用，MonitorEnter 和 MonitorExit两个编译器字段

Java中每个对象都以用来实现一个同步锁
线程在进入同步代码块之前会自动获取锁，并且在退出同步代码块时自动释放锁，无论是通过正常路径退出，还是通过代码块中抛出异常退出。获得内置锁的唯一途径就是进入由这个锁保护的同步方法或代码块。
synchronized是JVM层面的，从JVM规范里可以看到synchronized在JVM里的实现原理，JVM基于进入和退出Monitor对象来实现方法同步和代码块同步，但两者的实现细节不一样

原理，synchronized的原理是monitorenter和monitorexit
monitorenter 指令是在编译后插入到同步代码块的开始位置， 
而 monitorexit 是插入到方法结束处和异常 处， 
JVM 要保证每个 monitorenter 必须有对应的 monitorexit 与之配对。 
任何对象都有一个monitor 与之关联， 当且一个 monitor 被持有后， 它将处于锁定状态。 
（正对应了synchronized是jvm层的实现，是java内置的规范
线程执行到 monitorenter 指令时， 
将会尝试获取对象所对应的 monitor 的所有权， 即尝试获得对象的锁。
```

##### 什么事Java对象头

##### Monitor监视器是什么

Java对象头和Monitor是实现synchronized基础。

​	HotSpot对象在内存中的布局分为三部分：对象头、实例数据、对其填充。

- 对象头

  对象头的结构：对象的内存结构详细解析见 JVM/对象内存部分 。

  MarkWord 和类型指针，当然可能不只这么简单

  MarkWord又包含hashCode，分代年龄，锁类型，锁标志位

  # 下面是MarkWord的细节。

  ![](assets/%E5%AF%B9%E8%B1%A1%E5%A4%B4%E7%9A%84%E7%BB%93%E6%9E%84.png)

  ![markword](/Users/Haoyu/Documents/Notes-For-Interviews/技术资料/assets/markword.png)

- Monitor：管程，每个对象天生自带的锁，Monitor对象存在于每个对象的对象头中。

  ![](assets/Monitor%E9%94%81%E7%9A%84%E7%AB%9E%E4%BA%89%E3%80%81%E8%8E%B7%E5%8F%96%E4%B8%8E%E9%87%8A%E6%94%BE.png)

- 详细流程

  ​	在执行monitorenter指令时，首先要尝试获取对象的锁，如果这个对象没被锁定，或者当前线程已经拥有了那个对象的锁，把锁的计数器加一，相应的，在执行monitorexit指令时会将锁计数器减一。当计数器为零时，所就会被释放。如果获取对象锁失败，那当前线程就要等待，知道对象锁被另外一个线程释放为止。

  **注意点**：

  1. synchronized同步块对同一线程来说是可重入的，不会出现把自己锁死的情况。
  2. 同步块在已进入的线程执行完之前，会阻塞后面其它线程的进入。
  3. **synchronized是一个重量级锁**，在jdk1.6之前，java的线程是银蛇到操作系统的原生线程之上的，如果要唤醒或者阻塞一个线程，都需要操作系统来帮忙完成，这就需要从用户态转到核心态中，因此状态转换需要耗费很多的处理器时间。jdk1.6版本对锁做了很多优化措施，使得synchronized的效率提高。

### Synchronized用法  对象锁

##### 同步代码块

```java
是一个修饰符，可以用来修饰方法或者代码块，又叫同步块
synchronized获取对象锁，它修饰的对象有
1.修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围就是调用这个代码块的对象。
例子()也可以写要锁的 类的实例对象||this
	synchronized(this){};
	SyncThread syncThread = new SyncThread();
	Thread thread1 = new Thread(syncThread,"1");
	Thread thread2 = new Thread(syncThread,"2");
```

##### 修饰一个非静态的方法

```
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
	


```

##### 修饰静态方法和类

```java
3.修饰一个静态的方法，其作用范围是整个静态方法，作用的对象是这个类的所有对象（因为是类中的静态方法）
/**
     * 通过同步代码块(synchronized(类.class))，获得类锁
     */
    private void syncClassBlock1(){
        synchronized (ClassLockAndObjLock.class){
            try {
                System.out.println(Thread.currentThread().getName() + "_SyncClassBlock1_Start（通过同步代码块(synchronized(类.class))，获得类锁）: "
                        + new SimpleDateFormat("HH:mm:ss").format(new Date()));
                Thread.sleep(1000);
                System.out.println(Thread.currentThread().getName() + "_SyncClassBlock1_End（通过同步代码块(synchronized(类.class))，获得类锁）: "
                        + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    /**
     * 通过同步静态方法，获得类锁
     */
    private static synchronized void syncClassMethod1(){
        try {
            System.out.println(Thread.currentThread().getName() + "_SyncClassMethod1_Start（通过同步静态方法，获得类锁）: "
                    + new SimpleDateFormat("HH:mm:ss").format(new Date()));
            Thread.sleep(1000);
            System.out.println(Thread.currentThread().getName() + "_SyncClassMethod1_End（通过同步静态方法，获得类锁）: "
                    + new SimpleDateFormat("HH:mm:ss").format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
4.修饰一个类，其作用范围是synchronized后面括号括起来的部分，作用的对象是这个类的所有对象。
```



### 修饰方法时要注意

```
就是对于synchronized 修饰方法的时候要注意
1.当一个线程正在访问一个对象的synchronized方法，那么其他线程不能访问该对象的其他synchronized方法，这个原因很简单，因为调用synchronized方法 还是要获取对象锁，当一个线程获取了该对象的锁之后，其他线程无法获取该对象的锁，所以无法访问该对象的其他synchronized方法。
2.当一个线程正在访问一个对象的 synchronized 方法，那么其他线程能访问该对象的非 synchronized 方法。这个原因很简单，访问非 synchronized 方法不需要获得该对象的锁，假如一个方法没用 synchronized 关键字修饰，说明它不会使用到临界资源，那么其他线程是可以访问这个方法的
3.如果一个线程 A 需要访问对象 object1 的 synchronized 方法 fun1，另外一个线程 B 需要访问对象 object2 的 synchronized 方法 fun1，即使 object1 和 object2 是同一类型），也不会产生线程安全问题，因为他们访问的是不同的对象，所以不存在互斥问题。
访问不同对象，即时同一个类的对象，也不会出现问题，因为不是同一个对象锁
```

### 代码块VS非静态方法

```
从上面代码可以看出，synchronized代码块 比 synchronized方法 的粒度更细一些，使用起来也灵活得多。因为也许一个方法中只有一部分代码只需要同步，
如果此时对整个方法用synchronized进行同步，会影响程序执行效率。而使用synchronized代码块就可以避免这个问题，synchronized代码块可以实现只对需要同步的地方进行同步。
```

### 类锁VS对象锁

```
synchronized修饰的方法在执行的时候就会获取该对象的锁，但是对于静态方法，没有实例对象，就要获取该class的锁
特别地，每个类也会有一个锁，静态的 synchronized方法 就是以Class对象作为锁。另外，它可以用来控制对 static 数据成员 （static 数据成员不专属于任何一个对象，是类成员） 的并发访问。
同时 类锁和对象锁是不互斥的，正如不同对象的对象锁之间不互斥
```

# Java对象组成详解

### 简介

​	该部分的补充是为了解释synchronized四种锁的底层实现原理。

​	Java对象保存在内存中时，由以下三部分组成：

1. 对象头
2. 实例数据
3. 对齐填充字节

### 对象头

java的对象头由以下三部分组成：

1. Mark Word
2. 指向类的指针
3. 数组长度（只有数组对象才有）

##### Mark word

​	Mark Word记录了对象和锁有关的信息，当这个对象被synchronized关键字当成同步锁时，围绕这个锁的一系列操作都和Mark Word有关。Mark Word在32位JVM中的长度是32bit，在64位JVM中长度是64bit。

​	Mark Word在不同的锁状态下存储的内容不同，在32位JVM中是这么存的：

| 锁状态   | 23bit                       | 2bit  | 4bit     | 1bit（是否偏向锁） | 2bit（锁标识位） |
| -------- | --------------------------- | ----- | -------- | ------------------ | ---------------- |
| 无锁     | 对象的HashCode(25bit)       |       | 分代年龄 | 0                  | 01               |
| 偏向锁   | 线程ID                      | Epoch | 分代年龄 | 1                  | 01               |
| 轻量级锁 | 指向栈中锁记录的指针(30bit) |       |          |                    | 00               |
| 重量级锁 | 指向重量级锁的指针(30bit)   |       |          |                    | 10               |
| GC标记   | 空                          |       |          |                    | 11               |

​	其中无锁和偏向锁的锁标志位都是01，只是在前面的1bit区分了这是无锁状态还是偏向锁状态。JDK1.6以后的版本在处理同步锁时存在锁升级的概念，JVM对于同步锁的处理是从偏向锁开始的，随着竞争越来越激烈，处理方式从偏向锁升级到轻量级锁，最终升级到重量级锁。

​	**JVM一般是这样使用锁和Mark Word的：**

1. 当对象没有被当成锁时，这就是一个普通对象，markWord记录的是对象的HashCode，锁标识位为01，偏向锁标识位为0。
2. 当对象被当做同步锁并且有一个线程A抢到锁时，锁标识位还是01，偏向锁标识位改成1，前23bit改为抢到锁的线程的id，标识进入偏向锁状态。
3. 当线程A再次尝试获得锁时，JVM发现同步锁对象的标志位为01，偏向锁标识位为1，MarkWord中记录的线程id就是线程A的线程id，表示线程A已经获得了这个同步锁，可进入同步代码块。
4. <font color='red'>当线程B尝试获得这个锁时，JVM发现同步锁处于偏向状态，但是markWord中的id不是线程 B，那么线程 B 会先用CAS尝试获得锁，这里的获得锁操作是有可能成功的，因为线程A一般不会自动释放偏向锁。</font>如果抢锁成功，那就把偏向锁的markword中的线程id改成b线程的id，表示b线程获得了这个偏向锁，可以执行同步代码块。如果抢锁失败，则继续执行步骤5。
5. 偏向锁抢锁失败，代表当前锁存在一定竞争，偏向锁将升级为轻量级锁。JVM会在当前线程的线程栈中开辟中一块单独的空间，里面保存指向对象锁markword的指针，同时在对象锁MarkWord中保存指向这片空间的指针。上面两个保存操作都是CAS操作，如果保存成功，代表线程抢到了同步锁，就将markword中的锁标识位改成00，可以执行同步代码。如果竞争失败，代表竞争太激烈，继续执行步骤6。
6. 轻量级锁抢锁失败，JVM会使用自旋锁，自旋锁不是一个锁状态，只是代表不断地重试，尝试抢锁。从jdk1.7开始，自旋锁默认自动开启，自旋次数有JVM决定。如果抢锁成功则执行同步代码，如果失败则继续执行步骤7。
7. 自旋锁重试之后抢锁依然失败，轻量级锁会升级至重量级锁，锁标识位改为10，在这个状态下，未抢到锁的线程都会被阻塞。

##### 指向对象的指针

​	该指针在32位JVM中的长度是32bit，在64位JVM中长度是64bit。java基础->多态部分有结构示意图。

​	Java对象的类数据保存在方法区。

##### 数组长度

​	只有数组对象保存了这部分数据。

​	该数据在32位和64位JVM中长度都是32bit。

### 实例数据

​	对象的实例数据就是在java代码中能看到的属性和他们的值。

### 对其填充字节

​	因为JVM要求java的对象占的内存大小应该是8bit的倍数，所以后面有几个字节用于把对象的大小补齐至8bit的倍数，没有特别的功能。

### 补充

##### 偏向锁的加锁和解锁

​	引入偏向锁是为了在无多线程竞争的情况下获得锁资源的消耗（即使轻量级锁的获取及释放依赖多次CAS原子指令），偏向锁只需要在置换ThreadID的时候依赖一次CAS原子指令（由于一旦出现多线程竞争的情况就必须撤销偏向锁，所以偏向锁的撤销操作的性能损耗必须小于节省下来的CAS原子指令的性能消耗）。**轻量级锁是为了在线程交替执行同步块时提高性能，而偏向锁则是在只有一个线程执行同步块时进一步提高性能。**

- 偏向锁的获取过程

  1. 访问Mark Word中偏向锁的标识是否设置成1，锁标志位是否为01——确认为可偏向状态。
  2. 如果为可偏向状态，则测试线程ID是否指向当前线程，如果是，进入步骤（5），否则进入步骤（3）。
  3. 如果线程ID并未指向当前线程，则通过CAS操作竞争锁。如果竞争成功，则将Mark Word中线程ID设置为当前线程ID，然后执行（5）；如果竞争失败，执行（4）。
  4. 如果CAS获取偏向锁失败，则表示有竞争。当到达全局安全点（safepoint）时获得偏向锁的线程被挂起，偏向锁升级为轻量级锁，然后被阻塞在安全点的线程继续往下执行同步代码。
  5. 执行同步代码。

- 偏向锁的释放过程

  ​	偏向锁的撤销在上述第四步骤中有提到**。**偏向锁只有遇到其他线程尝试竞争偏向锁时，持有偏向锁的线程才会释放锁，线程不会主动去释放偏向锁。偏向锁的撤销，需要等待全局安全点（在这个时间点上没有字节码正在执行），它会首先暂停拥有偏向锁的线程，判断锁对象是否处于被锁定状态，撤销偏向锁后恢复到未锁定（标志位为“01”）或轻量级锁（标志位为“00”）的状态。

  偏向锁的释放和偏向锁的撤销

##### 轻量级锁的加锁和解锁

- 轻量级锁的加锁过程

  1. 在代码进入同步块的时候，如果同步对象锁状态为无锁状态（锁标志位为“01”状态，是否为偏向锁为“0”），虚拟机首先将在当前线程的栈帧中建立一个名为**锁记录**（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝，官方称之为 **Displaced Mark Word（注意此时栈中已经存下了对象的hash、age等重要信息）**。这时候线程堆栈与对象头的状态如下图所示：

     ![](./assets/%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%81.png)

  2. 拷贝对象头中的Mark Word复制到锁记录中。

  3. 拷贝成功后，虚拟机将使用**CAS操作**尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock record里的owner指针指向object mark word。如果更新成功，则执行步骤（4），否则执行步骤（5）。

  4. 如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，即表示此对象处于轻量级锁定状态，这时候线程堆栈与对象头的状态如下图2.2所示。

     ![](./assets/%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%812.png)

  5. 如果这个更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行。否则说明多个线程竞争锁，轻量级锁就要膨胀为重量级锁，锁标志的状态值变为“10”，Mark Word中存储的就是指向重量级锁（互斥量）的指针，后面等待锁的线程也要进入阻塞状态。 而当前线程便尝试使用自旋来获取锁，自旋就是为了不让线程阻塞，而采用循环去获取锁的过程。

- 轻量级锁的解锁过程

  1. 通过CAS操作尝试把线程中复制的Displaced Mark Word对象替换当前的Mark Word。
  2. 如果替换成功，整个同步过程就完成了。
  3. 如果替换失败，说明有其他线程尝试过获取该锁（此时锁已膨胀），那就要在释放锁的同时，唤醒被挂起的线程。

##### 偏向、轻量级、重量级锁转换

![](assets/%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%81%E3%80%81%E9%87%8D%E9%87%8F%E7%BA%A7%E9%94%81%E5%92%8C%E5%81%8F%E5%90%91%E9%94%81%E7%9A%84%E8%BD%AC%E6%8D%A2.png)

# ReentrantLock 

### Synchronized和Lock概述

```
synchronized 是java的关键字 是 java的内置特性，在JVM层面实现了对临界资源的同步互斥访问，但是synchronized粒度有些大，在处理时机问题时有诸多局限性，比如响应中断等。
Lock提供了比synchronized更广泛的锁操作，它能以更优雅的方式处理线程问题。
synchronized和Lock的对比为切入点，对java中Lock框架的枝干部分进行了介绍。
```

### Synchronized的局限性和Lock的优点：

```
对于Synchronized来说：
占有锁的线程执行完了会释放
waiting了会释放比如 在线程内调用wait元素
或者发生异常了会释放，但是

1.在长时间io或者线程内调用sleep的时候不会释放
2.读与写，写与写是互斥的很好理解，但是读与读应该是可以共享的，这里就体现了synchronized锁粒度大的问题，应当设计一种机制来使得多个线程都是读操作的时候，线程之间不会发生冲突。Lock 的 ReentrantReadWriteLock就出现了
3.我们也可以通过Lock的值
```

### Synchronized和Lock的具体区别

```
1.synchronized是Java的关键字，因此是Java的内置特性，是基于JVM层面实现的，
其经过编译之后，会在同步块的前后分别形成 monitorenter 和 monitorexit 两个字节码指令；
而Lock是一个Java接口，是基于JDK层面实现的，通过这个接口可以实现同步访问；

2.采用synchronized方式不需要用户去手动释放锁，当synchronized方法或者synchronized代码块执行完之后，系统会自动让线程释放对锁的占用；而 Lock则必须要用户去手动释放锁 (发生异常时，不会自动释放锁)，如果没有主动释放锁，就有可能导致死锁现象。
3.可中断，响应中断，Lock可以让等待锁的线程响应中断，Lock锁就不会一直傻等下去，线程可以不用一直等待就结束了，而使用synchronized时，等待的线程会一直等待下去，不能够响应终端 
4.立即返回
5.读共享，读写锁
6.可以实现公平锁
7.显式获取和释放，前者无法判断是否获取锁的状态，Lock可以判断是否获取到锁
synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；

而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；一定要在finally中释放锁

```

#### Lock接口与实现关系

![2446D75C-8BDF-4AFC-9F17-47D14BFD483B](/Users/Haoyu/Desktop/2446D75C-8BDF-4AFC-9F17-47D14BFD483B.png)

```
可以看到lock有由lock产生的condition实例接口
```

#### Lock对象

而Lock是一个Java接口(API如下图所示)，是基于JDK层面实现的，通过这个接口可以实现同步访问，

它提供了比synchronized关键字更灵活、更广泛、粒度更细的锁操作，底层是由AQS实现的。

```

```



#### 关于Condition





- 弱引用与强引用？软引用，虚引用？

  ```
  
  ```

  

- Java中的死锁（DeadLock）其实和操作系统中的死锁区别不大✅

  ```
  死锁是指两个以上的线程永远阻塞的情况，这种情况产生至少需要两个以上的线程和两个以上的资源
  多个线程多个资源，占用不释放，循环请求，循环等待就构成了死锁
  
  分析死锁，我们需要查看Java应用程序的线程转储。我们需要找出那些状态为BLOCKED的线程和他们等待的资源。每个资源都有一个唯一的id，用这个id我们可以找出哪些线程已经拥有了它的对象锁。下面列举了一些JDK自带的死锁检测工具：
  Jconsole
  Jstack
  VisualVM
  ```








# 并发编程的实现问题

### 什么是线程池，如何创建一个线程池

```
一个线程池管理了一组工作线程，同时它还包括一个用于放置等待执行的任务的队列。
线程池可以避免线程的频繁创建与销毁，降低资源消耗，提高系统反应速度。
java.util.concurrent.Executors 提供了几个Java.Util.concurrent.Executor接口的实现，用于创建线程池，其主要涉及四个角色

线程池：Executor
工作线程Worker线程，Worker的run()方法执行Job的run()方法
任务Job：Runable 和 Callable
阻塞队列：BlockingQueue，用过这个容器接口了
```

##### 线程池Executor

- Executor及其实现类是用户级的线程调度器，也是对任务执行机制的抽象，其将任务的提交与任务的执行分离开来，核心实现类包括ThreadPoolExecutor(用来执行被提交的任务)和ScheduledThreadPoolExecutor  (可以在给定的延迟后执行任务或者周期性执行任务)。

- Executor的实现继承链条为：(父接口)Executor -> (子接口)ExecutorService -> (实现类)[ ThreadPoolExecutor + ScheduledThreadPoolExecutor ]。

##### 任务Runnable/Callable

- Runnable（run）和Callable（call）都是对任务的抽象，但是Callable可以返回任务执行的结果或者抛出异常

  在Thread板块，有对Callable和Runnable的详细讲解，还有Runnable和Thread的区别

##### 任务执行状态future

- Future是对任务执行状态和结果的抽象，核心实现类是futureTask（所以它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值）

##### 四种常用线程池

```

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

### 线程池的饱和策略

### 线程池调优



### 使用线程池的原因

1. 降低资源消耗：重用存在的线程，减少对象创建、消亡的开销，性能佳。
2. 可有效控制最大并发线程数，提高系统资源利用率，同时可以避免过多的资源竞争，避免阻塞。
3. 提高线程可管理性：提供定时执行、定期执行、单线程、并发数控制等功能。

### 五大线程池

1. Executors.newFixedThreadPool(int nThreads)：指定工作线程数量的线程池。
2. Executors.newCachedThreadPool()：处理短时间工作任务的线程池，其有如下特点：
   - 试图缓存线程并重用，当无缓存线程可用时，就会创建新的工作线程。
   - 如果线程闲置的时间超过阈值（一般60s），则会被终止并移出缓存，这样就保证了在系统长时间闲置的时候，其不会消耗什么资源。
3. Executors.newSingleThreadExecutor()：创建唯一的工作者线程来执行任务，如果线程异常结束，会有另一个线程来取代它。
4. Executors.newSingleThreadScheduledExcutor()与Executors.newSheduledThreadPool(int poolSize)：定时或周期性的工作调度，两者的区别在于应用于单一工作线程和多个工作线程。
5. Executors.newWorkStealingPool()：内部会构建ForkJoinPool()，利用working-stealing算法，并行地处理任务，不保证处理顺序。

### 线程池类图结构

![](./assets/ThreadPool%E7%B1%BB%E5%9B%BE%E7%BB%93%E6%9E%84.png)

### J.U.C三个接口

##### Executor 接口

​	运行新任务的简单接口，将任务提交和任务执行细节解耦。其中只包含 void execute(Runnable command)方法。

```java
// 测试代码如下

//传统方式
Thread t = new Thread();
t.start();

//通过Executor接口
Thread t = new Thread();
ExecutorService.newSingleExecutorService().execute(t);
```

##### ExecutorService 接口

​	ExecutorService扩展了Executor接口，添加了一些管理和生命周期控制相关的方法，提交任务机制更完善。

​	重点方法：

- <T> Future<T> submit(Callable<T> task)，该方法获得线程返回值

  即如何通过线程池构造带返回值的线程，也是通过FurtureTask，在构造参数里指定一个callable对象，

  然后作为线程的任务

##### ScheduledExecutorService 

​	支持Future和定期执行任务。

### ThreadPoolExecutor详解

​	ThreadPoolExecutor继承自AbstractExecutorService，AbstractExecutorService实现自ExecutorService。

##### 构造函数参数

- corePoolSize：核心线程数量，可以理解为长期驻留的线程数目。

  ​	在不同种类的ThreadPool中会有很大的区别，如在Executors.newFixedThreadPool(int nThreads)中，返回的是一个corePooSize = maximumPoolSize = nThreads的线程池，而在Executors.newCachedThreadPool( )方法中，返回的是一个corePooSize = 0，maximumPoolSize = Integer.MAX_VALUE的线程池。

- maximumPoolSize：线程池中最大线程数量，该值同样在不同的线程池中具体的值会有很大的区别。具体见上述内容。

- workQueue：等待任务队列。不同的工作队列对优先级的分配有不同的实现。

- keepAliveTime：线程池维护线程所允许的空闲时间。

  ​	当线程池中的线程数量大于corePoolSize时，如果没有新的任务提交，核心线程外的线程不会被立即销毁，直到等待时间超过keepAliveTime才会被销毁。

- Timeunit：与keepAliveTIme配套使用，keepAliveTime参数指明等待时间的量化值，timeUnit指明量化值单位。例如keepAliveTime=1，timeUnit为TimeUnit.MINUTES，代表空闲线程的回收阀值为1分钟。

- threadFactory：创建新线程使用的工厂，默认使用的是Executors.defaultThreadFactory()，使用该工厂创建线程会使创建的线程具有相同的priority，并且是非守护线程，同时也设置了线程名称。

- handler：线程池饱和策略。当阻塞队列满了并且有新的任务提交时，就需要采取一种饱和策略来处理该任务。线程池当前提供了四种策略：

  - AbortPolicy：直接抛出异常，这是默认策略
  - CallerRunsPolicy：用调用者所在的线程来执行任务
  - DiscardOldestPolicy：丢弃队列中最靠前的任务，并执行当前任务
  - DiscardPolicy：直接丢弃任务
  - 此外，还可以通过实现RejectedExecutionHandler接口来自定义Handler

##### 新任务提交execute()执行流程

1. 如果线程池中运行的线程小于corePoolSize，则创建新线程来处理任务，即使线程池中其它线程是空闲的；

2. 如果线程池中运行的线程的数量大于corePoolSize且小于maximumPoolSize，则只有当workQueue满时才会创建新线程去处理任务；

3. 当线程池中已经运行了maximumPoolSize数量的线程，还有新任务提交时，如果workQueue未满，则将任务放入workQueue中，若满了，则通过handler策略来处理任务。

   其大致流程如下图所示：

![](assets/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%A4%84%E7%90%86%E6%96%B0%E4%BB%BB%E5%8A%A1%E6%B5%81%E7%A8%8B.png)

### 线程池的状态

- RUNNING：能接收新提交的任务，并且也能处理阻塞队列中的任务；
- SHUTDOWN：不接受新提交的任务，但还可以继续处理阻塞队列中的任务和线程池中正在进行中的任务；
- STOP：不接受提交任务，也不接受阻塞队列中的任务，仍然会执行完当前线程池中的线程；
- TIDYING：所有任务都已经终止，此时workCount(有效线程数)为0；
- TERMINATED：线程池调用terminate()方法后进入该状态，该状态是作为一个结束标志。

状态转换图如下：

![](./assets/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2%E5%9B%BE.png)

工作线程的生命周期

​	该部分主要用于帮助理解源码，图像如下：

![](./assets/工作线程额生命周期.png)

### CAS：CAS自旋volatile变量

```
CAS有三个操作数，CAS在java中是通过unsafe类的compareAndSwap (JNI, Java Native Interface) 方法实现的，该方法包含四个参数：第一个参数是要修改的对象，第二个参数是对象中要修改的变量的偏移量，第三个参数是修改之前的值，第四个参数是预想修改之后的值
内存值V，旧的预期值A，新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。
1.但是这样仍旧存在问题：ABA问题，循环时间长开销大问题，和只能保证一个共享变量的原子操作（原子的意思就是，要么做，要么不做，没有做一半）
各种乐观锁的实现中通常都会用版本戳version来对记录或对象标记，避免并发操作带来的问题，
2.循环时间开销大，因为是自旋的，不适用于竞争激烈的情形中
3.只能保证一个共享变量的原子操作，因为自旋，然后赋值一次，没有锁，
```

### AQS队列同步器 ✅

java.util.concurrent包下非常重要和基础的类，concurrent包下提供了一系列的同步操作需要的工具，包括了ReentrantLock、ReentrantReadWriteLock、ThreadPoolExecutor、CountDownLatch、LinkedBlockingQueue等等，该包下很多的工具类都直接或者间接基于AQS提供的独占锁、共享锁和等待队列实现了各自的同步需求。

**子类通过继承并实现它的aquire()和release()方法操控其状态，实际上就是AQS中信号量state的状态，state为0代表无锁，不为0代表上锁。**

这个同步器，提供了锁的实现和等待队列，方便了对锁进行操作，我们不需要自己决定 线程怎么抢占，怎么上锁，我们只需要按模版来自己改写就好

### AQS的核心思想

​	**AQS的核心思想是基于volatile int state这样的volatile变量，配合Unsafe工具对其原子性的操作来实现对当前锁状态进行修改。同步器内部依赖一个FIFO的双向队列来完成资源获取线程的排队工作**。

```
队列同步器(AbstractQueuedSynchronizer)是用来构建锁和其他同步组件的基础框架，
技术是 CAS自旋Volatile变量：
它使用了一个Volatile成员变量表示同步状态，通过CAS修改该变量的值，修改成功的线程表示获取到该锁；
Volatile就是线程共享，就是强制写入主存并发通知，告诉其他线程它们的私有copy失效了。

若没有修改成功，或者发现状态state已经是加锁状态，则通过一个Waiter对象封装线程，添加到等待队列中，并挂起等待被唤醒。

同步器是实现锁的关键，子类通过继承同步器并实现它的抽象方法来管理同步状态，利用同步器实现锁的语义。特别地，锁是面向锁使用者的，它定义了使用者与锁交互的接口，隐藏了实现细节；同步器面向的是锁的实现者，它简化了锁的实现方式，屏蔽了同步状态管理、线程排队、等待与唤醒等底层操作。

锁和同步器很好地隔离了锁的使用者与锁的实现者所需关注的领域。
🔒锁的使用者只需要加锁就好了，不用去关注怎么让线程排队，何时唤醒，怎么唤醒。怎么让CAS是原理 队列同步器相当于是一个操作类，是一个框架。

```

### AQS的其他性质✅

```
AQS是基于模版的，想自己实现锁的释放和调度，只需要继承接口，重写方法即可

AQS维护了一个volatile int state（代表共享资源）和一个FIFO线程等待队列（多线程争用资源被阻塞时会进入此队列）。这里volatile是核心关键词，具体volatile的语义，在此不述。state的访问方式有三种:getState()、setState()以及compareAndSetState()。
AQS的结构：volatile in state代表控制的资源，然后就是一个FIFO队列对请求的线程进行排序

AQS定义了两种资源共享方式：Exclusive（独占，只有一个线程能执行，如ReentrantLock）和Share（共享，多个线程可同时执行，如Semaphore/CountDownLatch）。
不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。自定义同步器实现时主要实现以下几种方法：

1.isHeldExclusively()：该线程是否正在独占资源。只有用到condition才需要去实现它；

2.tryAcquire(int)：独占方式。尝试获取资源，成功则返回true，失败则返回false；

3.tryRelease(int)：独占方式。尝试释放资源，成功则返回true，失败则返回false；

4.tryAcquireShared(int)：共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源；

5.tryReleaseShared(int)：共享方式。尝试释放资源，成功则返回true，失败则返回false。
```

![8E5391EA-770C-4924-8524-84792CDFE066](/Users/Haoyu/Documents/8E5391EA-770C-4924-8524-84792CDFE066.png)

### AQS加锁解锁总结

总结一下吧。

在并发环境下，加锁和解锁需要以下三个部件的协调：

1. **锁状态state。我们要知道锁是不是被别的线程占有了，这个就是 state 的作用，它为 0 的时候代表没有线程占有锁，可以去争抢这个锁，用 CAS 将 state 设为 1，如果 CAS 成功，说明抢到了锁，这样其他线程就抢不到了，如果锁重入的话，state进行+1 就可以，解锁就是减 1，直到 state 又变为 0，代表释放锁，所以 lock() 和 unlock() 必须要配对。然后唤醒等待队列中的第一个线程，让其来占有锁。**
2. 线程的阻塞和解除阻塞。AQS 中采用了 LockSupport.park(thread) 来挂起线程，用 unpark 来唤醒线程。
3. 阻塞队列。因为争抢锁的线程可能很多，但是只能有一个线程拿到锁，其他的线程都必须等待，这个时候就需要一个 queue 来管理这些线程，**AQS 用的是一个 FIFO 的队列**，就是一个链表，每个 node 都持有后继节点的引用。AQS 采用了 CLH 锁的变体来实现，感兴趣的读者可以参考这篇文章[关于CLH的介绍](http://coderbee.net/index.php/concurrent/20131115/577)，写得简单明了。





# 单例模式与多线程

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

