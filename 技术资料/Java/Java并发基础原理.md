# 多线程与并发

> 书单 
>
> 《Java的并发编程艺术》

多线程与并发的学习，首先是

##### 补充：关于并发和并行

解释一：并行是指两个或者多个事件在同一时刻发生；而并发是指两个或多个事件在同一时间间隔发生。
解释二：并行是在不同实体上的多个事件，并发是在同一实体上的多个事件。
解释三：在一台处理器上“同时”处理多个任务，在多台处理器上同时处理多个任务。如hadoop分布式集群

支持并发：同一时间，可以有多个任务发生，但是同一时间只能执行一个，并且可以交替执行

并发：同一时间间隔 同一实体 多个事件的发生，并行同一时刻，不同实体，多个事件的发生

也就是涉及到同步和互斥，有资源的共享

支持并行：两个或者多个时间在同一个时刻发生，可以互相独立执行互不影响，也就是说，没有资源的抢占，可以并行不悖，但是这个在单核CPU是不行的，因为就算其他的不强，CPU计算资源也是要抢的，多核CPU可以并行，没有数据共享（同步问题）或者资源互斥的话可以实现并行，否则还需要并发控制。

进程间可以进行通信，通过子父进程的匿名管道，命名管道，或者信箱或者消息队列之类的，数据共享区也是可以的，但是需要进行并发控制。



##### 并发基础和多线程

首先需要学习的就是并发的基础知识，什么是并发，为什么要并发，多线程的概念，线程安全的概念等。

然后学会使用Java中的Thread或是其他线程实现方法，了解线程的状态转换，线程的方法，线程的通信方式等。

##### JMM内存模型

任何语言最终都是运行在处理器上，JVM虚拟机为了给开发者一个一致的编程内存模型，需要制定一套规则，这套规则可以在不同架构的机器上有不同实现，并且向上为程序员提供统一的JMM内存模型。

> 看来JVM不仅仅由classLoader，runtime data area execution engine 和 native interface四部分组成，
>
> 其中runtime data area 是主要的内存模型，这里JMM又是JVM给开发者提供的一个一致的编程内存模型
>
> 编程内存模型，就是一套规则，说白了

所以了解JMM内存模型也是了解Java并发原理的一个重点，其中**了解指令重排，内存屏障，以及可见性原理**尤为重要。

这三点正是我迫切想要了解的

JMM只保证happens-before和as-if-serial规则，所以在多线程并发时，可能出现原子性，可见性以及有序性这三大问题。

##### Synchronized,volatile,final等JVM层面的关键字

- 对于并发的三大问题，volatile可以保证原子性和可见性，
- synchronized三种特性都可以保证（允许指令重排）。

**原理层面**synchronized是基于操作系统的mutex lock指令实现的，volatile和final则是根据JMM实现其内存语义。
此处还要了解CAS操作，它不仅提供了类似volatile的内存语义，并且保证操作原子性，因为它是由硬件实现的。

寄存器指令。

JUC中的Lock底层就是使用volatile加上CAS的方式实现的。synchronized也会尝试用cas操作来优化器重量级锁。

##### JUC包

JUC提供了包括Lock，原子操作类，线程池，同步容器，工具类等内容。
这些类的基础都是AQS，所以了解AQS的原理是很重要的。

除此之外，还可以了解一下Fork/Join，以及JUC的常用场景，比如生产者消费者，阻塞队列，以及读写容器等。

### Java中的并发

### 内存模型的相关概念

其实并发的问题最早在CPU设计的时候就出现了
对于多个线程，缓存内有共享变量，CPU的写操作也会导致不一致性出现，比如脏读
因此，当时对CPU的解决办法就是

1. 加锁，像java一样加锁，访问的时候其他线程都阻塞或者循环获取，然后就是锁优化技术，锁粗化，自旋锁，锁升级等等。然后就是，通过在总线加锁的方式
2. 缓存一致协议，最出名的比如Intel的MESI协议

在多线程编程中，线程安全问题是一个最为关键的问题，其核心概念就在于正确性，即当多个线程访问某一共享、可变数据时，始终都不会导致数据结构的破坏和其他Exception。

```
所有的并发模式在解决这个问题的时候（原来的生产者消费者模型不知道有什么关系，还有防止死锁的知识）
采用的方案都是序列化访问临界资源
在 Java 中，提供了两种方式来实现同步互斥访问：synchronized 和 Lock。本文针对 synchronized 内置锁 详细讨论了其在 Java 并发 中的应用，包括它的具体使用场景（同步方法、同步代码块、实例对象锁 
```

### 线程五种状态 ✅

##### 创建状态

new方法进行创建线程 系统分配给线程资源，线程

##### 就绪状态

start调用后 进程进入就绪状态，但是不一定会立即执行run方法，还要等待CPU的调度

##### 执行状态

CPU开始调度执行该线程，然后线程开始执行run方法

runnable状态

##### 阻塞状态

线程的执行状态中由于一些原因进入阻塞状态，比如

调用sleep方法，或者尝试去获取一个锁

在Java程序中，还分Timed-Waiting，Timed-Waiting又有（sleeping）（parking）（onobjectmonitor）几种状态

和Waiting两种状态

##### 死亡状态

run方法执行完，或者执行过程中遇到了一个异常



非阻塞实现方式，一般都是指的乐观锁。

阻塞实现方式，因为获取锁的过程中，会使其他想要获取锁的阻塞，并且自身在获取锁的时候也会阻塞。

### 线程安全问题 ✅



在单线程中不会出现线程安全问题，而在多线程编程中，有可能会出现同时访问同一个 共享、可变资源 的情况，这种资源可以是：一个变量、一个对象、一个文件等。特别注意两点，

- 共享：不同线程都可访问，且可以同时访问
- 可变：该资源在生命周期内可以被修改
  由于每个线程执行的过程是不可控的，所以需要采用同步机制来协同对对象可变状态的访问。

就算已经实现了同步操作，但是开多个线程还是有可能出脏读的问题

不过，当多个线程执行一个方法时，该方法内部的局部变量并不是临界资源，因为这些局部变量是在每个线程的私有栈中，因此不具有共享性，不会导致线程安全问题。

**解决思想** 什么叫序列化访问临界资源：即在同一时刻，只能有一个线程访问临界资源，也称作 同步互斥访问。

##### 线程不安全问题的由来

![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/java%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.png)

**这样也就引出了线程操作中变量一致性的问题**：堆中某个对象的共享变量值为1，线程A和线程B在读取的时候都拷贝了一个副本，A线程在将本地副本中的1进行+1操作修改为2后，将其重新写会堆中，但此时B线程也进行了同样的操作，其写回主存中的变量值也为2，这就与预期的3发生了不一致情况。

**因为A写回去后，没有立即通知B，告知B的私有变量拷贝失效**

//其实就是变量 共享，可变 两个问题，需要互斥同步访问

##### 如何确保线程安全？

在Java中有很多方法来保证线程安全 如

1. 通过加锁（Lock/Synchronized）保证对临界资源的互斥访问
2. 使用volatile关键字，轻量级同步机制，但不保证原子性？？
3. 使用不变类和线程安全类（原子类，并发容器（比如HashTable），同步容器）等

CPU层面的 还有缓存一致协议，感觉就是volatile可见性

#####  

##### 何为线程安全的类  ✅

在线程安全性的定义中，最核心的概念就是 正确性。当多个线程访问某个类时，
不管运行时环境采用何种调度方式或者这些线程将如何交替执行，//无论
并且在主调代码中不需要任何额外的同步或协同，//不需要任何同步或者协同
这个类都能表现出正确的行为，那么这个类就是线程安全的。

##### 如何停止一个线程

1. 使用volatile变量终止正常运行的线程 + 抛异常法/Return法

   ```java
   //正常线程run结束就会退出线程，但是有可能如监听等情况 会采用一个循环 不断jian ting
   ```

   

2. 组合使用interrupt方法与interruptted/isinterrupted方法终止正在运行的线程 + 抛异常法/Return法

   

3. 使用interrupt方法终止 正在阻塞中的 线程 阻塞就是  wait();

### 线程之间的协作

##### 线程通信方法 ✅

wait(); notify();notifyAll();定义在Object类中 
Wait-notify机制是在获取对象锁的前提下不同线程间的通信机制。在Java中，任意对象都可以当作锁来使用，由于锁对象的任意性，所以这些通信方法需要被定义在Object类里。

wait()

```java
//wait有三种方式的调用
wait()
//必要要由 notify 或者 notifyAll 来唤醒​​​​
wait(long timeout)
//在指定时间内，如果没有notify或notifAll方法的唤醒，也会自动唤醒。
wait(long timeout,long nanos)
//本质上还是调用一个参数的方法
public final void wait(long timeout, int nanos) throws InterruptedException {
      if (timeout < 0) {
             throw new IllegalArgumentException("timeout value is negative");
       }
      if (nanos < 0 || nanos > 999999) {
              throw new IllegalArgumentException(
             "nanosecond timeout value out of range");
       }
       if (nanos > 0) {
             timeout++;
       }
       wait(timeout);
}
//所以 nanos是个啥？好像是毫秒或者纳秒？小数单位？

```

- notify
  只能唤醒一个处于 wait 的线程
- notifyAll
  唤醒全部处于 wait 的线程
  ​

### 并发三准则 ✅

```
1.异常不会导致死锁现象：当线程出现异常且没有捕获处理时，JVM会自动释放当前线程占用的锁，因此不会由于异常导致出现死锁现象，同时还会释放CPU；
2.锁的是对象而非其引用
3.有Wait必须有Notify
```

### 并发编程中的三个性质

1. 原子性：即一个操作或者多个操作，要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行
2. 可见性：多个线程访问同一个变量，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值
3. 有序性：即程序执行的顺序按照代码的先后顺序执行

这个问题的产生是，编译器会对代码进行优化，一些数据之间没有相互依赖关系的语句可能会被重排序，但是在单线程环境下，重排序后的结果一定是和原执行语句一致，是不变的。

但是在多线程环境下这一点就很难被保证了，可能会影响结果的正确性。

#####  

##### 原子性

Java内存模型（JMM）只能保证基本的读取和复制是原子性操作，如果要实现更大范围操作的原子性，就需要lock和synchronized来实现

##### 可见性

可见性，java提供了volatile关键字来保证可见性，当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到贮存，当有其他线程需要读取的时候，它会去内存中读取新的值，而普通共享变量被修改后，什么时候写入主存是不确定的，当其他线程去读取时，此时内存中可能还是原来的旧值，因此无法保证可见性。

而加了volatile就不一样了
首先它会在线程更改后，立刻将更改写入主存
另外它会使其他线程对同一变量的缓存无效，因此需要其他线程重新去主存获取这个变量。

CAS 为什么叫自旋volatile变量，因此自选就是一直循环尝试写，循环判断内存中的值和线程中的copy值是否一致。 所以说CAS是一种乐观并发策略。

##### 有序性

另外 java内存模型 具备一些先天的有序性，即不需要任何手段就能够得到保证的有序性，
这个也称为happens-beforce原则，如果两个操作的次序，无法从happens-beforce中推导出来，那么他们就不能保证它们的有序性。

##### 补充： 指令重排序的例子

# Java并发机制的底层实现原理

### 5.19的读书笔记

volatile的关键字的应用

首先是定义，volatile关键字在JVM中相当于小的synchronized，

首先讲了 CPU是怎么获取数据的，缓存以及内存，并且讲了几个常见的缓存概念，比如 缓存行（是缓存的最小单元），缓存行填充，缓存命中以及写命中，写缺失等寄存器概念

但是为了提高处理速度，CPU是不会直接和内存通信的

volatile在汇编上的语义实现（主要是生成的汇编语句后，会多一句lock指令）

lock指令的作用

一是可见性，解释了可见性的由来，就是缓存什么时候会写到内存里的问题，缓存中的值并不会立即写入内存中

二是缓存中其他指向同一内存地址的引用失效

接下来又讨论了volatile的使用优化，比如采用补足法，追加字节的性能优化，将一个volatile拆成两部分，存在两个缓存行里，并用Queue来管理读写，进而 读写分离，删除和增加在两个缓冲行里进行，这样避免互相锁定。

然后在Synchronized原理这部分，讲了synchronized的底层原理，是生成相关汇编语句后，会生成两个monitorenter，monitorexit指令，进出monitor对象，或者monitor对象是如何访问互斥的。

并且提到了synchronized的使用，对代码块，对类成员方法，对静态方法的修饰

这里还要注意一点，就是静态内部类的使用，静态内部类是当前比较流行的线程安全的lazy初始化方式，比双检初始化还要好些

按么synchronized在1.6后又开始区分偏向锁，轻量级锁，重量级锁等概念

本文首先讨论了锁机制在CPU层面上的实现，比如总线锁，或者说缓存行锁，进而为了提高读写速率，

然后引入介绍了java对象头的概念，对象头应该是在装载的时候就生成的，放在方法区内，

首先是偏向锁的升级，一个对象头由25bit的hashcode也就是地址和4bit的对象分代年龄和1bit的偏向锁标志位，

2bit的锁状态标记位构成

compareAndSet操作 CAS本身就是一个编译指令，它可以保证一个变量的原子操作，所以在尝试获取monitor所有权，也就是说尝试获取锁的时候，常用CAS操作。所以在synchronized中也多有使用

接下来本文又讲了两点，一是synchronized的锁转换，

首先我们可以在JVM -Usebiasedlock在虚拟机中设置是否使用偏向锁

如果使用，我们会检查，当前线程的虚拟机栈帧是否有创建的锁记录，如果没有则申请空间，并用CAS将对象头markword中的数据复制到线程栈帧的锁记录中，并将对象头中的数据改为指向栈帧的引用

偏向锁是当前不存在竞争的时候，就把对象头中的线程id复制到自己的栈帧中，这样就不需要CAS的方式获取了，只需要检查一下对象头是否存储着指向当前线程的偏向锁，即线程地址

如果有另一个线程尝试把对象头中的线程地址转换成自己的，则说明有竞争，偏向锁不合适，中止，地址清零，改用轻量级锁（即偏向锁的撤销）

偏向锁的存在就是为不存在竞争环境下的对象，提供方便的，不需要每次都以cas的方式获取锁

和无锁轻量级锁

接下来就是另一个体系，无状态锁，轻量级锁，和重量级锁

当然对象头不只有markword ，普通对象还有指向数据类型的指针，数组对象还有数组长度

如果有一个线程多次尝试获取对象头的锁，

轻量级锁，是已经有竞争的情况下的，所以 还是会在虚拟机栈帧中开辟一片空间，称为displaced mark word 尝试将markword 和 该锁记录的引用（一个指针）进行交换，并把锁状态设为00

另一个线程会用CAS的方式自旋获取锁

当自旋超过一定次数还没有获取到的时候，CAS获取失败，锁就会膨胀为重量级锁，markword变为指向重量级锁的指针，其他线程都会被阻塞，等待锁被释放。

这一章还讲到了，原子操作的实现

即原子操作在cpu层面上的实现，

以及CAS是如何实现原子操作的，并且有哪些问题（ABA，CPU资源消耗，以及只能保证单一变量原子性的问题）



当然这里还有一点小细节没有看到，就是 偏向锁什么时候撤销，一般获取偏向锁后，没有其他锁竞争是不会主动撤销偏向锁的



# Volatile关键字

### 定义

volatile 保证了可见性，底层实现CAS，其实就是个乐观锁，但是不能保证原子性，因为只是针对一个变量的操作。怎么说，是个轻量级的synchronized变量吧，不会引起线程切换和调度，执行开销更小。

**可见性**，java提供了volatile关键字来保证可见性，当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到贮存，当有其他线程需要读取的时候，它会去内存中读取新的值，而普通共享变量被修改后，什么时候写入主存是不确定的，当其他线程去读取时，此时内存中可能还是原来的旧值，因此无法保证可见性。

而加了volatile就不一样了
首先它会在线程更改后，立刻将更改写入主存
另外它会使其他线程存在缓存中的，同一内存地址对同一变量的缓存无效，因此需要其他线程重新去主存获取这个变量。

### 原理

1. 使用volitale修饰的变量在汇编阶段，会多出一条lock前缀指令 

2. 它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成

   **JMM内存模型 需要了解一下**

3. 它会强制将对缓存的修改操作立即写入主存

4. 如果是写操作，它会导致其他CPU(寄存器)里缓存了该内存地址的数据无效

##### 补充：Volatile关键字在Java中的作用 

什么叫指令重排序？
volatile的特殊规则保证了新值能立即同步到主内存，以及每次使用前立即从主内存刷新，保证了内存的可见性
也禁止指令重排序，此外，synchronized关键字也可以保证内存可见性

指令重排序问题在并发环境下会导致线程安全问题，volatile关键字通过禁止指令重排序来避免这一问题。而对于Synchronized关键字，其所控制范围内的程序在执行时独占的，指令重排序问题不会对其产生任何影响，因此无论如何，其都可以保证最终的正确性。



##### 补充：volatile原子性的理解

volatile的原子性，只能保证到对耽搁volatile的读写的原子性，因为读和写（赋值）操作本身就是原子性的，再加上volatile的可见性，因此可以保证多线程环境下的安全，但是有个问题就是对于volatile的自增操作，如

volatile++是不具有原子性的，因为

**大家是不是有这样的疑问**：“线程1在读取inc为10后被阻塞了，没有进行修改所以不会去通知其他线程，此时线程2拿到的还是10，这点可以理解。但是后来线程2修改了inc变成11后写回主内存，这下是修改了，线程1再次运行时，难道不会去主存中获取最新的值吗？按照volatile的定义，如果volatile修饰的变量发生了变化，其他线程应该去主存中拿变化后的值才对啊？”

是的，线程2应该再重新拿值才对啊

即使加了volatile关键字，多个线程去修改这个volatile变量时，还是可能会出现非预期值。因为修改变量可分为几个步骤（1.读取值到寄存器1，2.cpu高速缓存修改为新值，新值保存到寄存器2，3.写入寄存器2的值到主存），线程1走完了步骤2还未执行步骤三时，即使此时有线程2修改了volatile变量的值，也只是会同步寄存器1的值不会同步寄存器2的值，所以线程继续将寄存器2的值写入主存，导致两次对volatile变量的操作不是叠加而是覆盖。

**网上的资料，真的参差不齐，还是读书好，真的，吃源头知识！**

volatile 只能保证 “可见性”，不能保证 “原子性”。count++; 这条语句由3条指令组成： 
（1）将 count 的值从内存加载到 cpu 的某个寄存器r 
（2）将 寄存器r 的值 +1，结果存放在 寄存器s 
（3）将 寄存器s 中的值写回内存 
所以，如果有多个线程同时在执行 count++;，在某个线程执行完第（3）步之前，其它线程是看不到它的执行结果的。 
在没有 volatile 的时候，执行完 count++;，执行结果其实是写到CPU缓存中，没有马上写回到内存中，后续在某些情况下（比如CPU缓存不够用）再将CPU缓存中的值flush到内存。正因为没有马上写到内存，所以不能保证其它线程可以及时见到执行的结果。 
在有 volatile 的时候，执行完 count++;，执行结果写到CPU缓存中，并且同时写回到内存，因为已经写回内存了，所以可以保证其它线程马上看到执行的结果。但是，volatile 并没有保证原子性，在某个线程执行（1）（2）（3）的时候，volatile 并没有锁定 count 的值，也就是并不能阻塞其他线程也执行（1）（2）（3）。可能有两个线程同时执行（1），所以（2）计算出来一样的结果，然后（3）存回的也是同一个值。 

# CAS 

### 什么是和为什么出现

CAS与volatile没什么关系，不是一种东西，底层实现机制不一样。

volatile也是到汇编层次，CAS也是。

CAS是一种乐观锁，为了解决synchronzied的非可中断这种互斥锁性能，效率低而出现的。

CAS有三个操作数，CAS在java中是通过unsafe类的compareAndSwap (JNI, Java Native Interface) 方法实现的，该方法包含四个参数：第一个参数是要修改的对象，第二个参数是对象中要修改的变量的偏移量，第三个参数是修改之前的值，第四个参数是预想修改之后的值
内存值V，旧的预期值A，新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。

### 实现原理

CAS在java中是通过unsafe类的compareAndSwap (JNI, Java Native Interface) 方法实现的，这个方法有四个参数，一个是要修改的对象，第二个是内存值V，第三个是修改前的值A，新的值B V==A 则 V=B 其实有点像，数据库乐观锁的版本号，一个version int(3) 属性，如果版本号等于预期，则修改并把版本号加1

### CAS的问题

1. 但是这样仍旧存在问题：ABA问题，循环时间长开销大问题，和只能保证一个共享变量的原子操作（原子的意思就是，要么做，要么不做，没有做一半）
   各种乐观锁的实现中通常都会用版本戳version来对记录或对象标记，避免并发操作带来的问题，

   当然ABA可以用version 版本戳来对记录或对象标记，所以现在会检查，预期引用和当前引用，预期标记和当前标记，如果都相同，那么就会替换。
   
   不再单单是值比较了
   
2. 循环时间开销大，因为是自旋的，不适用于竞争激烈的情形中

3. 只能保证一个共享变量的原子操作，因为自旋，然后赋值一次，没有锁，

   只能保证一个共享变量的原子操作。当然还有一种取巧的办法，



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

     ![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%81.png)

  2. 拷贝对象头中的Mark Word复制到锁记录中。

  3. 拷贝成功后，虚拟机将使用**CAS操作**尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock record里的owner指针指向object mark word。如果更新成功，则执行步骤（4），否则执行步骤（5）。

  4. 如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，即表示此对象处于轻量级锁定状态，这时候线程堆栈与对象头的状态如下图2.2所示。

     ![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%812.png)

  5. 如果这个更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行。否则说明多个线程竞争锁，轻量级锁就要膨胀为重量级锁，锁标志的状态值变为“10”，Mark Word中存储的就是指向重量级锁（互斥量）的指针，后面等待锁的线程也要进入阻塞状态。 而当前线程便尝试使用自旋来获取锁，自旋就是为了不让线程阻塞，而采用循环去获取锁的过程。

     

##### 轻量级锁的解锁过程

虚拟机栈中锁记录被替换

1. 通过CAS操作尝试把线程中复制的Displaced Mark Word对象替换当前的Mark Word。
2. 如果替换成功，整个同步过程就完成了。
3. 如果替换失败，说明有其他线程尝试过获取该锁（此时锁已膨胀），那就要在释放锁的同时，唤醒被挂起的线程。

##### 偏向、轻量级、重量级锁转换

![](/Users/Haoyu/Documents/Notes-For-Interviews/%E6%8A%80%E6%9C%AF%E8%B5%84%E6%96%99/assets/%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%81%E3%80%81%E9%87%8D%E9%87%8F%E7%BA%A7%E9%94%81%E5%92%8C%E5%81%8F%E5%90%91%E9%94%81%E7%9A%84%E8%BD%AC%E6%8D%A2.png)

# 原子操作



# AQS队列同步器 ✅

AQS抽象的队列式同步器，是一种基于状态（state）的链表管理方式

java.util.concurrent包下非常重要和基础的类，concurrent包下提供了一系列的同步操作需要的工具，包括了ReentrantLock、ReentrantReadWriteLock、ThreadPoolExecutor、CountDownLatch、LinkedBlockingQueue等等，该包下很多的工具类都直接或者间接基于AQS提供的独占锁、共享锁和等待队列实现了各自的同步需求。

**子类通过继承并实现它的aquire()和release()方法操控其状态，实际上就是AQS中信号量state的状态，state为0代表无锁，不为0代表上锁。**

这个同步器，提供了锁的实现和等待队列，方便了对锁进行操作，我们不需要自己决定 线程怎么抢占，怎么上锁，我们只需要按模版来自己改写就好

### AQS的核心思想

​	AQS的核心思想是基于volatile int state这样的volatile变量，配合Unsafe工具对其原子性的操作来实现对当前锁状态进行修改。同步器内部依赖一个FIFO的双向队列来完成资源获取线程的排队工作

​	state是用volatile 配 CAS进行修改的

​	 private volatile int state;是的 state就是volatile变量

队列同步器(AbstractQueuedSynchronizer)是用来构建锁和其他同步组件的基础框架，
技术是 CAS自旋Volatile变量：
它使用了一个Volatile成员变量表示同步状态，通过CAS修改该变量的值，修改成功的线程表示获取到该锁；

若没有修改成功，或者发现状态state已经是加锁状态，则通过一个Waiter对象封装线程，添加到等待队列中，并挂起等待被唤醒。

同步器是实现锁的关键，子类通过继承同步器并实现它的抽象方法来管理同步状态，利用同步器实现锁的语义。特别地，锁是面向锁使用者的，它定义了使用者与锁交互的接口，隐藏了实现细节；同步器面向的是锁的实现者，它简化了锁的实现方式，屏蔽了同步状态管理、线程排队、等待与唤醒等底层操作。

锁和同步器很好地隔离了锁的使用者与锁的实现者所需关注的领域。
🔒锁的使用者只需要加锁就好了，不用去关注怎么让线程排队，何时唤醒，怎么唤醒。怎么让CAS是原理 队列同步器相当于是一个操作类，是一个框架。

### AQS的其他性质✅

AQS是基于模版的，想自己实现锁的释放和调度，只需要继承接口，重写方法即可

AQS维护了一个volatile int state（代表共享资源）和一个FIFO线程等待队列（多线程争用资源被阻塞时会进入此队列）。这里volatile是核心关键词，具体volatile的语义，在此不述。state的访问方式有三种:getState()、setState()以及compareAndSetState()。

AQS的结构：volatile in state代表控制的资源，然后就是一个FIFO队列对请求的线程进行排序

AQS定义了两种资源共享方式：

Exclusive(独占，只有一个线程能执行，如ReentrantLock）

和

Share（共享，多个线程可同时执行，如Semaphore/CountDownLatch）。

##### **Semaphore 是互斥信号量**

semaphore也是两个主要方法，一个是acquire() 一个是release() 正好和countDownLatch的作用相反，acquire代表获取到可用的Semaphore，如果达到指定上限则其他获取 就会阻塞其他获取请求

```java
Semaphores are often used to restrict the number of threads than can
* access some (physical or logical) resource. For example, here is
* a class that uses a semaphore to control access to a pool of items:
class Pool {
 *   private static final int MAX_AVAILABLE = 100;
 *   private final Semaphore available = new Semaphore(MAX_AVAILABLE, true);
 *
 *   public Object getItem() throws InterruptedException {
 *     available.acquire();
 *     return getNextAvailableItem();
 *   }
 *
 *   public void putItem(Object x) {
 *     if (markAsUnused(x))
 *       available.release();
 *   }
 *
 *   // Not a particularly efficient data structure; just for demo
 *
 *   protected Object[] items = ... whatever kinds of items being managed
 *   protected boolean[] used = new boolean[MAX_AVAILABLE];
 *
 *   protected synchronized Object getNextAvailableItem() {
 *     for (int i = 0; i < MAX_AVAILABLE; ++i) {
 *       if (!used[i]) {
 *          used[i] = true;
 *          return items[i];
 *       }
 *     }
 *     return null; // not reached
 *   }
 *
 *   protected synchronized boolean markAsUnused(Object item) {
 *     for (int i = 0; i < MAX_AVAILABLE; ++i) {
 *       if (item == items[i]) {
 *          if (used[i]) {
 *            used[i] = false;
 *            return true;
 *          } else
 *            return false;
 *       }
 *     }
 *     return false;
 *   }
 * }}</pre>
```

Semaphore是控制最大访问线程数的信号量，比如低于0，就会阻塞，直到再生产出来

Semaphore恰恰相反

##### **CountDownLatch是同步信号量**

其实countDown就两个方法，一个是countDown 信号量减一，代表执行一次

一个是await()代表等待消息，一旦检测到countDown变为0了 就开始执行后面的代码

```java
<p>A {@code CountDownLatch} is a versatile synchronization tool
* and can be used for a number of purposes.  A
* {@code CountDownLatch} initialized with a count of one serves as a
* simple on/off latch, or gate: all threads invoking {@link #await await}
* wait at the gate until it is opened by a thread invoking {@link
* #countDown}.  A {@code CountDownLatch} initialized to <em>N</em>
* can be used to make one thread wait until <em>N</em> threads have
* completed some action, or some action has been completed N times.
  	class Driver { // ...
 *   void main() throws InterruptedException {
 *     CountDownLatch startSignal = new CountDownLatch(1);
 *     CountDownLatch doneSignal = new CountDownLatch(N);
 *
 *     for (int i = 0; i < N; ++i) // create and start threads
 *       new Thread(new Worker(startSignal, doneSignal)).start();
 *
 *     doSomethingElse();            // don't let run yet
 *     startSignal.countDown();      // let all threads proceed
 *     doSomethingElse();
 *     doneSignal.await();           // wait for all to finish
 *   }
 * }
 *
 * class Worker implements Runnable {
 *   private final CountDownLatch startSignal;
 *   private final CountDownLatch doneSignal;
 *   Worker(CountDownLatch startSignal, CountDownLatch doneSignal) {
 *     this.startSignal = startSignal;
 *     this.doneSignal = doneSignal;
 *   }
 *   public void run() {
 *     try {
 *       startSignal.await();
 *       doWork();
 *       doneSignal.countDown();
 *     } catch (InterruptedException ex) {} // return;
 *   }
 *
 *   void doWork() { ... }
 * }}</pre>
 *
 
```

意思是，CountDownLatch 直到数量为0才会执行，和Semaphore正好相反，虽然都是同步信号量

##### 互斥锁 Condition 同步信号量

另外condition也是同步信号量，就是简化版本的Semaphore吧 可用数量为1 上限为1那种Semaphore 主要是做 信息传递用，condition和互斥锁绑定使用，可以更精确的控制线程何时挂起 何时执行

##### 补充：CyclicBarrier和CountdownLatch

CountdownLatch其实很好立即，其实就相当于一个计数器，没执行一次调用countdown一下 总次数减一，用await方法等待信号，减到0后，await方法后的部分才会执行

CyclicBarrier

回环栅栏

这个也很好理解，就是让某一组线程都等待至某个状态后再全部同时执行，叫做回环是因为当所有等待线程被释放后，CyclicBarrier可以重用

就是等待所有线程都到Barrier状态，并且达到barrier状态后 barrier还实现了run方法，会从N个线程中选一个出来执行，上一轮达到barrier状态后，可以重用

```java
public class Test {
    public static void main(String[] args) {
        int N = 4;
        CyclicBarrier barrier  = new CyclicBarrier(N,new Runnable() {
            @Override
            public void run() {
                System.out.println("当前线程"+Thread.currentThread().getName());   
            }
        });
         
        for(int i=0;i<N;i++)
            new Writer(barrier).start();
    }
    static class Writer extends Thread{
        private CyclicBarrier cyclicBarrier;
        public Writer(CyclicBarrier cyclicBarrier) {
            this.cyclicBarrier = cyclicBarrier;
        }
 
        @Override
        public void run() {
            System.out.println("线程"+Thread.currentThread().getName()+"正在写入数据...");
            try {
                Thread.sleep(5000);      //以睡眠来模拟写入数据操作
                System.out.println("线程"+Thread.currentThread().getName()+"写入数据完毕，等待其他线程写入完毕");
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }catch(BrokenBarrierException e){
                e.printStackTrace();
            }
            System.out.println("所有线程写入完毕，继续处理其他任务...");
        }
    }
}
```





不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。自定义同步器实现时主要实现以下几种方法：

1. isHeldExclusively()：该线程是否正在独占资源。只有用到condition才需要去实现它；
2. tryAcquire(int)：独占方式。尝试获取资源，成功则返回true，失败则返回false；
3. tryRelease(int)：独占方式。尝试释放资源，成功则返回true，失败则返回false；
4. tryAcquireShared(int)：共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源；
5. tryReleaseShared(int)：共享方式。尝试释放资源，成功则返回true，失败则返回false。



AQS可以有 共享式写法，也可以有互斥式写法。

![8E5391EA-770C-4924-8524-84792CDFE066](/Users/Haoyu/Documents/8E5391EA-770C-4924-8524-84792CDFE066.png)

### AQS加锁解锁总结

总结一下吧。

在并发环境下，加锁和解锁需要以下三个部件的协调：

1. **锁状态state。我们要知道锁是不是被别的线程占有了，这个就是 state 的作用，它为 0 的时候代表没有线程占有锁，可以去争抢这个锁，用 CAS 将 state 设为 1，如果 CAS 成功，说明抢到了锁，这样其他线程就抢不到了，如果锁重入的话，state进行+1 就可以，解锁就是减 1，直到 state 又变为 0，代表释放锁，所以 lock() 和 unlock() 必须要配对。然后唤醒等待队列中的第一个线程，让其来占有锁。**
2. 线程的阻塞和解除阻塞。AQS 中采用了 LockSupport.park(thread) 来挂起线程，用 unpark 来唤醒线程。
3. 阻塞队列。因为争抢锁的线程可能很多，但是只能有一个线程拿到锁，其他的线程都必须等待，这个时候就需要一个 queue 来管理这些线程，**AQS 用的是一个 FIFO 的队列**，就是一个链表，每个 node 都持有后继节点的引用。AQS 采用了 CLH 锁的变体来实现，感兴趣的读者可以参考这篇文章[关于CLH的介绍](http://coderbee.net/index.php/concurrent/20131115/577)，写得简单明了。

